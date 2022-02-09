# SSH

## Keys
create a keypair with keygen. location `/home/username/.ssh`. It creates a private + public key  
```console
root@machine:~$ ssh-keygen
```
if there isn't a .ssh directory, you need to create one with permission `chmod 700`. This directory needs a `authorized_keys` file with permission `chmod 600` and contains the public key.
```console
root@machine:~$ mkdir .ssh
root@machine:~$ chmod 700 .ssh
root@machine:~$ cat id_rsa.pub >> authorized keys
root@machine:~$ chmod 600 authorized_keys
```

## Basic Usage

```console
root@machine:~$ ssh -i id_rsa user@<ip adress>

Options:
    -i  private key
```
`id_rsa` private key file needs permission `chmod 600` to read it.

## SCP
Copy file via ssh to target
```console
root@machine:~$ scp <local file> user@<IP target>:<directory target>
```
You may need to enter the password.

## Bruteforce SSH

[ssh2john](https://null-byte.wonderhowto.com/how-to/crack-ssh-private-key-passwords-with-john-ripper-0302810/)
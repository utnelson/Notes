# SSH

## Keys
create a keypair with keygen. Standard location `~/home/username/.ssh`  
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
root@machine:~$ ssh -i id_rsa cactus@10.10.234.125

Options:
    -i  private key
```
`id_rsa` private key file needs permission `chmod 600`
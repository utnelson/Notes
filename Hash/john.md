# John

## gpg Hash

```console
get the hash

root@machine:~$ gpg2john tryhackme.asc > hash
```

```console
crack the hash with wordlist

root@machine:~$ john --wordlist=/usr/share/wordlists/rockyou.txt hash 
```
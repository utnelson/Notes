# gpg

## decrypt file

```console
import key | you need the passphrase > look at john to crack the hash

root@machine:~$ gpg --import tryhackme.asc 
```

```console
decrypt to plaintext

root@machine:~$ gpg --output ./plaintext.txt --decrypt ./credential.pgp 
```
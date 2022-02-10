# gpg

## decrypt file

```console
import key

root@machine:~$ gpg --import tryhackme.asc 
```

```console
decrypt to plaintext

root@machine:~$ gpg --output ./plaintext.txt --decrypt ./credential.pgp 
```
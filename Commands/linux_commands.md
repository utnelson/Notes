# Usefull Commands

## Processes

```console
show processes

$ ps aux
```

```console
show start command by PID

$ ps -e {PID} -o args
```


```console
show start command with grep

$ ps aux | grep <name>
```
---
## Cat
```console
cat all with keyword "test" and 20 lines bevor and after

$ cat * | grep -A 20 -B 20 "test"
```
---
## Sort
```console
sort file and only uniq words + count

$ sort <file> | uniq | wc -l
$ sort <file> | uniq > file.txt
```

## Network
```console
netstat

$ netstat
```

## Cut

```console
$ cat /etc/passwd | cut -d ":" -f 1

Show only usernames
-d delimiter ":"
-f field
```

## Variables

```console
$ export VAR=value
use it with -> $var
```

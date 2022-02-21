# Usefull Commands

### Processes

```console
show processes

root@machine:~$ ps aux
```

```console
show start command by PID

root@machine:~$ ps -e {PID} -o args
```


```console
show start command with grep

root@machine:~$ ps aux | grep <name>
```
---
### Cat
```console
cat all with keyword "test" and 20 lines bevor and after

root@machine:~$ cat * | grep -A 20 -B 20 "test"
```
---
### Sort
```console
sort file and only uniq words + count

root@machine:~$ sort <file> | uniq | wc -l
root@machine:~$ sort <file> | uniq > file.txt
```

### Network
```console
netstat

root@machine:~$ netstat
```

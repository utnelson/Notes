# Usefull Commands

### Processes

```console
show processes

root@machine:~$ ps aux
```

```console
- show start command by PID

root@machine:~$ ps -e {PID} -o args
```


```console
show start command with grep

root@machine:~$ ps aux | grep mongo
```
---
```console
cat all with keyword "test" and 20 lines bevor and after

cat * | grep -A 20 -B 20 "test"
```


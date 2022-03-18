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

## grep

Grep is an essential Linux and Unix command. It is used to search text and strings in a given file.

```console
$ grep -rn -e "text"
$ grep -ri x11forwarding /etc/

-r, --recursive
              Read  all files under each directory, recursively, following symbolic links only if they are on the command line.  Note that if no file operand is given, grep searches
              the working directory.  This is equivalent to the -d recurse option.
-n, --line-number
              Prefix each line of output with the 1-based line number within its input file.
-e PATTERNS, --regexp=PATTERNS
              Use PATTERNS as the patterns.  If this option is used multiple times or is combined with the -f (--file) option, search for all patterns given.   This  option  can  be
              used to protect a pattern beginning with “-”.
-i            ignor capital letters


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

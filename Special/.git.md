# Git

## GitTools

[Internetwache GitTools](https://github.com/internetwache/GitTools)

Is used to download as much as it can from a /.git
```console
$ ./gitdumper.sh -h

[*] USAGE: http://target.tld/.git/ dest-dir [--git-dir=otherdir]
		--git-dir=otherdir		Change the git folder name. Default: .git

```

Is used to recover the dumped repository

```console
$ ./extractor.sh /tmp/mygitrepo /tmp/mygitrepodump
```
Unix:
```shell
$ file testfile1.txt
testfile.txt: ASCII text
```
"DOS":
```shell
$ file testfile2.txt
testfile2.txt: ASCII text, with CRLF line terminators
```
To convert from "DOS" to Unix:
```shell
$ dos2unix testfile2.txt
```
To convert from Unix to "DOS":
```shell
$ unix2dos testfile1.txt
```
To install dos2unix on CentOS, Fedora or RHEL:
```shell
$ sudo yum install dos2unix
```
To install dos2unix on Ubuntu or Debian:
```shell
$ sudo apt-get install tofrodos
$ sudo ln -s /usr/bin/fromdos /usr/bin/dos2unix
```

git 
---
```shell
git config --global core.autocrlf true
```

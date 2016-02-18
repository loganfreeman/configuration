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

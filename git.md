get branch
---
```
git rev-parse --abbrev-ref HEAD
ask :branch, proc { `git rev-parse --abbrev-ref HEAD`.chomp }
```
auto convert new line
---
```shell
git config --global core.autocrlf true
```

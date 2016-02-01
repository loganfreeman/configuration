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
checkout branch
---
```shell
# Usage: ${0} [branch=master]
BRANCH="${1:-master}"
REMOTE_REPO="https://github.com/uofu-ccts/sparc-request.git"
mkdir $BRANCH
cd $BRANCH
git init
git remote add -t $BRANCH -f origin $REMOTE_REPO
git checkout $BRANCH
```

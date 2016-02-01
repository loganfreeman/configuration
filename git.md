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
# Usage: ${0} repo [branch=master]
if [ -z ${1} ]; then
  exit "REMOTE REPO IS EMPTY"
fi
BRANCH="${2:-master}"
REMOTE_REPO="${1}"
mkdir $BRANCH
cd $BRANCH
git init
git remote add -t $BRANCH -f origin $REMOTE_REPO
git checkout $BRANCH
```

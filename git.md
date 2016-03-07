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
release
---
```ruby
require File.expand_path('../../lib/version',  __FILE__)

version = Discourse::VERSION::STRING
puts "New version is: #{version}"

unless ARGV.include?('no-commit') or !update_version_file
  puts "Committing..."

  `git add lib/version.rb`
  `git commit -m "Version bump to v#{version}"`
  sha = `git rev-parse HEAD`.strip
  `git tag -d latest-release`
  `git push origin :latest-release`
  `git tag -a v#{version} -m "version #{version}" #{sha}`
  `git tag -a latest-release -m "latest release" #{sha}`
end

if ARGV.include?('push')
  puts "Pushing..."

  `git push origin master`
  `git push origin v#{version}`
  `git push origin latest-release`
end
```
feature branch
---
When starting work on a new feature, branch off from the develop branch.
```shell
git checkout -b myfeature develop
```
Finished features may be merged into the develop branch to definitely add them to the upcoming release.
> The `--no-ff` flag causes the merge to always create a new commit object, even if the merge could be performed with a fast-forward. 
This avoids losing information about the historical existence of a feature branch and groups together all commits that together added the feature.

```shell
git checkout develop
git merge --no-ff myfeature
git branch -d myfeature # Deleted branch myfeature
git push origin develop
```
Creating a release branch
---
```shell
git checkout -b release-1.2 develop
./bump-version.sh 1.2 # version bumped to 1.2.
git commit -a -m "Bumped version number to 1.2" 
```
Finishing a release branch
---
```shell
git checkout master
git merge --no-ff release-1.2
git tag -a 1.2 # the -a option makes an unsigned, annotated tag object
git checkout develop
git merge --no-ff release-1.2
git branch -d release-1.2  # now the release may be deleted
```
git flow
---
After installing git-flow (`brew install git-flow`), you can start using git-flow in your repository by using it's `init` command. 
```shell
git flow init
```
> `git-flow` is just a wrapper around existing git commands, so the init command doesn't change anything in your repository other than creating branches for you.

> If you run git branch after setting up, you'll notice that you switched from the master branch to a new one named develop.

```shell
git flow feature start authentication
```
> Switched to a new branch 'feature/authentication'

get current branch
---
```shell
git branch --no-color | grep '^\* ' | grep -v 'no branch' | sed 's/^* //g'
```

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

```shell
git flow feature finish authentication
```
> Switched to branch 'develop'. 

> Summary of actions:
- The feature branch 'feature/authentication' was merged into 'develop'
- Feature branch 'feature/authentication' has been removed
- You are now on branch 'develop'

> Internally, git-flow used `git merge --no-ff feature/authentication` to make sure you don't lose any hostorical information about your feature branch before it is removed.

```shell
git flow release start 0.1.0
git flow release finish 0.1.0
```
> Summary of actions:
- Latest objects have been fetched from 'origin'
- Release branch has been merged into 'master'
- The release was tagged '0.1.0'
- Release branch has been back-merged into 'develop'
- Release branch 'release/0.1.0' has been deleted


Only remove files from remote
---
```shell
git rm -r --cached cache/
echo /cache/ >> .gitignore
```
git rename remote branch
---
You just need create a new branch and delete the old branch
```shell
git branch new-branch-name origin/old-branch-name
git push origin --set-upstream new-branch-name
git push origin :old-branch-name
```
git compare two branches
---
```shell
git diff branch1 branch2 --name-only
git diff branch1 branch2 -- file-name-to-compare
```

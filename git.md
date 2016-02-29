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

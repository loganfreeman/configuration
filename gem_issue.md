eventmachine openssl issue
---
```shell
gem install eventmachine -- --with-cppflags=-I/usr/local/opt/openssl/include 
```

When using bundler and homebrew:
---
```shell
bundle config build.eventmachine --with-cppflags=-I$(brew --prefix openssl)/include
bundle install
```

sqlite3 1.3.7 implicit declaration of function 'RBIGNUM' is invalid in C99
---
```shell
gem install sqlite3
```
“gem install therubyracer -v '0.10.2'” on osx not installing
---
```
linking shared-object v8.bundle
clang: error: no such file or directory: '~/.rvm/gems/ruby-2.1.5/gems/libv8-3.3.10.4/lib/libv8/build/v8/libv8.a'
```
If you decide to use a newer therubyracer gem version, you will no longer have this problem

Otherwise:
```shell
brew tap homebrew/dupes # Thanks Tom
brew install apple-gcc42

export CC=/usr/local/Cellar/apple-gcc42/4.2.1-5666.3/bin/gcc-4.2
export CXX=/usr/local/Cellar/apple-gcc42/4.2.1-5666.3/bin/g++-4.2
export CPP=/usr/local/Cellar/apple-gcc42/4.2.1-5666.3/bin/cpp-4.2

brew uninstall v8

gem uninstall libv8

gem install therubyracer -v '0.10.2' # specify version
```

Fortunately, with homebrew fixing this (on OSX 10.11, El Capitan at least) worked perfectly. Simply execute these commands:
```
brew install v8
gem install therubyracer
gem install libv8 -v '3.16.14.3' -- --with-system-v8
```

[capybara-webkit Qt issue](https://github.com/thoughtbot/capybara-webkit/wiki/Installing-Qt-and-compiling-capybara-webkit)
---
Latest Capybara-webkit throws a deprecation warning:
```
WARNING: The next major version of capybara-webkit will require at least version 5.0 of Qt. You're using version 4.8.7.
```

```shell
brew uninstall qt
brew install qt5
brew link --force qt5
which qmake # should return /usr/local/bin/qmake
gem pristine capybara-webkit # At this point I can rebuild the gem against the new qt5.
```
ExecJS and could not find a JavaScript runtime
---
```
# Ubuntu
sudo apt-get install nodejs
# CentOS
sudo yum install nodejs
```
`Installing mysql2 0.3.19 with native extensions` Hangs forever
---
change to 0.3.20
```shell
yum install mysql-devel
```
[Installing Qt and compiling capybara webkit](https://github.com/thoughtbot/capybara-webkit/wiki/Installing-Qt-and-compiling-capybara-webkit)
---
CentOS
```shell
yum install qtwebkit-devel # will work with the dash in between qt and webkit, but not if you are using chef
ln -s /usr/lib64/qt4/bin/qmake /usr/bin/qmake # or some other method to get qmake in your path like `export QMAKE`
```
Incorrect MySQL client library version! Gem compiled for the wrong client library version
---
```shell
gem uninstall mysql2 # choose all versions
bundle install
```
error install pg
---
```shell
yum install postgresql-libs
yum install postgresql-devel
```
libxml2
---
```ruby
 rvm cleanup all
 rvm pkg uninstall libxml2
 rvm reinstall all --force
 ```
nokogiri not compatible with libxml2
---
```shell
yum -y install libxml2-devel libxslt-devel
```
ruby headers
---
```shell
yum -y install gcc mysql-devel ruby-devel rubygems
```
libv8
---
```shell
gem install libv8 -v '3.16.14.3' -- --with-system-v8
bundle config build.libv8 --with-system-v8
```

pg_ext improperly linked
---
```shell
ldd  /usr/local/rvm/gems/ruby-2.2.4/gems/pg-0.18.4/lib/pg_ext.so | grep libruby
# show: libruby.so.2.0 => /usr/lib64/libruby.so.2.0 (0x00007fe73da7d000)
gem install pg -- --with-pg-config=/var/lib/pgsql/data
ldd  /usr/local/rvm/gems/ruby-2.2.4/gems/pg-0.18.4/lib/pg_ext.so | grep libruby
# show: libruby.so.2.2 => /usr/local/rvm/rubies/ruby-2.2.4/lib/libruby.so.2.2 (0x00007f142615b000)
```

bundle install fails but gem install succeeds
---
> It looks like when you run bundler, it's using the Mac OS X system Ruby (/usr/bin/ruby) but when using gem it is using a Ruby 1.8.7 installed in ~/.rubies.

> When building native extensions for the system Ruby, you need to have the full XCode installed: just the command-line tools alone don't work.

> I'd bet that the problem is that Bundler itself is installed as gem under the system Ruby, rather than the one in ~/.rubies.


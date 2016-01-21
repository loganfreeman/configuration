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

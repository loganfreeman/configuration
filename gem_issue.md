1. eventmachine openssl issue
```shell
gem install eventmachine -- --with-cppflags=-I/usr/local/opt/openssl/include 
```

When using bundler and homebrew:
```shell
bundle config build.eventmachine --with-cppflags=-I$(brew --prefix openssl)/include
bundle install
```

2. sqlite3 1.3.7 implicit declaration of function 'RBIGNUM' is invalid in C99

```shell
gem install sqlite3
```

1. eventmachine openssl issue
```shell
gem install eventmachine -- --with-cppflags=-I/usr/local/opt/openssl/include 
```

When using bundler and homebrew:
```shell
bundle config build.eventmachine --with-cppflags=-I$(brew --prefix openssl)/include
bundle install
```

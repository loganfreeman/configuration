Setup
---
setup cron environment for rvm
```shell
rvm cron setup
```

Load rvm in shell 
```shell
#!/usr/bin/env bash

# load rvm ruby
source /usr/local/rvm/environments/ruby-2.1.1@projectX
bundle install
bundle exec rake task
```

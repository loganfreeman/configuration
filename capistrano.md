Checking the directory structure on the remote machine:
---
```shell
ssh deploy@remote 'ls -lR /var/www/my-application'
```
Writing our first cap task to formalize this into a check!
---
```shell
desc "Check that we can access everything"
task :check_write_permissions do
  on roles(:all) do |host|
    if test("[ -w #{fetch(:deploy_to)} ]")
      info "#{fetch(:deploy_to)} is writable on #{host}"
    else
      error "#{fetch(:deploy_to)} is not writable on #{host}"
    end
  end
end
```
add those lines to a file in ./lib/capistrano/tasks, call it something like access_check.rake, and run cap -T from the top directory and we'll be able to see the task listed:

```
bundle exec cap -T
```

check ssh agent forwarding
---
```ruby
# lib/capistrano/tasks/agent_forwarding.rake
desc "Check if agent forwarding is working"
task :forwarding do
  on roles(:all) do |h|
    if test("env | grep SSH_AUTH_SOCK")
      info "Agent forwarding is up to #{h}"
    else
      error "Agent forwarding is NOT up to #{h}"
    end
  end
end
```

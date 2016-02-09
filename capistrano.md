Checking the directory structure on the remote machine:
---
```shell
ssh deploy@remote 'ls -lR /var/www/my-application'
```
Writing our first cap task to formalize this into a check!
---
```ruby
# /lib/capistrano/tasks/access_check.rake
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

Local Tasks
---
```ruby
desc 'Notify service of deployment'
task :notify do
  run_locally do
    with rails_env: :development do
      rake 'service:notify'
    end
  end
end
```
Of course, you can always just use standard ruby syntax to run things locally:
```ruby
desc 'Notify service of deployment'
task :notify do
  %x('RAILS_ENV=development bundle exec rake "service:notify"')
end
```
Alternatively you could use the rake syntax:
```ruby
desc "Notify service of deployment"
task :notify do
   sh 'RAILS_ENV=development bundle exec rake "service:notify"'
end
```

To create deploy user
---
```shell
adduser deploy
# this locks the user, it changes the user's password to an untypable string, 
# guaranteeing that the user has no password which can be used to log in
passwd -l deploy
```

Authentication
---
1. From our workstation/notebook/etc to our servers. We do this with SSH keys, passphrase protected, ideally, using a key agent.
2. From our servers to the repository host. We do this so that our servers can check out our application code from Github, or similar and install it to the servers. This is usually done using SSH agent forwarding, HTTP authentication, or with deploy keys.

Commands
---
```shell
bundle exec cap -V # list capistrano version
bundle exec cap -T # list all capistrano task
bundle exec cap install # install and initialize
```
sudo if needed
---
```ruby
    def sudo_if_needed(command)
      send(use_sudo? ? :sudo : :execute, command)
    end
```
Precompile assets only after changes
---
```ruby
# set the locations that we will look for changed assets to determine whether to precompile
set :assets_dependencies, %w(app/assets lib/assets vendor/assets Gemfile config/routes.rb)


# clear the previous precompile task
Rake::Task["deploy:assets:precompile"].clear_actions
class PrecompileRequired < StandardError; end


namespace :deploy do
  namespace :assets do
    desc "Precompile assets if changed"
    task :precompile_changed do
      on roles(:app) do
        within release_path do
          with rails_env: fetch(:rails_env) do
            begin

              # find the most recent release
              latest_release = capture(:ls, '-xr', releases_path).split[1]

              # precompile if this is the first deploy
              raise PrecompileRequired unless latest_release

              #
              latest_release_path = releases_path.join(latest_release)

              # precompile if the previous deploy failed to finish precompiling
              execute(:ls, latest_release_path.join('assets_manifest_backup')) rescue raise(PrecompileRequired)

              fetch(:assets_dependencies).each do |dep|
                #execute(:du, '-b', release_path.join(dep)) rescue raise(PrecompileRequired)
                #execute(:du, '-b', latest_release_path.join(dep)) rescue raise(PrecompileRequired)

                # execute raises if there is a diff
                execute(:diff, '-Naur', release_path.join(dep), latest_release_path.join(dep)) rescue raise(PrecompileRequired)
              end

              warn("-----Skipping asset precompile, no asset diff found")

              # copy over all of the assets from the last release
              execute(:cp, '-rf', latest_release_path.join('public', fetch(:assets_prefix)), release_path.join('public', fetch(:assets_prefix)))

            rescue PrecompileRequired
              warn("----Run assets precompile")

              execute(:rake, "assets:precompile")
            end
          end
        end
      end
    end
  end
end
```
maintenance page for nginx
---
```ruby
# maintenance page
namespace :deploy do
  namespace :web do
    desc <<-DESC
      Present a maintenance page to visitors.
        $ cap deploy:web:disable REASON="a hardware upgrade" UNTIL="12pm Central Time"
    DESC

    task :disable do
      on roles(:web) do
        execute "rm #{shared_path}/system/maintenance.html" rescue nil

        require 'erb'
        reason = ENV['REASON']
        deadline = ENV['UNTIL']
        template = File.read('app/views/admin/maintenance.html.haml')
        #page = ERB.new(template).result(binding)
        content = ERB.new(template).result(binding)

        path = "public/system/maintenance.html"
        File.open(path, "w") { |f| f.write content }

        upload! path, "#{shared_path}/public/system/maintenance.html", :mode => 0644
      end

    end

    task :enable do
      on roles(:web) do
        execute "rm #{shared_path}/public/system/maintenance.html"
      end
    end

  end
end
```
deploy
---
```ruby
desc 'Deploy a new release.'
task :deploy do
  set(:deploying, true)
  %w{ starting started
      updating updated
      publishing published
      finishing finished }.each do |task|
    invoke "deploy:#{task}"
  end
end
```

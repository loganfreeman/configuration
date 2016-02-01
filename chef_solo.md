copies the tree to the server and runs `install.sh`
---
[Managing a single server with Chef](http://www.opinionatedprogrammer.com/2011/06/chef-solo-tutorial-managing-a-single-server-with-chef/)
```shell
#!/bin/bash

# Usage: ./deploy.sh [host]

host="${1:-ubuntu@opinionatedprogrammer.com}"

# The host key might change when we instantiate a new VM, so
# we remove (-R) the old host key from known_hosts
ssh-keygen -R "${host#*@}" 2> /dev/null

tar cj . | ssh -o 'StrictHostKeyChecking no' "$host" '
sudo rm -rf ~/chef &&
mkdir ~/chef &&
cd ~/chef &&
tar xj &&
sudo bash install.sh'
```
[vagrant made easy with chef_solo](https://adamcod.es/2013/01/15/vagrant-is-easy-chef-is-hard-part2.html)
---

Install Chef solo & knife solo
---
```shell
curl -L https://www.opscode.com/chef/install.sh | bash
chef-solo -v
wget http://github.com/opscode/chef-repo/tarball/master
tar -zxf master
mv chef-chef-repo* chef-repo
cd chef-repo/
mkdir .chef
echo "cookbook_path [ '/root/chef-repo/cookbooks' ]" > .chef/knife.rb
knife cookbook create phpapp
knife cookbook site download apache2
tar zxf apache2*
knife cookbook site download apt
tar zxf apt*
knife cookbook site download iptables
tar zxf iptables*
knife cookbook site download logrotate
tar zxf logrotate*
```

```shell
gem install knife-solo --no-ri --no-rdoc
gem install librarian-chef --no-ri --no-rdoc
cd ~/Projects
mkdir chef-demo
cd chef-demo
knife solo init .
librarian-chef init
cat <<EOT >> Cheffile
cookbook 'apache2'
cookbook 'mysql'
cookbook 'php'
EOT
cat <<EOT nodes/82.196.8.99.json
{
  "apache": {
    "default_site_enabled": true
  },
  "mysql": {
    "server_root_password": "yoursecretsecurepassword",
    "server_repl_password": "yoursecretsecurepassword",
    "server_debian_password": "yoursecretsecurepassword"
  },
  "run_list": [
    "recipe[apache2]",
    "recipe[mysql]",
    "recipe[mysql::server]",
    "recipe[php]"
  ]
}
EOT
librarian-chef install
# This will login to your server, download and install chef, copy across your cookbooks, and then run chef. 
knife solo bootstrap root@82.196.8.99
```

rbenv
---
```shell
#!/bin/bash
set -e # exit on error


# Configuration
INSTALL_RUBY_VERSIONS=("1.9.3-p545" "2.1.1")
PROFILE=~/.bash_profile


# Install build required package for rbenv
## For another operating system, Refer the page below.
## https://github.com/sstephenson/ruby-build/wiki#suggested-build-environment
yum -y install gcc-c++ glibc-headers openssl-devel readline libyaml-devel readline-devel zlib zlib-devel git


# Install build required package for Apache Passenger
yum -y install curl-devel httpd-devel


# Install rbenv
cd /root
git clone https://github.com/sstephenson/rbenv.git ~/.rbenv
grep 'rbenv/bin' $PROFILE &>/dev/null || \
  echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
grep 'rbenv init' $PROFILE &>/dev/null || \
  echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
source ~/.bash_profile
test -d ~/.rbenv/plugins/ruby-build || \
  git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
test -d ~/.rbenv/plugins/rbenv-gem-rehash || \
  git clone https://github.com/sstephenson/rbenv-gem-rehash.git ~/.rbenv/plugins/rbenv-gem-rehash
grep 'gem: --no-rdoc --no-ri' $PROFILE &>/dev/null || \
  echo 'gem: --no-rdoc --no-ri' >> ~/.gemrc
for version in ${INSTALL_RUBY_VERSIONS[@]}; do
  rbenv install $version
  rbenv rehash
  rbenv global $version
  # Install some gems with specific version as you like.
  gem install -l /vagrant/gems/bundler-1.6.2.gem
  gem install -l /vagrant/gems/rack-1.5.2.gem \
    /vagrant/gems/daemon_controller-1.2.0.gem \
    /vagrant/gems/passenger-4.0.41.gem
  passenger-install-apache2-module --languages ruby --auto 2>/dev/null
done


# Set executable option
chmod o+x /root
chmod o+x /root/.rbenv/


# Put configuration files into apache
cp /vagrant/passenger.conf /etc/httpd/conf.d/passenger.conf
cp /vagrant/virtualhosts.conf /etc/httpd/conf.d/virtualhosts.conf


# Put sample applications
for version in "ruby19" "ruby21"; do
  cp -rp /vagrant/$version.example.jp /var/www/$version.example.jp
done


# Start apache service
service httpd start


echo "Done!!"

```

Application Environment Setup Using /etc/profile.d/*
---
> When a user logs in, environment variables are set from various places.  That includes `/etc/profile` (for all users).
> Then all the files in the `/etc/profile.d` directory.
> Then `~/.bash_profile`, then `~/.bashrc`.
```shell
echo "Adding composer's vendor directory to system PATH"
cat >/etc/profile.d/composer-bin-root.sh <<EOF
#!/usr/bin/env bash
pathmunge /home/vagrant/.composer/vendor/bin after
pathmunge /root/.composer/vendor/bin after
EOF
```


install rbenv and multiple ruby versions and passenger
---
```shell
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
```
find a needle in an array of strings
---
```
yourValue="$1"
echo "you entered $yourValue"
array=(hello world peace war)
for i in "${array[@]}"
do
    # echo "$i"
    if [ "$i" == "$yourValue" ] ; then
        echo "Found Match"
    fi
done
```
number of arguments
---
```shell
if [ $# -ne $Number_of_expected_args ]
then
  echo "Usage: `basename $0` $script_parameters"
  # `basename $0` is the script's filename.
  exit $E_WRONG_ARGS
fi
```
create a script to update time
---
```shell
# Create a script to update time.
cat >/usr/bin/updatetime <<EOL
systemctl stop ntpd.service
ntpdate pool.ntp.org
systemctl start ntpd.service
EOL
# now we can just run "updatetime" to restart and sync time servers:
chmod +x /usr/bin/updatetime
```
Get the parent directory of where this script is
---
```shell
while [ -h "$SOURCE" ] ; do SOURCE="$(readlink "$SOURCE")"; done
DIR="$( cd -P "$( dirname "$SOURCE" )/.." && pwd )"
```
make a temp directory and clean up when exit
---
```shell
# Create a temporary build dir and make sure we clean it up. For
# debugging, comment out the trap line.
DEPLOY=`mktemp -d /tmp/vagrant-www-XXXXXX`
trap "rm -rf $DEPLOY" INT TERM EXIT
```

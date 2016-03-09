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
For an array, the keys are accessed using an exclamation point: `${!array[@]}`, the values are accessed using `${array[@]}`.

You can iterate over the key/value pairs like this:
```shell
for i in "${!array[@]}"
do
  echo "key  : $i"
  echo "value: ${array[$i]}"
done
```

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
traverse upward until home root
---
```shell
  _files=( $(
    while [[ "$PWD" != "/" && "$PWD" != "$home" ]]
    do
      _file="$PWD/$AUTOENV_ENV_FILENAME"
      if [[ -f "${_file}" ]]
      then echo "${_file}"
      fi
      builtin cd .. &>/dev/null
    done
  ) )
  
  _file=${#_files[@]}
  while (( _file > 0 ))
  do
    envfile=${_files[_file-__array_offset]}
    autoenv_check_authz_and_run "$envfile"
    : $(( _file -= 1 ))
  done
```
escape spaces in path
---
```shell
dir=$(echo $dir | sed 's/ /\ /g')
"${dir}/php-parse" "$@"
```
cypath
---
```shell
# See if we are running in Cygwin by checking for cygpath program
if command -v 'cygpath' >/dev/null 2>&1; then
	# Cygwin paths start with /cygdrive/ which will break windows PHP,
	# so we need to translate the dir path to windows format. However
	# we could be using cygwin PHP which does not require this, so we
	# test if the path to PHP starts with /cygdrive/ rather than /usr/bin
	if [[ $(which php) == /cygdrive/* ]]; then
		dir=$(cygpath -m $dir);
	fi
fi
```
Find out which shell is running
---
```shell
ps -p $$
# use bash as shell
usermod -s /bin/bash YOUR_USERNAME
```
If the user has no admin priviledges, just add to the end of `.cshrc`
```
setenv SHELL /bin/bash
exec /bin/bash --login
```
bash prompt
---
```
# If id command returns zero, you’ve root access.
if [ $(id -u) -eq 0 ];
then # you are root, set red colour prompt
  PS1="\\[$(tput setaf 1)\\]\\u@\\h:\\w #\\[$(tput sgr0)\\]"
else # normal
  PS1="[\\u@\\h:\\w] $"
fi
```
logger
---
```shell
log_generic () {
  priority="$1"
  shift

  msg="`date +'%y%m%d %H:%M:%S'` mysqld_safe $*"
  echo "$msg"
  case $logging in
    init) ;;  # Just echo the message, don't save it anywhere
    file) echo "$msg" >> "$err_log" ;;
    syslog) logger -t "$syslog_tag_mysqld_safe" -p "$priority" "$*" ;;
    both) echo "$msg" >> "$err_log"; logger -t "$syslog_tag_mysqld_safe" -p "$priority" "$*" ;;
    *)
      echo "Internal program error (non-fatal):" \
           " unknown logging method '$logging'" >&2
      ;;
  esac
}
```

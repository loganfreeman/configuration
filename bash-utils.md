get platform
---
```shell
get_distro() {
	ARCH=$(uname -m | sed 's/x86_/amd/;s/i[3-6]86/x86/')

	if [ $ARCH == "amd64" ]; then
		T_ARCH="x86_64"
		WK_ARCH="amd64"
	else
		T_ARCH="i386"
		WK_ARCH="i386"
	fi

	if [ -f /etc/redhat-release ]; then
		OS="centos"
		OS_VER=`cat /etc/redhat-release | sed 's/Linux\ //g' | cut -d" " -f3 | cut -d. -f1`

	elif [ -f /etc/lsb-release ]; then
		. /etc/lsb-release
		OS=$DISTRIB_ID
		OS_VER=$DISTRIB_CODENAME

	elif [ -f /etc/debian_version ]; then
		. /etc/os-release
		OS="debian"  # XXX or Ubuntu??
		OS_VER=$VERSION_ID
	fi

	export OS=$OS
	export OS_VER=$OS_VER
	export ARCH=$ARCH
	export T_ARCH=$T_ARCH
	export WK_ARCH=$WK_ARCH
	echo Installing for $OS $OS_VER $ARCH
	echo "In case you encounter an error, you can post on https://discuss.frappe.io"
	echo
}
```
verbose
---
```shell
run_cmd() {
	if $VERBOSE; then
		"$@"
	else
		# $@
		"$@" > /tmp/cmdoutput.txt 2>&1 || (cat /tmp/cmdoutput.txt && exit 1)
	fi
}
```
add user
---
```shell
add_user() {
# Check if script is running as root and is not running as sudo. We want to skip
# this step if the user is already running this script with sudo as a non root
# user
	if [ "$FRAPPE_USER" == "false" ]; then
		if [[ $SUDO_UID -eq 0 ]] && [[ $EUID -eq 0 ]]; then
			export FRAPPE_USER="frappe"
		else
			export FRAPPE_USER="$SUDO_USER"
		fi
	fi

	USER_EXISTS=`bash -c "id $FRAPPE_USER > /dev/null 2>&1  && echo true || (echo false && exit 0)"`

	if [ $USER_EXISTS == "false" ]; then
		useradd -m -d /home/$FRAPPE_USER -s $SHELL $FRAPPE_USER
		echo $FRAPPE_USER:$FRAPPE_USER_PASS | chpasswd
		chmod o+x /home/$FRAPPE_USER
		chmod o+r /home/$FRAPPE_USER
	fi
}
```
configure mariaDB
---
```shell
configure_mariadb() {
	config="
[mysqld]
innodb-file-format=barracuda
innodb-file-per-table=1
innodb-large-prefix=1
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci
[mysql]
default-character-set = utf8mb4
 "
	deb_cnf_path="/etc/mysql/conf.d/barracuda.cnf"
	centos_cnf_path="/etc/my.cnf.d/barracuda.cnf"

	if [ $OS == "centos" ]; then

		echo "$config" > $centos_cnf_path
		if [ $OS_VER == "6" ]; then
			run_cmd sudo service mysql restart
		elif [ $OS_VER == "7" ]; then
			run_cmd sudo systemctl restart mysql
		fi

	elif [ $OS == "debian" ] || [ $OS == "Ubuntu" ]; then
		echo "$config" > $deb_cnf_path
		sudo service mysql restart
	fi
}
```
generate password
---
```shell
get_passwd() {
	echo `cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w 16 | head -n 1`
}
```
install supervisor
---
```shell
install_supervisor_centos6() {
		run_cmd easy_install supervisor
		curl -Ss https://raw.githubusercontent.com/pdvyas/supervisor-initscripts/master/redhat-init-jkoppe > /etc/init.d/supervisord
		curl -Ss https://raw.githubusercontent.com/pdvyas/supervisor-initscripts/master/redhat-sysconfig-jkoppe > /etc/sysconfig/supervisord
		curl -Ss https://raw.githubusercontent.com/pdvyas/supervisor-initscripts/master/supervisord.conf > /etc/supervisord.conf
		run_cmd mkdir /etc/supervisor.d
		run_cmd chmod +x /etc/init.d/supervisord
		bash -c "service supervisord start || true"
}
```
install wkhtmltopdf
---
```shell
install_wkhtmltopdf_centos () {

	if [[ $OS == "centos" && $OS_VER == "7" && $T_ARCH == "i386" ]]; then
		echo "Cannot install wkhtmltodpdf. Skipping..."
		return 0
	fi
	RPM="wkhtmltox-0.12.2.1_linux-$OS$OS_VER-$WK_ARCH.rpm"
	run_cmd wget http://download.gna.org/wkhtmltopdf/0.12/0.12.2.1/$RPM
	rpm --quiet -q wkhtmltox || run_cmd rpm -Uvh $RPM
}

install_wkhtmltopdf_deb () {
	WK_VER=$OS_VER

	if [[ $OS_VER == "utopic" ||  $OS_VER == "vivid" ||  $OS_VER == "wily" ]]; then
		echo "Installing wkhtmltox package for trusty (Ubuntu 14.4) even if you are using $OS_VER."
		WK_VER="trusty"
	fi
	if [[ $OS == "debian" &&  $OS_VER == "7" ]]; then
		WK_VER="wheezy"
	elif [[ $OS == "debian" &&  $OS_VER == "8" ]]; then
		WK_VER="jessie"
	fi

	run_cmd wget http://download.gna.org/wkhtmltopdf/0.12/0.12.2.1/wkhtmltox-0.12.2.1_linux-$WK_VER-$WK_ARCH.deb
	run_cmd dpkg -i wkhtmltox-0.12.2.1_linux-$WK_VER-$WK_ARCH.deb
}
```
add epel
---
```shell
add_epel_centos7() {
	yum install -y epel-release
}
```
realpath
---
```shell

realpath () {
(
  TARGET_FILE="$1"

  cd "$(dirname "$TARGET_FILE")"
  TARGET_FILE=$(basename "$TARGET_FILE")

  COUNT=0
  while [ -L "$TARGET_FILE" -a $COUNT -lt 100 ]
  do
      TARGET_FILE=$(readlink "$TARGET_FILE")
      cd "$(dirname "$TARGET_FILE")"
      TARGET_FILE=$(basename "$TARGET_FILE")
      COUNT=$(($COUNT + 1))
  done

  if [ "$TARGET_FILE" == "." -o "$TARGET_FILE" == ".." ]; then
    cd "$TARGET_FILE"
    TARGET_FILEPATH=
  else
    TARGET_FILEPATH=/$TARGET_FILE
  fi

  # make sure we grab the actual windows path, instead of cygwin's path.
  if ! is_cygwin; then
    echo "$(pwd -P)/$TARGET_FILE"
  else
    echo $(cygwinpath "$(pwd -P)/$TARGET_FILE")
  fi
)
}
```
is_cygwin
---
```shell
# Uses uname to detect if we're in the odd cygwin environment.
is_cygwin() {
  local os=$(uname -s)
  case "$os" in
    CYGWIN*) return 0 ;;
    *)  return 1 ;;
  esac
}

# This can fix cygwin style /cygdrive paths so we get the
# windows style paths.
cygwinpath() {
  local file="$1"
  if is_cygwin; then
    echo $(cygpath -w $file)
  else
    echo $file
  fi
}
```

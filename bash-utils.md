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

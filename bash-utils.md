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

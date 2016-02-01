getops
---
```shell
while getopts "hqv:t:m:e:" OPTION; do
	case ${OPTION} in
		v ) if [ "${OPTARG}" == "5.4" ]; then
				VERSION="php"
			elif [ "${OPTARG}" == "5.5" ]; then
				VERSION="php55w"
				USE_EPEL=true
			elif [ "${OPTARG}" == "5.6" ]; then
				VERSION="php56w"
				USE_EPEL=true
			else
				${QUIET} || >&2 echo "Unsupported PHP version '${OPTARG}'"
				exit 1
			fi
			VERSION_STRING=${OPTARG}
			;;
		t ) TZ=${OPTARG}
			;;
		m ) MEMORY_LIMIT=${OPTARG}
			;;
		q ) QUIET=false
			;;
		e ) EXEC_TIME=${OPTARG}
		    ;;
		h ) usage
			exit 0
	esac
done

function usage() {
	echo -e "Syntax `basename $0` [-h] [-v version]
	-h Show this help
	-q Quiet mode
	-t Timezone to use for PHP.ini
	-m Memory limit for PHP.ini (in M)
	-e Max execution time for PHP.ini
	-v Set the version of PHP to install\n"
}
```

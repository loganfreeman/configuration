getops
---
[some getopts tutorial](http://wiki.bash-hackers.org/howto/getopts_tutorial)
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
An option with argument
---
 Also we disable the verbose error handling by preceding the whole option string with a `colon (:)`.
 
`shift $((OPTIND-1))` removes all the options that have been parsed by getopts from the parameters list, and so after that point, `$1` will refer to the first non-option argument passed to the script.
```shell
#!/bin/bash
 
while getopts ":a:" opt; do
  case $opt in
    a)
      echo "-a was triggered, Parameter: $OPTARG" >&2
      ;;
    \?)
      echo "Invalid option: -$OPTARG" >&2
      exit 1
      ;;
    :)
      echo "Option -$OPTARG requires an argument." >&2
      exit 1
      ;;
  esac
done

shift $((OPTIND-1))
```

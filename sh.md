source and then exec
---
```shell
read_profile=""

while getopts ":hd:p" OPT; do
  case $OPT in
     d) cd "$OPTARG" ;;
     p) read_profile="1" ;;
     h) usage ;;
    \?) error "invalid option: -$OPTARG" ;;
     :) error "option -$OPTARG requires an argument" ;;
  esac
done

shift $((OPTIND-1))

[ -z "$1" ] && usage

if [ "$read_profile" = "1" ]; then
  if [ -f .profile ]; then
    . ./.profile
  fi
fi

exec "$@"
```

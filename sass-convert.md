```shell
#!/bin/bash

#script to recursively call sass-convert to convert

function traverse() {
    for file in $(ls "$1")
    do
        #current=${1}{$file}
        if [[ ! -d ${1}/${file} ]]; then
          filename=$(basename "$file")
          extension="${filename##*.}"
          filename="${filename%.*}"
          if [ "${extension}" = "sass" ]; then
            echo "sass-convert ${1}/${file} ${1}/${filename}.${extension}"
            sass-convert "${1}/${file}" "${1}/${filename}.scss"
          fi
        else
            #echo "entering recursion with: ${1}${file}"
            traverse "${1}/${file}"
        fi
    done
}

function main() {
    traverse "$1"
}

main "$1"
```

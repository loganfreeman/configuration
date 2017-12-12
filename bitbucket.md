clone all repos
---
```
#!/bin/bash

echo -n "Username: ";
read USERNAME;
echo -n "Password: ";
#Disabling echo, so that password will not be visible on screen
read -s PASSWORD
#Enabling echo
echo

echo -n "Server (e.g https://repository.bytefreaks.net:7990): ";
read server;
echo -n "PROJECTNAME to clone repositories in: ";
read PROJECTNAME;

mkdir -p "$PROJECTNAME";
cd "$PROJECTNAME";

#Getting all PROJECTNAMEs
curl -s -u $USERNAME:$PASSWORD https://bitbucket.${server}.com/rest/api/1.0/projects/$PROJECTNAME/repos/ \
  | jq -r '.values[].links.clone[] | select(.name=="ssh") | .href' \
  | xargs -I {} echo "git clone '{}'" > clone_"$PROJECTNAME".sh

exit 0;
```

clone all projects
---
```
#!/bin/bash

#Enabling echo
echo

clone() {
  PROJECTNAME=$1

  mkdir -p "$PROJECTNAME";
  cd "$PROJECTNAME";

  #Getting all PROJECTNAMEs
  curl -s -u $USERNAME:$PASSWORD https://bitbucket.${server}.com/rest/api/1.0/projects/$PROJECTNAME/repos/ \
    | jq -r '.values[].links.clone[] | select(.name=="ssh") | .href' \
    | xargs -I {} echo "git clone '{}'" > clone_"$PROJECTNAME".sh
}

export -f clone

curl -s -u $USERNAME:$PASSWORD https://bitbucket.${server}.com/rest/api/1.0/projects/ \
  | jq -r '.values[] | .key' \
  | xargs -I {} bash -c 'clone "$@"' _ {}

exit 0;
```

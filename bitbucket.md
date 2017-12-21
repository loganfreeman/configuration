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

  chmod +x clone_"$PROJECTNAME".sh
}

export -f clone

curl -s -u $USERNAME:$PASSWORD https://bitbucket.${server}.com/rest/api/1.0/projects/ \
  | jq -r '.values[] | .key' \
  | xargs -I {} bash -c 'clone "$@"' _ {}

exit 0;

```

clone all
```
#!/bin/bash

cloned() {
  subdircount=`find $1 -maxdepth 1 -type d | wc -l`

  echo $subdircount
}

export -f cloned
for f in *; do
    if [[ -d $f ]]; then
      subdircount=$(cloned $f)
      # echo "$f $subdircount"
      if [ $subdircount -eq 1 ]; then
        pushd `pwd`
        cd $f
        sh "clone_`basename $f`.sh"
        popd
      fi

    fi
done
```


clone all repos
---
```ruby
require 'fileutils'
require 'net/http'
require 'openssl'
require 'json'
require 'colorize'
username = ENV['bitbuckt_username']
server = ENV['bitbuckt_server']
password = ENV['bitbucket_password']
project = ARGV[0]
FileUtils::mkdir_p project
Dir.chdir project
puts `pwd`

uri = URI("https://bitbucket.#{server}.com/rest/api/1.0/projects/#{project}/repos/")

# puts "curl -s -u #{username}:#{password} https://bitbucket.${server}.com/rest/api/1.0/projects/#{project}/repos"

Net::HTTP.start(uri.host, uri.port,
  :use_ssl => uri.scheme == 'https',
  :verify_mode => OpenSSL::SSL::VERIFY_NONE) do |http|

  request = Net::HTTP::Get.new uri.request_uri
  request.basic_auth username, password

  response = http.request request # Net::HTTPResponse object

  json = JSON.parse(response.body)


  json['values'].each do | repo |
    puts repo['name'].red
    url = repo['links']['clone'].find { |link| /^http/ =~ link['name'] }['href']

    puts "#{repo['name']} #{Dir.exist? repo['name']}"
    if Dir.exist? repo['name']

    else
      `git clone #{url}`
    end
  end

  File.open("#{project}.json", 'w') { |file| file.write(response.body) }
end
```

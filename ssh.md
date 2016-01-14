[How To Configure Custom Connection Options for your SSH Client](https://www.digitalocean.com/community/tutorials/how-to-configure-custom-connection-options-for-your-ssh-client)
---
```shell
touch ~/.ssh/config
```
The config file is organized by hosts. Each host definition can define connection options for the specific matching host. 

Each of the sections starts with a header defining the hosts that should match the configuration options that will follow. The specific configuration items for that matching host are then defined below. 

The general format will look something like this:
```
Host firsthost
    SSH_OPTION_1 custom_value
    SSH_OPTION_2 custom_value
    SSH_OPTION_3 custom_value

Host secondhost
    ANOTHER_OPTION custom_value

Host *host
    ANOTHER_OPTION custom_value

Host *
    CHANGE_DEFAULT custom_value
```

Basic Connection Options
---
To connect as a user named apollo to a host called example.com that runs its SSH daemon on port 4567 from the command line, we could give the variable information in a variety of ways. The most common would probably be:
```shell
ssh -p 4567 apollo@example.com
```
However, we could also use the full option names with the -o flag, like this:
```shell
ssh -o "User=apollo" -o "Port=4567" -o "HostName=example.com" 
```
You can find a full list of available options by typing:
```shell
man ssh_config
```

SSH agent forwarding
---
```shell
env | grep SSH_AUTH_SOCK
```
To test that agent forwarding is working with your server, you can SSH into your server and run ssh -T git@github.com once more.

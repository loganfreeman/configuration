How To Configure Custom Connection Options for your SSH Client
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

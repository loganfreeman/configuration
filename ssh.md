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

SSH key generating
---
```shell
ssh-keygen -t rsa -C 'me@my_email_address.com'
```
We can see which keys are loaded in the SSH agent by running ssh-add -l
```shell
ssh-add -l
```
To check if the ssh key is present
--
```shell
[ ! -d /root/.ssh ] && echo 'no /root.ssh directory.  mkdir /root/.ssh'
[ -d /root/.ssh ] && [ ! -f /root/.ssh/authorized_keys ] && echo 'no /root/.ssh/authorized_keys file.'
[ -f /root/.ssh/authorized_keys ] && ! grep -qai 'ssh-rsa AAAAB3NzaC1yc2EAAAABJQAAAIBufK3VvFDfjRAswyZibINSWLILZ4sTGuW8ffF5tmMRouGbA55SbUqjOeDQgBfUs0HRp5K34Qnw608DEhZw' /root/.ssh/authorized_keys && echo "/root/.ssh/authorized_keys exists but does not contain the rimuhosting key"
if [ -f /root/.ssh/authorized_keys ] ; then if [ $(find /root/.ssh/authorized_keys -perm /o+rw,g+r | wc -l) -gt 0 ] ; then echo "/root/.ssh/authorized_keys permissions are too open"; fi; fi
if [ -d /root/.ssh ] ; then if [ $(find /root/.ssh -perm /o+rw,g+w | wc -l) -gt 0 ] ; then echo "/root/.ssh directory should not be other or group writable"; fi; fi
echo 'sshd settings.  Check PermitRootLogin is not 'no'.  That the Port is 22 or matches RimuHostings records.  And that AllowUsers (if set) includes root.'
egrep '^PermitRootLogin|^Port|^AllowUsers' /etc/ssh/sshd_config
[ -e /etc/hosts.allow ] && [ $(cat /etc/hosts.allow | egrep -v '^$|#' | wc -l) -gt 0 ] && echo "Contents of /etc/hosts.allow:" && cat /etc/hosts.allow | egrep -v '^$|#'
[ -e /etc/hosts.deny ] && [ $(cat /etc/hosts.deny | egrep -v '^$|#' | wc -l) -gt 0 ] && echo "Contents of /etc/hosts.deny:" && cat /etc/hosts.deny | egrep -v '^$|#'
```

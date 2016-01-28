https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-14-04

As root, run this command to add your new user to the sudo group
```shell
gpasswd -a demo sudo
```
*ssh-copy-id*

After providing your password at the prompt, your public key will be added to the remote user's .ssh/authorized_keys file.
```shell
ssh-copy-id demo@SERVER_IP_ADDRESS
# or manually
mkdir .ssh
chmod 700 .ssh
nano .ssh/authorized_keys
# paste the public key
chmod 600 .ssh/authorized_keys
```
Groups user belongs to
```shell
groups $USER
sudo chown -R $USER:admin /usr/local/share
```
private key permission should be: `chmod 600 ~/.ssh/id_rsa`

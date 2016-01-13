* List running machines (returns name and UUID):
```shell
VBoxManage list runningvms
```
* Stop running VMs by "hibernating" them (reommended to avoid data loss)
```shell
VBoxManage controlvm <name|uuid> savestate
```
* Poweroff running VMs (not recommended because we may lose data in the guest)
```shell
VBoxManage controlvm <name|uuid> poweroff
```
* $HOME/bin/stop-vagrant.sh
```
vboxmanage list runningvms | sed -r 's/.*\{(.*)\}/\1/' | xargs -L1 -I {} VBoxManage controlvm {} savestate
```
#Command Explained:

vboxmanage list runningvms | -- gets a list of all running vms under VirtualBox

sed -r 's/.*\{(.*)\}/\1/' | -- strips the string down to id number

xargs -L1 -I {} VBoxManage controlvm {} savestate -- runs the save state command on each box that's open.

On xargs

-L1 - take one line at a time
-I {} - uses {} as a place holder for the next command

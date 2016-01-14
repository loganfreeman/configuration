List running machines (returns name and UUID):
---
```shell
VBoxManage list runningvms
vagrant global-status | awk '/running/{print $1}'
```
Stop running VMs by "hibernating" them (reommended to avoid data loss)
---
```shell
VBoxManage controlvm <name|uuid> savestate
```
```shell
vagrant global-status | grep virtualbox | cut -c 1-9 | while read line; do echo $line; vagrant halt $line; done;
```
- global-status lists all boxes
- filter that for lines containing virtualbox (Filters out the help text, will break if you're using some other provider)
- Filter that to display only the first 9 characters (the global unique ID)
- While we can still read a line from that input, read it as the variable $line then:
 - Print out that $line
 - run vagrant halt $line halting the vagrant for that global unique ID
Poweroff running VMs (not recommended because we may lose data in the guest)
---
```shell
VBoxManage controlvm <name|uuid> poweroff
```
$HOME/bin/stop-vagrant.sh
---
```
vboxmanage list runningvms | sed -r 's/.*\{(.*)\}/\1/' | xargs -L1 -I {} VBoxManage controlvm {} savestate
```
Command Explained:

- vboxmanage list runningvms | -- gets a list of all running vms under VirtualBox

- sed -r 's/.*\{(.*)\}/\1/' | -- strips the string down to id number

- xargs -L1 -I {} VBoxManage controlvm {} savestate -- runs the save state command on each box that's open.

On xargs

 - -L1 - take one line at a time
 - -I {} - uses {} as a place holder for the next command

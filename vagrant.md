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

vagrant cannot mount 
---
```shell
vagrant plugin install vagrant-vbguest
# check the symbolic link exists
sudo ln -s /opt/VBoxGuestAdditions-x.x.x/lib/VBoxGuestAdditions /usr/lib/VBoxGuestAdditions
```
use 1/4 system memory
---
```ruby
config.vm.provider "virtualbox" do |v|
  host = RbConfig::CONFIG['host_os']

  # Give VM 1/4 system memory 
  if host =~ /darwin/
    # sysctl returns Bytes and we need to convert to MB
    mem = `sysctl -n hw.memsize`.to_i / 1024
  elsif host =~ /linux/
    # meminfo shows KB and we need to convert to MB
    mem = `grep 'MemTotal' /proc/meminfo | sed -e 's/MemTotal://' -e 's/ kB//'`.to_i 
  elsif host =~ /mswin|mingw|cygwin/
    # Windows code via https://github.com/rdsubhas/vagrant-faster
    mem = `wmic computersystem Get TotalPhysicalMemory`.split[1].to_i / 1024
  end

  mem = mem / 1024 / 4
  v.customize ["modifyvm", :id, "--memory", mem]
end
```
To enable NFS, add the following to your Vagrantfile:
---
```ruby
# Required for NFS to work, pick any local IP
config.vm.network :private_network, ip: '192.168.50.50'
# Use NFS for shared folders for better performance
config.vm.synced_folder '.', '/vagrant', nfs: true
```
symbolic links
---
> Virtualbox does not allow symlinks on shared folders for security reasons. To enable symlinks the following line needs to be added to the vm provider config block in the Vagrantfile:
```ruby
config.vm.provider "virtualbox" do |v|
    v.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/v-root", "1"]
end
```
You need to boot up the Vagrant machine in Administrator mode.

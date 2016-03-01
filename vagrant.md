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

vboxadd
---
```ruby
      # Checks for the correct location of the 'vboxadd' tool.
      # It checks for a given list of possible locations. This list got
      # extracted from the 'VBoxLinuxAdditions.run' script.
      #
      # @return [String|nil] Absolute path to the +vboxadd+ tool,
      #                      or +nil+ if none found.
      def vboxadd_tool
        candidates = [
          "/usr/lib/i386-linux-gnu/VBoxGuestAdditions/vboxadd",
          "/usr/lib/x86_64-linux-gnu/VBoxGuestAdditions/vboxadd",
          "/usr/lib64/VBoxGuestAdditions/vboxadd",
          "/usr/lib/VBoxGuestAdditions/vboxadd",
          "/lib64/VBoxGuestAdditions/vboxadd",
          "/lib/VBoxGuestAdditions/vboxadd",
          "/etc/init.d/vboxadd",
        ]
        bin_path = ""
        cmd = <<-SHELL
        for c in #{candidates.join(" ")}; do
          if test -x "$c"; then
            echo $c
            break
          fi
        done
        SHELL

        path = nil
        communicate.sudo(cmd, {:error_check => false}) do |type, data|
          path = data.strip unless data.empty?
        end
        path
      end
```

multi-VM environment setup
---
```ruby
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
	numNodes = 4
	r = numNodes..1
	(r.first).downto(r.last).each do |i|
		config.vm.define "node-#{i}" do |node|
			node.vm.box = "centos65"
			node.vm.box_url = "http://files.brianbirkinbine.com/vagrant-centos-65-i386-minimal.box"
			node.vm.provider "virtualbox" do |v|
			  v.name = "node#{i}"
			  v.customize ["modifyvm", :id, "--memory", "1024"]
			end
			if i < 10
				node.vm.network :private_network, ip: "10.211.55.10#{i}"
			else
				node.vm.network :private_network, ip: "10.211.55.1#{i}"
			end
			node.vm.hostname = "node#{i}"
			node.vm.provision "shell", path: "scripts/setup-centos.sh"
			node.vm.provision "shell" do |s|
				s.path = "scripts/setup-centos-hosts.sh"
				s.args = "-t #{numNodes}"
			end
			if i == 2
				node.vm.provision "shell" do |s|
					s.path = "scripts/setup-centos-ssh.sh"
					s.args = "-s 3 -t #{numNodes}"
				end
			end
			if i == 1
				node.vm.provision "shell" do |s|
					s.path = "scripts/setup-centos-ssh.sh"
					s.args = "-s 2 -t #{numNodes}"
				end
			end
			node.vm.provision "shell", path: "scripts/setup-java.sh"
			node.vm.provision "shell", path: "scripts/setup-hadoop.sh"
			node.vm.provision "shell" do |s|
				s.path = "scripts/setup-hadoop-slaves.sh"
				s.args = "-s 3 -t #{numNodes}"
			end
			node.vm.provision "shell", path: "scripts/setup-spark.sh"
			node.vm.provision "shell" do |s|
				s.path = "scripts/setup-spark-slaves.sh"
				s.args = "-s 3 -t #{numNodes}"
			end
		end
	end
end
```

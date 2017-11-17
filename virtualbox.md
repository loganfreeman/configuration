How do I install VirtualBox guest additions for CentOS 7?
---
```
$ cat /var/log/vboxadd-install.log
/tmp/vbox.0/Makefile.include.header:97: *** Error: unable to find the \\
sources of your current Linux kernel. \\
Specify KERN_DIR=<directory> and run Make again.  Stop.
```

Your install.log file is very clear: you need to install the headers,

```
sudo yum update
sudo yum install kernel-headers kernel-devel
```

[vagrant-vbguest](https://github.com/dotless-de/vagrant-vbguest)
---
```
vagrant plugin install vagrant-vbguest
```

When you switched off the middleware auto update, or you have a box up and running you may also run the installer manually.
```
vagrant vbguest [vm-name] [--do start|rebuild|install] [--status] [-f|--force] [-b|--auto-reboot] [-R|--no-remote] [--iso VBoxGuestAdditions.iso] [--no-cleanup]
```

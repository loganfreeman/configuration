Where VirtualBox stores its files
---
By default, this "machine folder" is placed in a common folder called "VirtualBox VMs", which VirtualBox creates in the current system user's home directory. The location of this home directory depends on the conventions of the host operating system:

On Windows, this is the location returned by the `SHGetFolderPath` function of the Windows system library Shell32.dll. 

Only on very old Windows versions which don't have this function or where it unexpectedly returns an error, there is a fallback based on environment variables: first `%USERPROFILE%` is checked, if it doesn't exist then an attempt with `%HOMEDRIVE%%HOMEPATH%` is made. Typical value is C:\Users\username.

On Linux, Mac OS X and Solaris, this is generally taken from the environment variable `$HOME`, except for the user root for which it's taken from the account database (as a workaround for the frequent trouble caused by users using VirtualBox in combination with the tool sudo which by default doesn't reset the environment variable $HOME). Typical value on Linux and Solaris is /home/username and on Mac OS X /Users/username.

For simplicity, we will abbreviate this as $HOME below. Using that convention, the common folder for all virtual machines is $HOME/VirtualBox VMs.

You can change the default machine folder by selecting "Preferences" from the "File" menu in the VirtualBox main window. Then, in the window that pops up, click on the "General" tab. Alternatively, use `VBoxManage setproperty machinefolder`;


[Global configuration data](https://www.virtualbox.org/manual/ch10.html#vboxconfigdata)

 Optionally, you can supply an alternate configuration directory by setting the `VBOX_USER_HOME` environment variable.
 
 


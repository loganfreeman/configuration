[SELinux Contexts – Labeling Files](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Security-Enhanced_Linux/sect-Security-Enhanced_Linux-Working_with_SELinux-SELinux_Contexts_Labeling_Files.html)
---
On systems running SELinux, all processes and files are labeled in a way that represents security-relevant information. This information is called the SELinux context. For files, this is viewed using the `ls -Z` command



[SELinux Types](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Confined_Services/sect-Managing_Confined_Services-The_Apache_HTTP_Server-Types.html)
---
`httpd_sys_content_t`

Use this type for static web content, such as .html files used by a static website. Files labeled with this type are accessible (read only) to httpd and scripts executed by httpd. By default, files and directories labeled with this type cannot be written to or modified by httpd or other processes. Note that by default, files created in or copied into /var/www/html/ are labeled with the httpd_sys_content_t type.




SELinux Access Control
---
SELinux has 3 forms of access control:

1. Type Enforcement (`TE`): Type Enforcement is the primary mechanism of access control used in the targeted policy
2. Role-Based Access Control (`RBAC`): Based around SELinux users (not necessarily the same as the Linux user), but not used in the default targeted policy
3. Multi-Level Security (`MLS`): Not commonly used and often hidden in the default targeted policy.

All processes and files have an SELinux security context. Let's see these in action by looking at the SELinux security context of the Apache homepage: '/var/www/html/index.html'

```
$ ls -Z /var/www/html/index.html  
-rw-r--r--  username username system_u:object_r:httpd_sys_content_t /var/www/html/index.html 
```
In addition to the standard file permissions and ownership, we can see the SELinux security context fields: `system_u:object_r:httpd_sys_content_t`.

This is based upon `user:role:type:mls`. In our example above, `user:role:type` fields are displayed and `mls` is hidden. Within the default `targeted` policy, type is the important field used to implement Type Enforcement, in this case `httpd_sys_content_t`.

Now consider the SELinux security context of the Apache web server process: 'httpd'
```
$ ps axZ | grep httpd
system_u:system_r:httpd_t        3234 ?        Ss     0:00 /usr/sbin/httpd
```

By default SELinux log messages are written to `/var/log/audit/audit.log` via the Linux Auditing System `auditd`, which is started by default. If the `auditd` daemon is not running, then messages are written to `/var/log/messages` . SELinux log messages are labeled with the "AVC" keyword so that they might be easily filtered from other messages, as with grep.

Using Apache as an example, suppose you want to change the DocumentRoot to serve web pages from a location other than the default /var/www/html/ directory.

The 'chcon' command may be used to change SELinux security context of a file or files/directories in a similar way to how 'chown' or 'chmod' may be used to change the ownership or standard file permissions of a file.

```shell
chcon -v --type=httpd_sys_content_t /html
# context of /html changed to user_u:object_r:httpd_sys_content_t
chcon -v --type=httpd_sys_content_t /html/index.html
# context of /html/index.html changed to user_u:object_r:httpd_sys_content_t
ls -Z /html/index.html
# -rw-r--r--  root root user_u:object_r:httpd_sys_content_t    /html/index.html
```

Or recursively
```shell
chcon -Rv --type=httpd_sys_content_t /html 
```

To make the security context changes permanent, even through a complete filesystem relabel, we can use the SELinux Management Tool or the 'semanage' command from the command line:
```shell
semanage fcontext -a -t httpd_sys_content_t "/html(/.*)?" 
```
access check
---
```shell
 sudo -u www-data stat /home/vagrant/grav/user/config/security.yaml
 ```

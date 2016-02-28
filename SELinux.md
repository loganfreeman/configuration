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

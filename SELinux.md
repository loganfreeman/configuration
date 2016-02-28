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

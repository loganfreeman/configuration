Postgresql backups
---
```shell
cat /etc/cron.d/backupfsn.sh
#!/bin/bash
backup_dir="/var/log/"
timeslot=`date +%u`
i=dbnametobackup
/usr/bin/pg_dump -U postgres $i | gzip > "$backup_dir/postgresql-$i-$timeslot-database.gz"
```

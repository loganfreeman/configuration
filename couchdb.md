install couchdb on centos
---
```shell
#!/bin/bash

# Move to a location where you don't mind storing the couchdb install files.
cd  /tmp/

EPEL_FILE=epel-release-latest-7.noarch.rpm
COUCH=apache-couchdb-1.6.1.tar.gz

rm -rf epel-release-latest-7.noarch.rpm

# Enable EPEL and REMI repositories for installing couchdb deps.
wget http://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
rpm -Uvh epel-release-latest-7.noarch.rpm

# Install couchdb dependencies.
yum install  autoconf autoconf-archive automake libtool perl-Test-Harness erlang libicu-devel js-devel curl-devel gcc-c++

# Download and install couchdb.
wget http://apache.mirror.digitalpacific.com.au/couchdb/source/1.6.1/apache-couchdb-1.6.1.tar.gz

tar -xzf $COUCH
cd apache-couchdb-1.6.1
./configure --with-erlang=/usr/lib64/erlang/usr/include
make && make install


# Add couchdb user and proper file ownership and permissions.
adduser -r --home /usr/local/var/lib/couchdb -M --shell /bin/bash --comment "CouchDB Administrator" couchdb
chown -R couchdb:couchdb /usr/local/etc/couchdb
chown -R couchdb:couchdb /usr/local/var/lib/couchdb
chown -R couchdb:couchdb /usr/local/var/log/couchdb
chown -R couchdb:couchdb /usr/local/var/run/couchdb
chmod 0770 /usr/local/etc/couchdb
chmod 0770 /usr/local/var/lib/couchdb
chmod 0770 /usr/local/var/log/couchdb
chmod 0770 /usr/local/var/run/couchdb

# If you want to start couchdb as a daemon on boot.
ln -s /usr/local/etc/rc.d/couchdb /etc/init.d/couchdb
chkconfig --add couchdb
chkconfig --level 345 couchdb on

# get a list of active zones.. you might have more.
firewall-cmd --get-active-zones

# firewalld command to allow this port open to dmz
firewall-cmd --zone=public --add-port=5984/tcp   --permanent

# Restart the firewalld service
firewall-cmd --reload

# Start couchdb as a daemon
/usr/local/etc/rc.d/couchdb start
```

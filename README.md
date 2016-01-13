### The location of the Apache configuration file
- /etc/apache2/httpd.conf
- /etc/apache2/apache2.conf
- /etc/httpd/httpd.conf
- /etc/httpd/conf/httpd.conf

### Relevant Pages in the Passenger Docs
[The Apache version of the Passenger user’s guide](http://www.modrails.com/documentation/Users%20guide%20Apache.html) covers the Passenger-specific configuration directives we use below in much greater detail.

### create a user for the app
```shell
$ sudo adduser myappuser
$ sudo mkdir -p ~myappuser/.ssh
$ sudo sh -c "cat $HOME/.ssh/authorized_keys >> ~myappuser/.ssh/authorized_keys"
$ sudo chown -R myappuser: ~myappuser/.ssh
$ sudo chmod 700 ~myappuser/.ssh
$ sudo sh -c "chmod 600 ~myappuser/.ssh/*"
```

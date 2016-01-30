```shell
echo "install rvm"
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
\curl -sSL https://get.rvm.io | bash -s stable
source /etc/profile.d/rvm.sh
rvm install 2.1.5
rvm use 2.1.5 --default
echo "installing epel-release pygame curl"
yum install -y epel-release pygpgme curl git
echo "Add our el7 YUM repository"
curl --fail -sSLo /etc/yum.repos.d/passenger.repo https://oss-binaries.phusionpassenger.com/yum/definitions/el-passenger.repo
echo "Install Passenger + Apache module"
yum install -y mod_passenger
echo "restart apache service"
systemctl restart httpd
echo "validate passenger install"
passenger-config validate-install
echo "install qtwebkit devel"
yum install qtwebkit-devel
echo "add qmake to your path"
ln -s /usr/lib64/qt4/bin/qmake /usr/bin/qmake
echo "Install Node from the EPEL Repository"
yum install nodejs
echo "install mysql-devel"
yum install mysql-devel
adduser capistrano
usermod -a -G rvm capistrano
if [ ! -d "/etc/httpd/sites-available" ]; then
  mkdir /etc/httpd/sites-available
fi
if [ ! -d "/etc/httpd/sites-enabled" ]; then
  mkdir /etc/httpd/sites-enabled
fi
if grep -q "IncludeOptional sites-enabled/\*.conf" "/etc/httpd/conf/httpd.conf"; then
  echo "IncludeOptional sites-enabled/*.conf" >> "/etc/httpd/conf/httpd.conf"
fi
cd
su capistrano && cd
git clone https://github.com/uofu-ccts/sparc-request.git sparc-request
exit
```

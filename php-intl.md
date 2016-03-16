The intl extension is an internationalization extension (referred as Intl) that is a wrapper for ICU library, enabling PHP programmers to perform UCA-conformant collation and date/time/number/currency formatting in their scripts. 

```shell
brew update
brew search icu
brew install icu4c
```
Before you install intl you have to install `Autoconf` if you don’t have installed it yet. In your terminal execute following commands:

```shell
cd ~ && curl -O http://ftp.gnu.org/gnu/autoconf/autoconf-latest.tar.gz 
tar -zxvf autoconf-latest.tar.gz 
cd autoconf-2.69 
./configure 
make 
sudo make install
```

The following instructions install PEAR and PECL on Mac OS X under /usr/local/. PECL is bundled with PEAR. 
```shell
curl -O http://pear.php.net/go-pear.phar
sudo php -d detect_unicode=0 go-pear.phar
sudo pecl install intl
sudo cp /private/etc/php.ini{.default,}
sudo chmod 644 /private/etc/php.ini
echo extension=intl.so >> /usr/local/etc/php/5.5/php.ini
```

When prompting for icu directory, enter the icu directory: `/usr/local/Cellar/icu4c/56.1/`

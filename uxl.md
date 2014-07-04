
# Diskplass

Har utvidet var til 14GB, også har jeg meldt inn den ekstradisken på 50 GB i volumgruppen internvg slik at dere i fremtiden selv kan få utvidet det dere måtte trenge ved å kjøre følgende:
```bash
lvresize -L +10G /dev/mapper/internvg-var
resize2fs /dev/mapper/internvg-var
```

```bash
$ cat /etc/hosts.allow
# Allow everything from localhost
ALL: localhost 127.0.0.1 [::1]

# Allow everything from UiO
ALL: 129.240. .uio.no [2001:700:100::]/40
```

cat /etc/redhat-release
Red Hat Enterprise Linux Server release 6.5 (Santiago)
```

sudo yum list installed | grep php
Failed to set locale, defaulting to C
This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
php.x86_64                           5.3.3-27.el6_5        @rhel-x86_64-server-6
php-cli.x86_64                       5.3.3-27.el6_5        @rhel-x86_64-server-6
php-common.x86_64                    5.3.3-27.el6_5        @rhel-x86_64-server-6
php-gd.x86_64                        5.3.3-27.el6_5        @rhel-x86_64-server-6


sudo yum list installed | grep uio
Failed to set locale, defaulting to C
This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
laptop-detect.noarch                 0.13.7-4.el6          @uio-free           
perl-Sys-Filesystem.noarch           1.400-1.el6           @uio-free           
                                                           @uio-free           
uio-cfengine.x86_64                  3.5.3-2.el6           @uio-free           
uio-nagios-plugins.x86_64            0.17-1.el6            @uio-free           
uio-priss-clients.x86_64             1.0.2-1.el6           @uio-free           
uio-release.noarch                   6-2                   @/uio-release-6-2.noarch
uio-selinux.noarch                   0.8-1.el6             @uio-free           
uio-selinux-mail.noarch              0.8-1.el6             @uio-free           
uio-server-utils.noarch              0.20-1.el6            @uio-free           
uio-server-utils-dell.noarch         0.20-1.el6            @uio-free           
uio-server-utils-hp.noarch           0.20-1.el6            @uio-free           
uio-shell-settings.noarch            0.3-1.el6             @uio-free           
uio-ssl-filelog.noarch               0.6.1-3.el6           @uio-free           
uio-ssl-filelog-server.noarch        0.6.1-3.el6           @uio-free           
uio-tools.noarch                     0.25-1.el6            @uio-free           
uio-tsm-check.noarch                 1.4.4-1.el6           @uio-free


List the contents of a package

repoquery -lq uio-tools.noarch


What packages provides a given command?

sudo yum whatprovides setlocale



Installere nyere PHP:

we can find many rpm packages on the Internet. However, they all conflict with the php which comes with CentOS, so, we’d better build and install them from soure, this is not difficult, the point is to install php at different location.

sudo yum update


sudo yum install -y gcc git screen libxml2-devel bzip2-devel zlib-devel curl-devel libmcrypt-devel libjpeg-devel libpng-devel gd-devel mysql-devel postgresql-devel openldap-devel expat-devel libtool libtool-ltdl 

mkdir tmp
cd tmp
wget http://no1.php.net/get/php-5.3.28.tar.bz2/from/this/mirror
mv mirror.1 php-5.3.28.tar.bz2
tar -xvf php-5.3.28.tar.bz2
cd php-5.3.28


Following http://serverfault.com/a/214735

wget http://dl.iuscommunity.org/pub/ius/stable/Redhat/6/x86_64/epel-release-6-5.noarch.rpm
wget http://dl.iuscommunity.org/pub/ius/stable/Redhat/6/x86_64/ius-release-1.0-11.ius.el6.noarch.rpm
sudo rpm -Uvh ius-release*.rpm epel-release*.rpm

repo id                            repo name                                                                     status
epel                               Extra Packages for Enterprise Linux 6 - x86_64                                10662
ius                                IUS Community Packages for Enterprise Linux 6 - x86_64                          259
uio-free                           UiO Free Packages for RHEL6 - x86_64                                            628
uio-nonfree                        UiO Non-Free Packages for RHEL6 - x86_64                                        212
vmware-tools                       VMware Tools 5.0 - x86_64                                                        46




cd /usr/lib64
sudo ln -s libltdl.so.7 libltdl.so

./configure --prefix=/usr/local/php53 \
     --with-config-file-path=/etc/php53 \
     --with-config-file-scan-dir=/etc/php53/php.d \
     --with-libdir=lib64 \
     --enable-mbstring \
     --disable-debug \
     --disable-rpath \
     --with-bz2 \
     --with-curl \
     --with-gettext \
     --with-iconv \
     --with-openssl \
     --with-gd \
     --with-mcrypt \
     --with-pcre-regex \
     --with-zlib \
     --enable-mbstring=all \
     --enable-mbregex \
     --enable-calendar \
     --enable-cli \
     --enable-zip \
     --enable-exif \
     --disable-cgi \
     --disable-short-tags \
     --with-curl=/usr/lib64 \
     --with-freetype-dir=/usr/lib64 \
     --with-gd \
     --with-gettext \
     --with-jpeg-dir=/usr/lib64 \
     --with-ldap \
     --with-libexpat-dir=/usr/lib64 \
     --with-libxml-dir=/usr/lib64 \
     --with-openssl \
     --with-pgsql \
     --with-png-dir=/usr/lib64 \
     --with-zlib \
     --with-pdo-pgsql \
     --with-iconv \
     --with-mcrypt

make
make test
(a few tests failed)

sudo make install
Installing PHP CLI binary:        /usr/local/php53/bin/
Installing PHP CLI man page:      /usr/local/php53/man/man1/
Installing build environment:     /usr/local/php53/lib/php/build/
Installing header files:          /usr/local/php53/include/php/
Installing helper programs:       /usr/local/php53/bin/
  program: phpize
  program: php-config
Installing man pages:             /usr/local/php53/man/man1/
  page: phpize.1
  page: php-config.1
Installing PEAR environment:      /usr/local/php53/lib/php/
[PEAR] Archive_Tar    - installed: 1.3.11
[PEAR] Console_Getopt - installed: 1.3.1
warning: pear/PEAR requires package "pear/Structures_Graph" (recommended version 1.0.4)
warning: pear/PEAR requires package "pear/XML_Util" (recommended version 1.2.1)
[PEAR] PEAR           - installed: 1.9.4
Wrote PEAR system config file at: /usr/local/php53/etc/pear.conf
You may want to add: /usr/local/php53/lib/php to your php.ini include_path
[PEAR] Structures_Graph- installed: 1.0.4
[PEAR] XML_Util       - installed: 1.2.1
/home/dmheggo/tmp/php-5.3.28/build/shtool install -c ext/phar/phar.phar /usr/local/php53/bin
ln -s -f /usr/local/php53/bin/phar.phar /usr/local/php53/bin/phar
Installing PDO headers:          /usr/local/php53/include/php/ext/pdo/


cd /usr/local/bin
sudo ln -s /usr/local/php53/bin/php php53





For å bruke php 5.3.28 fremfor 5.3.3 som standard:


echo 'path_prepend /usr/local/php53/bin/' >> .dotfiles/uxl
echo 'uxl' >> .hostname
cd .dotfiles
git commit -m "Add uxl"
git pull
git push


Installere composer:

curl -sS https://getcomposer.org/installer | sudo /usr/local/php53/bin/php -- --install-dir=/usr/local/bin/ --filename=composer

#!/usr/bin/env php
All settings correct for using Composer
Downloading...

Composer successfully installed to: /usr/local/bin/composer
Use it: php /usr/local/bin/composer

Installere nodejs:

Nyeste utgave er i epel, så:

sudo yum install nodejs

2014-06-06 Baaah, vi fjerner PHP 5.3 og legger inn 5.5

sudo yum whatprovides php55
sudo yum remove php53u-5.3.28-4.ius.el6.x86_64
sudo yum remove php53u-cli-5.3.28-4.ius.el6.x86_64
sudo yum list installed | grep php
sudo yum remove php53u-common.x86_64 php53u-gd.x86_64 php53u-imap.x86_64 php53u-ldap.x86_64 php53u-mysql.x86_64 php53u-pdo.x86_64 php53u-pgsql.x86_64 php53u-xml.x86_64
sudo yum install php55u-5.5.12-2.ius.el6.x86_64
sudo yum install php55-php-gd.x86_64 php55-php-mbstring.x86_64 php55-php-xml.x86_64 php55u-pecl-mongo.x86_64

Following http://docs.mongodb.org/manual/tutorial/install-mongodb-on-red-hat-centos-or-fedora-linux/
Create /etc/yum.repos.d/mongodb.repo  and 


[mongodb]name=MongoDB Repositorybaseurl=http://downloads-distro.mongodb.org/repo/redhat/os/x86_64/gpgcheck=0enabled=1

sudo yum install mongodb-org

sudo service mongod start

Tomt for diskplass på /var


df -k
Filesystem           1K-blocks    Used Available Use% Mounted on
/dev/mapper/internvg-root
                      44381296 6123420  36003964  15% /
tmpfs                   959228       0    959228   0% /dev/shm
/dev/sda1               253871   91113    149651  38% /boot
/dev/mapper/internvg-opt
                       1032088  235792    743868  25% /opt
/dev/mapper/internvg-tmp
                       1032088   34072    945588   4% /tmp
/dev/mapper/internvg-usr
                       4128448 2605636   1313100  67% /usr
/dev/mapper/internvg-var
                       4128448  666288   3252448  18% /var


2014-06-06 Baah

Usikker på hva som er best. Flere og flere php-pakker krever PHP 5.4, noe som utelukker kjøring på app.uio.no. Kan gjøre fra egen server kanskje, men får vi da Feide-pålogging på en enkel måte? Alternativt holde seg på 5.3, men da må jeg antakelig drive og patche pakker manuelt hvis det kommer viktige oppdateringer :( :(

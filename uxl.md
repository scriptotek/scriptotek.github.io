
# Grunnleggende

```bash
$ cat /etc/redhat-release
Red Hat Enterprise Linux Server release 6.5 (Santiago)
```

## Diskplass

Fra USIT: Har utvidet `/var` til 14GB, også har jeg meldt inn den ekstradisken på 50 GB i volumgruppen internvg slik at dere i fremtiden selv kan få utvidet det dere måtte trenge ved å kjøre følgende:
```bash
$ lvresize -L +10G /dev/mapper/internvg-var
$ resize2fs /dev/mapper/internvg-var
```

Sjekke diskplass:
```bash
$ df -kh
Filesystem            Size  Used Avail Use% Mounted on
/dev/mapper/internvg-root
                       43G  5.9G   35G  15% /
tmpfs                 937M     0  937M   0% /dev/shm
/dev/sda1             248M   85M  151M  36% /boot
/dev/mapper/internvg-opt
                     1008M  231M  727M  25% /opt
/dev/mapper/internvg-tmp
                     1008M   34M  924M   4% /tmp
/dev/mapper/internvg-usr
                      4.0G  2.6G  1.3G  68% /usr
/dev/mapper/internvg-var
                       14G  671M   13G   6% /var
```
Hovedpartisjonen er den øverste, på 43G.

## IP-begrensning

```bash
$ cat /etc/hosts.allow
# Allow everything from localhost
ALL: localhost 127.0.0.1 [::1]

# Allow everything from UiO
ALL: 129.240. .uio.no [2001:700:100::]/40
```

## Useful commands

List the contents of a package

```bash
$ repoquery -lq uio-tools.noarch
```

What packages provides a given command?

```bash
$ sudo yum whatprovides setlocale
```

# Programvare

Basics

```bash
$ sudo yum update

$ sudo yum install -y gcc git screen libxml2-devel bzip2-devel zlib-devel curl-devel libmcrypt-devel libjpeg-devel libpng-devel gd-devel mysql-devel postgresql-devel openldap-devel expat-devel libtool libtool-ltdl 
```

## PHP

Pre-installert:

```bash
$ sudo yum list installed | grep php
Failed to set locale, defaulting to C
This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
php.x86_64                           5.3.3-27.el6_5        @rhel-x86_64-server-6
php-cli.x86_64                       5.3.3-27.el6_5        @rhel-x86_64-server-6
php-common.x86_64                    5.3.3-27.el6_5        @rhel-x86_64-server-6
php-gd.x86_64                        5.3.3-27.el6_5        @rhel-x86_64-server-6
```

Following [http://serverfault.com/a/214735](http://serverfault.com/a/214735),

```bash
$ wget http://dl.iuscommunity.org/pub/ius/stable/Redhat/6/x86_64/epel-release-6-5.noarch.rpm
$ wget http://dl.iuscommunity.org/pub/ius/stable/Redhat/6/x86_64/ius-release-1.0-11.ius.el6.noarch.rpm
$ sudo rpm -Uvh ius-release*.rpm epel-release*.rpm

repo id                            repo name                                                                     status
epel                               Extra Packages for Enterprise Linux 6 - x86_64                                10662
ius                                IUS Community Packages for Enterprise Linux 6 - x86_64                          259
uio-free                           UiO Free Packages for RHEL6 - x86_64                                            628
uio-nonfree                        UiO Non-Free Packages for RHEL6 - x86_64                                        212
vmware-tools                       VMware Tools 5.0 - x86_64                                                        46
```

2014-06-06 Baaah, fjerner PHP 5.3 og legger inn 5.5

```bash
$ sudo yum whatprovides php55
$ sudo yum remove php53u-5.3.28-4.ius.el6.x86_64
$ sudo yum remove php53u-cli-5.3.28-4.ius.el6.x86_64
$ sudo yum list installed | grep php
$ sudo yum remove php53u-common.x86_64 php53u-gd.x86_64 php53u-imap.x86_64 php53u-ldap.x86_64 php53u-mysql.x86_64 php53u-pdo.x86_64 php53u-pgsql.x86_64 php53u-xml.x86_64
$ sudo yum install php55u-5.5.12-2.ius.el6.x86_64
$ sudo yum install php55-php-gd.x86_64 php55-php-mbstring.x86_64 php55-php-xml.x86_64 php55u-pecl-mongo.x86_64
```

Merk at det er PHP 5.3. som kjører på app.uio.no, og USIT har ingen planer om å oppgradere, så vi bør beholde PHP 5.3-kompabilitet hvis det er mulig. Flere og flere pakker på Packagist krever imidlertid 5.4, så det er litt upraktisk...


Og legger inn composer

```bash
$ curl -sS https://getcomposer.org/installer | sudo /usr/bin/php -- --install-dir=/usr/local/bin/ --filename=composer
```


## nodejs:

Nyeste utgave er i `epel`, som vi har lagt til, så

```
$ sudo yum install nodejs
```

## mongodb

Following http://docs.mongodb.org/manual/tutorial/install-mongodb-on-red-hat-centos-or-fedora-linux/
Create `/etc/yum.repos.d/mongodb.repo` with 

```
[mongodb]name=MongoDB Repositorybaseurl=http://downloads-distro.mongodb.org/repo/redhat/os/x86_64/gpgcheck=0enabled=1
```
Then
```
sudo yum install mongodb-org
sudo service mongod start
```

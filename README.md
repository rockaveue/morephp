# TOC

1. [PHP Frameworks](#php-frameworks)
1. [Configuring PHP-FPM for amazon-linux-2](#Configuring-PHP-FPM-for-amazon-linux-2)

# PHP Frameworks

1. [Laravel](#laravel)
1. [Yii](#yii)
1. [Symfony](#symfony)
1. [Zend](#zend)
1. [Cake](#cake)

## Laravel

Сайн document болон community-тэй. Blade template-тэй учир үүн дотроо php ашиглаж болдог. Eloquent ORM.

Complex backend-тэй төсөл хөгжүүлэхэд тохиромжтой. Шинээр гарч ирсэн homestead нь vagrant ашигладаг.

### Requirement

PHP >= 5.4, PHP < 7
Laravel 8: PHP >= 7.2.5
Composer ашиглана

#### DB

MySQL 5.6+, PostgreSQL 9.4+, SQLite 3.8.8+, SQL Server 2017+.

## Symfony

Том хэмжээний төсөл хийхэд тохиромжтой. Олон дахин ашиглах боломжтой PHP components гэж байдаг. Laravel-тэй төстэй.

### Requirements

PHP >= 7.25

Composer ашиглана

#### DB

Drizzle, MySQL, Oracle, PostgreSQL, SAP Sybase SQL Anywhere, SQLite, SQLServer

## CodeIgniter

Маш жижигхэн. Анхлан суралцагчдад тохиромжтой. Хурдан. Жижиг хэмжээний scalable app хийх боломжтой.

### Requirements

PHP >= 7.2

#### DB

MySQL, PostgreSQL, SQLite3

## Zend

Том IT компани, банк зэрэг ашиглахад тохиромжтой.

### Requirements

Composer ашиглана

PHP >= 5.6

MariaDB, MySQL, Oracle, IBM DB2, Microsoft SQL Server, PostgreSQL, SQLite, and Informix Dynamic Server.

## Yii

Documentation болон community сайтай, суулгахад хялбар.

E-commerce, CMS, portal, forum зэргийг хийхэд илүү тохиромжтой.

SQL Injection, XSS, CSRF халдлагуудаас хамгаалах best practice ихтэй

Cata caching, fragment caching, page caching, HTTP caching гэсэн 4-н кээшлэлтийн аргатай.

### Requirements

PHP >= 5.4.0, 7+ recommended

Composer ашиглана

#### DB

SQLite, MySQL, PostgreSQL, MSSQL, Oracle databases

## Cake

Хэрэглэж, ашиглахад маш амар. Хөгжүүлэлт хийхэд хурдан өөрийн гэсэн bake command line tool байдаг

### Requirements

PHP 7.2 (minimum), 7.4 recommended

#### DB

MySQL 5.6+, MariaDB 5.6+, PostgreSQL 9.4+, SQLite 3.8, SQL Server 2012+


# Configuring PHP-FPM for amazon-linux-2

[comment]: # (**libapache2-mod-fcgid package alternatives**)

[comment]: # (http://213.146.141.13/package/details/Ubuntu1604/ubuntu_xenial_universe/x86-64/libapache2-mod-fcgid/2.3.9)

[php configuration source for fpm](https://habibza.in/install-apache-php-fpm-centos-7/)

[comment]: # (load module mod_proxy_fcgi /etc/httpd/conf.module.d)
1. install php and php-fpm

1. Tell apache to send all php requests over to php-fpm

add code below to /etc/httpd/conf.d/reward_admin.con
```
<Proxy "unix:/var/run/php-fpm/php-fpm.sock|fcgi://php-fpm">
	# we must declare a parameter in here (doesn't matter which) or it'll not register the proxy ahead of time
    	ProxySet disablereuse=off
</Proxy>

# Redirect to the proxy
<FilesMatch \.php$>
	SetHandler proxy:fcgi://php-fpm
</FilesMatch>

<VirtualHost *:80>
    ServerAdmin admin@example.com
    DocumentRoot /var/www/html

    ServerName example.com
    ServerAlias example.com

    ErrorLog logs/admin-error.log
    CustomLog logs/admin-access.log combined

    <Directory /var/www/html/>
    AllowOverride All
    Require all granted
    </Directory>
</VirtualHost>
```

3. [configuration for fpm.sock](https://stackoverflow.com/a/35245008)

change code below in /etc/php-fpm.d/www.conf
```
listen = /var/run/php-fpm/default.sock
listen.owner = vagrant
listen.group = vagrant
listen.mode = 0666
user = apache
group = apache
```

4. `sudo systemctl start php-fpm.service`

1. `sudo systemctl restart httpd.service`
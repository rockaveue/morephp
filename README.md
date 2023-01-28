# TOC

1. [PHP Frameworks](#php-frameworks)
1. [Configuring PHP-FPM for amazon-linux-2](#configuring-PHP-FPM-for-amazon-linux-2)
1. [Apache-ийг дэмждэг PHP handler-ууд](#Apache-ийг-дэмждэг-PHP-handler-ууд)

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


# Apache-ийг дэмждэг PHP handler-ууд

1. CGI (`mod_cgi/mod_cgid`)
1. suPHP (`mod_suphp`)
1. Apache Module/DSO (`mod_php`)
1. FCGI/FastCGI (`mod_fcgid`)
1. LSAPI (`mod_lsapi`)
1. PHP-FPM

### PHP-CGI

Хамгийн анхны handler-уудын нэг. PHP скриптийг ажиллуулахдаа default `apache` болон `nobody` user-ээр биш `owner`-р ажиллуулна. Давуу тал нь кодыг ажиллуулахдаа вэб серверээс тусд нь байлгадаг. Жишээ нь алдаатай PHP скрипт ажиллахад бусад файлын аюулгүй байдал эсвэл domain зэрэгт нөлөөлөхгүй. PHP кодыг ажиллуулах бүрт spawn-аар шинэ процесс үүсгэхийг шаарддаг учир ачаалал ихтэй вэбийн хувьд их resource ашиглах боломжтой.

**Давуу тал**

- FPM-тэй харьцуулахад илүү security-тэй учир нь PHP кодыг вэб серверээс тусд нь ажиллуулдаг.
- suEXEC тэй хослуулахад user PHP-ийн хүсэлтүүдийг харах боломжтой.

**Сул тал**

- Маш хуучирсан
- Ачаалал ихтэй app ажиллуулах тохиромжгүй
- Apache directives-үүдийг ашиглах боломжгүй. `php.ini` хэрэгтэй
- Аюулгүй байхын тулд `mpm-itk` шаардлагатай
- Хүсэлт бүр дээр шинэ процесс үүсгэж бүх модулууд болон тохиргоог ажиллуулдаг.

### Apache Module/mod_php/DSO

Apache-ийн модул байдлааг ашиглагддаг. Бүх хүсэлт/thread-үүд php модулийг хуулдаг. CSS болон зураг үзэх хүсэлтэд хүртэл модулийг ашигладаг. Уул нь эдгээр хүсэлтүүдэд php хэрэггүй ч apache мэдэхгүй тул модулийг ашиглана. 

**Давуу тал**

- Маш хурдан handler-уудын нэг
- mod_ruid2 болон mpm_itk модулуудтай ажилладаг
- PHP cache ашиглах боломжтой
- `.htaccess`-д `php_flag/php_value`-р php directive тохируулах боломжтой
- Бага CPU, Memory зарцуулалттай

**Сул тал**

- HTTP/2-ийг дэмжихгүй
- Скриптүүд нь owner биш `apache` эсвэл `nobody` user-ээр дуудагдана.
- Аль user их resource ашиглаад байгааг мэдэх боломжгүй.(`mod_ruid2`-ийг ажиллуулаагүй бол)
- Сервер дээр ганц user байхад л тохиромжтой

### FCGI

<!--FCGI эсвэл FastCGI нь suPHP-тэй харьцуулахад -->

**Давуу тал**

- Бага CPU зарцуулалттай
- PHP процессуудыг cPanel user-р ажиллуулна
- Cache ашиглах боломжтой
- DSO-той төстэй

**Сул тал**

- Memory зарцуулалт их
- PHP скрипт яаж бичигдсэнээс хамаарч алдааны массив үүснэ
- Хүсэлт ирэх үед ажиллахгүй, харин байнга ажиллаж байдаг.

### PHP-FPM

Apache болон бусад nginx гэх мэт вэб серверийг дэмжинэ. PHP-FPM нь FastCGI-ийн сайжруулсан хувилбар бөгөөд процесс байдлаар ажиллана. Apache зөвхөн php файлтай ажиллах үедээ уг процесс луу холболт хийгдэнэ. mod_php тэй харьцуулахад маш бага memory ашиглах юм. Тохиргоо хийхэд хүртэл mod_php-ээс илүү амар бөгөөд, илүү эрх мэдэл өгнө.

Процесс учир өөр портууд дээр олныг ажиллуулж хэд хэдэн PHP хувилбартай зэрэг ажиллах боломжтой.

**Давуу тал**

- Скриптүүд нь тухайн domain, subdomain дахь user-ээр дуудагдана
- Маш хурдан
- PHP opcode caching-тай ажиллах боломжтой
- Ачаалалтай вэбсайт дээр ашиглахад тохиромжтой, resource бага ашигладаг
- Олон веб серверүүдийг дэмждэг

**Сул тал**

- .htaccess файлд зарим тохиргоог оруулах боломжгүй
- PHP-CGI-тай харьцуулахад security бага
- PHP-CGI-тай харьцуулахад их тохиргоо шаардана
- Жижиг серверт тохиромжгүй
- Тохиргоог сайн хийгээгүй бол үзүүлэлтэд нөлөөлнө


## References

- [All php handlers](https://www.knownhost.com/kb/php-handlers-and-what-they-are/)
- [All php handlers2](https://www.hostdime.com/blog/php-handlers/)
- [Apache mod_php vs php-fpm](https://www.reddit.com/r/PHPhelp/comments/gqszb0/php_fpm_vs_apache_mod_php/)
- [php-cgi vs php-fpm](https://www.basezap.com/difference-php-cgi-php-fpm/)
- [mod_php vs fcgi vs fpm](https://www.devguide.at/en/backend/mod_php-vs-fastcgi-vs-fpm/)
- [php fpm configurations](https://www.php.net/manual/en/install.fpm.configuration.php)
- [Referential issue for configuring php-fpm](#23)

# LAMP



## 百度百科

LAMP是指一组通常一起使用来运行动态网站或者服务器的自由软件名称首字母缩写。

- Linux，操作系统
- Apache，网页服务器
- MariaDB或MySQL，数据库管理系统（或者数据库服务器）
- PHP、Perl或Python，脚本语言

一组常用来搭建动态网站或者服务器的开源软件，本身都是各自独立的程序，但是因为常被放在一块使用，拥有了越来越高的兼容度，共同组成了一个强大的 Web 应用服务平台。

虽然这些开放源代码程序本身并不是专门设计成同另几个程序一起工作的，但由于它们的廉价和普遍，这个组合开始流行（大多数Linux发行版本捆绑了这些软件）。

当一起使用的时候，它们表现的像一个具有活力的“解决方案包”（Solution Packages）。其他的方案包有苹果的WebObjects（最初是应用服务器），Java/J2EE和微软的.NET架构。

由于IT世界众所周知的对缩写的爱好，Kunze提出“LAMP”这一容易被市场接受的术语来普及自由软件的使用。 并且该软件开发的项目在软件方面的投资成本较低，因此受到了整个 IT 界的关注。从网站的流量上来说，70% 以上的访问流量是 LAMP 提供的，LAMP 是最强大的网站解决方案。



## 搭建

yum安装

```mysql
yum -y install httpd mysql mysql-server php php-mysql
```

整合 apache 和 php

```mysql
cd /etc/httpd/conf
cp httpd.conf httpd.conf.bak
vim httpd.conf

401 #
402 DirectoryIndex index.php index.html index.html.var
403 
 
780 AddType application/x-gzip .gz .tgz
781 AddType application/x-httpd-php .php
782 
```

重启 Apache

```mysql
service httpd restart
```

创建 PHP 测试页

```mysql
cd /var/www/html/
vim info.php

 1 <?php
 2 phpinfo();
 3 ?>
```

放行 80 端口，修改 selinux 为警告模式，访问 Apache ，出现 PHP 测试页面即为成功



## 电商项目

上传电商压缩包至网站根目录

```mysql
cd /var/www/html
rz
unzip tinyshopV2.5_data.zip 
chmod -R 777 /var/www/html
```

进去数据库，创建库，授权用户

```mysql
mysql> create database ds charset utf8;
Query OK, 1 row affected (0.00 sec)

mysql> grant all on ds.* to 'mike'@'10.0.0.%' identified by '123';
Query OK, 0 rows affected (0.00 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```

![img](file:///C:\Users\Henry\AppData\Local\Temp\ksohtml8700\wps1.jpg)

输出库地址为：MySQL 服务端地址 或者 VIP 地址
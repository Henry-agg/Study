# **mysql**

## **rpm 安装**



删除机子自带的mysql

```shell
yum -y remove mysql*
```



拖入mysql-server，mysql-client rpm包

![img](G:/%E8%BD%AF%E4%BB%B6/%E7%BD%91%E6%98%93%E4%BA%91%E6%9C%89%E9%81%93/henry_dzyj@126.com/88a44ba77790492dbe76e3f401091bc8/clipboard.png)



安装mysql-server包

```shell
rpm -ivh MySQL-server-5.6.45-1.el6.x86_64.rpm
```



出现错误

```shell
ERROR! The server quit without updating PID file (/var/lib/mysql/localhost.localdomain.pid).
```

解决方法

```shell
rm -rf /var/lib/mysql/lib*
/etc/init.d/mysql restart
```



启动服务

```shell
service mysql restart
```



查看进程

```shell
ps -ef |grep mysql
```

![img](G:/%E8%BD%AF%E4%BB%B6/%E7%BD%91%E6%98%93%E4%BA%91%E6%9C%89%E9%81%93/henry_dzyj@126.com/455e7c8b341b4e5cb3313fdb97afa7fd/clipboard.png)



查看端口

```shell
ss -lntp |grep mysql
```

![img](G:/%E8%BD%AF%E4%BB%B6/%E7%BD%91%E6%98%93%E4%BA%91%E6%9C%89%E9%81%93/henry_dzyj@126.com/87aede767e0d4e41b2fc59de7f542bd8/clipboard.png)





## **源码 安装**



mysql 官网

<https://www.mysql.com/downloads/>



安装前的准备

```shell
yum -y remove mysql*

rm -rf /var/lib/mysql/lib*
```

拖包

![img](G:/%E8%BD%AF%E4%BB%B6/%E7%BD%91%E6%98%93%E4%BA%91%E6%9C%89%E9%81%93/henry_dzyj@126.com/9c33651d8a254e3b98f13c91d0367093/clipboard.png)



或下载源码包

```shell
wget --no-check-certificate http://dev.mysql.com/get/Downloads/MySQL-5.6/mysql-5.6.45.tar.gz
```



安装依赖

```shell
yum -y install ncurses-devel libaio-devel cmake
```



设置用户

```shell
useradd -s /sbin/nologin -M mysql

id mysql
```



解压并检查编译环境

```shell
tar -zxvf mysql-5.6.45.tar.gz

cd mysql-5.6.45
```



```shell
cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql \

-DMYSQL_DATADIR=/usr/local/mysql/data \

-DMYSQL_UNIX_ADDR=/usr/local/mysql/mysql.sock \

-DMYSQL_USER=mysql \

-DMYSQL_TCP_PORT=3306 \

-DDEFAULT_CHARSET=utf8 \

-DDEFAULT_COLLATION=utf8_general_ci \

-DWITH_EXTRA_CHARSETS=all \

-DWITH_INNOBASE_STORAGE_ENGINE=1 \

-DWITH_FEDERATED_STORAGE_ENGINE=1 \

-DWITH_BLACKHOLE_STORAGE_ENGINE=1 \

-DWITHOUT_EXAMPLE_STORAGE_ENGINE=1 \

-DWITH_ZLIB=bundled \

-DWITH_SSL=bundled \

-DENABLED_LOCAL_INFILE=1 \

-DWITH_EMBEDDED_SERVER=1 \

-DENABLE_DOWNLOADS=1 \

-DWITH_DEBUG=0
```



编译并安装

```shell
make && make install
```



初始化 mysql 配置数据库

```shell
cd /usr/local/mysql/

\cp support-files/my-default.cnf /etc/my.cnf

/usr/local/mysql/scripts/mysql_install_db --basedir=/usr/local/mysql/ --datadir=/usr/local/mysql/data --user=mysql
```



配置启动脚本管理 mysql

```shell
chown -R mysql.mysql etc/local/mysqll

cd /usr/local/mysql/

\cp support-files/mysql.server /etc/init.d/mysqld

chmod 700 /etc/init.d/mysqld

chkconfig mysqld on

/etc/init.d/mysqld restart
```



设置 mysql 的登录密码

```shell
/usr/local/mysql/bin/mysqladmin -u root password '123456'
```



添加 mysql 命令到环境变量生效

```shell
echo 'export PATH=/usr/local/mysql/bin/:$PATH' >>/etc/profile

source /etc/profile
```



登录测试

```shell
mysql -u root -p123456
```

### 修改密码



命令行修改

```shell
mysqladmin -u root -p旧密码 password 新密码
```

```mysql
[root@localhost ~]# mysql_secure_installation

Set root password? [Y/n]    #是否设置root用户密码，输入y并回车或直接回车
New password:               #设置root用户的密码
Re-enter new password:      #再输入一次你设置的密码
Password updated successfully!
Reloading privilege tables..
… Success!

Remove anonymous users? [Y/n]   #是否删除匿名用户,生产环境建议删除，所以直接回车
… Success!

Disallow root login remotely? [Y/n] #是否禁止root远程登录,根据自己的需求选择Y/n并回车,建议禁止
… Success!

Remove test database and access to it? [Y/n] #是否删除test数据库,直接回车
- Dropping test database…
… Success!

Reload privilege tables now? [Y/n] #是否重新加载权限表，直接回车
… Success!
Cleaning up…
Thanks for using MySQL!
```

数据库内修改

```shell
set password for root@'localhost' =password('新密码');
```

```shell
update mysql.user set password=password('123456') where user='root' and host='localhost';
		
flush privileges;
```

### Mysqldump




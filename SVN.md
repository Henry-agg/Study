# SVN



## 百度百科

​	SVN 是 subversion 的缩写，是一个开放源代码的版本控制系统，通过采用分支管理系统的高效管理，简而言之就是用于多个人共同开发同一个项目，实现共享资源，实现最终集中式的管理。

​	SVN的全称是 Subversion，即版本控制系统。它是最流行的一个开放源代码的版本控制系统。作为一个开源的版本控制系统，Subversion管理着随时间改变的数据。这些数据放置在一个中央资料档案库（Repository）中。这个档案库很像一个普通的文件服务器，不过它会记住每一次文件的变动。这样就可以把档案恢复到旧的版本，或是浏览文件的变动历史。Subversion是一个通用的系统，可用来管理任何类型的文件，其中包括程序源码。



## SVN 优势

1. 存储
   SVN 服务器既具有 CVS 所具有数据储存的优点，像是信息资源存储后会形成资源树结构，便于存储的同时，数据一般不会丢失，同时又拥有自己的特色。SVN 是通过关系数据库及二进制的存储方式，同时解决了既往不能同时读写同一文件等问题，同时增添了自己特有的“零或一”原则。
2. 速度
   与人们初始的 CVS 相比，SVN 在速度运行方面有很大提升。因为 SVN 服务器只支持少量的信息、资源传输，与其他系统相比，更支持的是离线模式，因此避免了网络拥挤现象的出现。
3. 安全性
   SVN 是一种技术性更加安全的产品，实现了系统和控制两方面的结合。一方面可以将系统整体的安全功能有效地分布在分支系统中，进而保证分支系统能正常运行，从而使各分支系统能够互补，最终在系统整体性的安全性得以保障，通过均衡原则实现最终追求安全的目的。



## SVN 的安装和配置

安装

```mysql
yum -y install subversion
```

利用 Apache 进行 Web 可视化，

```mysql
yum -y install httpd 
```

整合 Apache 和 SVN 需要一个模块，mod_dav_svn

```mysql
yum -y install mod_dav_svn
```

修改 Apache 的配置文件

```mysql
vim subversion.conf 

 1 <Location /svn>
 2    DAV svn
 3    SVNParentPath /home/svn
 4       AuthType Basic
 5       AuthName "Authorization svn"
 6       AuthzSVNAccessFile /home/svn/conf/authz
 7       AuthUserFile /home/svn/conf/users
 8       Require valid-user
 9 </Location>
```

增加用户，用于测试，root 用户不安全

```mysql
useradd svn
```

修改 Apache 的默认运行用户

```mysql
cd /etc/httpd/conf
cp httpd.conf httpd.conf.bak
vim httpd.conf

65 #
66 User svn
67 Group svn
68 
```

运行 Apache 查看运行用户

```mysql
ps -ef |grep httpd
root       8154      1  0 02:16 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
svn        8155   8154  0 02:16 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
svn        8156   8154  0 02:16 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
svn        8157   8154  0 02:16 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
svn        8158   8154  0 02:16 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
svn        8159   8154  0 02:16 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
svn        8240   8154  0 02:17 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
svn        8241   8154  0 02:17 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
svn        8242   8154  0 02:17 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
svn        8243   8154  0 02:17 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
svn        8244   8154  0 02:17 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
root       8336   7779  0 02:58 pts/0    00:00:00 grep --color=auto httpd
```

进去 svn 家目录，添加配置文件

```mysql
cd /home/svn
su svn
mkdir conf
```

创建访问认证文件，添加用户

```mysql
htpasswd -c users tom
cat users
```

创建定义权限的文件

```mysql
vim authz

 1 [groups]
 2 aaa=tom
 3 
 4 [/]
 5 @aaa=rw
```

创建项目仓库

```mysql
svnadmin bbb
```

回到 root 用户，重启 Apache

```mysq
exit
systemctl restart httpd
```

最后使用 Windows 客户端即可进行测试
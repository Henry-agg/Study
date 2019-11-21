# Haproxy

HAProxy 是一个使用 C 语言编写的自由及开放源代码软件，其提供高可用性、负载均衡，以及基于 TCP 和 HTTP 的应用程序代理

HAProxy 特别适用于那些负载大的 Web 的站点，这些站点通常又需要会话保持或七层处理

| 名字 |      HAProxy       |
| :--: | :----------------: |
| 类型 |        代理        |
| 支持 |      虚拟主机      |
| 模型 |      单一进程      |
| 特点 | 免费、快速并且可靠 |

安装

```shell
yum -y install haproxy
```

配置

路径：/etc/haproxy/haproxy.cfg

```mysql
#---------------------------------------------------------------------
# main frontend which proxys to the backends
#---------------------------------------------------------------------
frontend  name *:80	# 名字可选，端口80
    default_backend             webservers

#---------------------------------------------------------------------
# static backend for serving up images, stylesheets and such
#---------------------------------------------------------------------
backend webservers
    balance     roundrobin	# 算法（轮询）
    server      web-1 127.0.0.1:4331 check
	# 可增加
```

动静分离

```mysql
#---------------------------------------------------------------------
# main frontend which proxys to the backends
#---------------------------------------------------------------------
frontend name *:80
    acl  image      path_beg       -i /static /images /javascript /stylesheets # 开头
    acl  image      path_end       -i .jpg .gif .png .css .js # 结尾

    use_backend static          if image
    default_backend             webservers
#---------------------------------------------------------------------
# static backend for serving up images, stylesheets and such
#---------------------------------------------------------------------
backend webservers
    balance     roundrobin
    server      static 10.0.0.12:80 check
backend static
    balance     roundrobin
    server      static 10.0.0.13:80 check
```






































































































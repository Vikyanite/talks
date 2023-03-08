# Linux如何通过Apache httpd部署MantisBT

## 一、Apache httpd

### 1. 安装 Apache httpd

``` bash
yum install httpd -y   #安装httpd

systemctl start httpd  #启动httpd

systemctl enable httpd #配置自启动
```

### 2. 修改Apache服务端口

httpd默认是80端口，如果服务器80端口被占用，可以通过修改`/etc/httpd/conf/httpd.conf`文件改变httpd的端口。

```bash
vim /etc/httpd/conf/httpd.conf  
...
Listen 80  ->这个地方改为你想要的端口，如9900
...
```

ps. vim的话可以通过`/str`快速找到`str`所在位置

### 3. 防火墙开放端口

80是默认开放端口，如果修改了httpd.conf的端口，那么就需要把对应端口开放给外界访问

```bash
## 防火墙设置
firewall-cmd --zone=public --add-port=80/tcp --permanent
## firewall-cmd --zone=public --add-port=9900/tcp --permanent
firewall-cmd --reload

#firewall 命令说明：
–-zone #作用域
–-add-port=8081/tcp #添加端口，格式为：端口/通讯协议 --add-port=30060-30090/tcp
-–permanent #永久生效，没有此参数重启后失效
--reload #重启防火墙
```

我一般都是修改配置文件，效果与上述语句相同

```bash 
vim /etc/firewalld/zones/public.xml 

<?xml version="1.0" encoding="utf-8"?>
<zone>
  <short>Public</short>
  <description>For use in public areas. You do not trust the other computers on networks to not harm your computer. Only selected incoming connections are accepted.</description>
  <service name="ssh"/>
  <service name="dhcpv6-client"/>
  <service name="http"/>
  <service name="https"/>
  <port protocol="tcp" port="80"/>
  <port protocol="tcp" port="你要添加的端口"/>
</zone>

```

改完后记得重启服务或者reload防火墙：

``` bash
firewall-cmd --reload
```

### 4. 启动httpd服务

```bash
systemctl start httpd
```

### 5. 测试是否启动成功

访问[http://ip:port](https://links.jianshu.com/go?to=http%3A%2F%2Fip%3Aport)，出现以下页面则安装成功

https://img-service.csdnimg.cn/img_convert/fb0d6d9cbb9b5292827f00ff610d0798.png

## 二、安装 php

首先检查服务器环境是否安装php，Mantis要求php版本大于5.5，演示环境php选择7.1版本。

### 1. 准备工作

安装EPEL repo，否则会在执行第二步时报出异常`error: Failed dependencies:epel-release >= 7 is needed by webtatic-release-7-3.noarch`。

``` bash 
yum -y install epel-release
```

### 2. 安装Webtatic存储库

默认情况下，PHP 7在CentOS存储库中不可用，所以需要安装Webtatic

``` bash 
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
```

### 3. 安装PHP7.1

```bash 
yum install php71w php71w-cli php71w-mysqli php71w-mbstring -y
```

### 4. 确认是否安装成功

```bash
php -v
```

```bash
#出现以下信息说明成功
PHP 7.1.33 (cli) (built: Oct 26 2019 10:16:23) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.1.0, Copyright (c) 1998-2018 Zend Technologies
```

## 三、安装 MySQL

建议还是使用5.7版本的MySQL，高版本MySQL会跟MantisBT有字符集兼容问题。

### 1. 查看Linux系统是否安装MySQL

```bash 
rpm -qa|grep -i mysql
```

如果安装过了就参考[Linux如何完全卸载rpm/yum安装的软件](https://www.cnblogs.com/Vikyanite/p/17078409.html)，将其卸载。

### 2. yum安装MySQL

```bash 
#获取yum源
wget https://repo.mysql.com//mysql80-community-release-el7-3.noarch.rpm
#安装yum源
rpm -ivh mysql80-community-release-el7-3.noarch.rpm
#查看所有yum源
yum repolist all| grep mysql
#切换yum 源为mysql 5.7
yum-config-manager --disable mysql80-community
yum-config-manager --enable mysql57-community
#安装mysql5.7
yum install mysql-community-server -y
```

### 3. MySQL设置

```bash
systemctl start mysqld  #启动程序
systemctl enable mysqld #开机自运行
systemctl status mysqld #查看状态
```

#### 查看MySQL生成的初始密码：

```bash
cat /var/log/mysqld.log|grep pass 
#或者
grep 'temporary password' /var/log/mysqld.log
```

#### 登录MySQL：

```bash 
mysql -uroot -p
```

键入上一步的密码即可登录

#### 修改root密码：

```bash
ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
```

#### 如果需要远程连接数据库：

**建立root远程用户并授权**

```
#创建用户
create user 'linux_mysql'@'%' identified by '123456';
#授权
grant all privileges on *.* to 'linux_mysql'@'%'identified by '123456' with grant option;
#刷新
flush privileges;
```

**【温馨提示】**

**如果本地访问Linux数据库失败，查看一下这些配置，如果防火墙开启，要把数据库端口放开**

```bash
#（1）服务器的防火墙mysql端口3306是否开放
firewall-cmd --query-port=3306/tcp
#（2）设置3306端口为永久开放
firewall-cmd --add-port=3306/tcp --permanent
#（3）重启防火墙（设置了新的端口记得刷新防火墙）
systemctl restart firewalld
```

## 四、MantisBT安装

#### 1. 去官网[Mantis Bug Tracker (mantisbt.org)](https://www.mantisbt.org/)查看最新的版本下载地址：

```bash
wget https://sourceforge.net/projects/mantisbt/files/mantis-stable/2.25.5/mantisbt-2.25.5.tar.gz
```

#### 2. 解压压缩包：

```bash
tar xvf mantisbt-2.25.5.tar.gz
```

#### 3. 部署MantisBT

将MantisBT解压出的文件夹重命名为mantisbt：

```bash
mv mantisbt-2.25.5 mantisbt
```

将文件夹移动到Apache服务设置的目录下：

```bash
mv mantitbt /var/www/html
```

#### 4. Apache增加对php文件的支持

修改httpd.conf文件

```bash
vim /etc/httpd/conf/httpd.conf

#第一处
...
<Directory/>
	AllowOverride one
	Require all denied --> 改为granted
</Directory>
···

#第二处
<IfModule dir_module>
    DirectoryIndex index.html index.php -->这里新增了index.php
</IfModule>

#第三处
<IfModule mime_module>
	···
	AddType application/x-httpd-php .php #新增这一行
	AddType application/x-compress .Z
    AddType application/x-gzip .gz .tgz
    ···
</IfModule>
```

之后重启httpd服务：

```bash
systemctl restart httpd
```

### 五、MantisBT安装

之后就访问http://ip:port/mantisbt ，根据MantisBT的提示进行填写对应部分就安装完成啦！

-----

完结撒花！！
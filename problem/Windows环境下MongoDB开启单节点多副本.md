# Windows环境下MongoDB开启单节点多副本

## 前言

众所周知，MongoDB只能在多副本模式下使用事务。

但是如果我们不想开启多个节点，又想使用多副本模式该怎么办呢？

## 步骤

### 安装所需软件

#### MongoDB

本地使用，若没什么需要企业版的功能直接下载社区版即可；

官方下载网址：https://www.mongodb.com/try/download/community

![image-20230620203915350](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-06-20-368407-image-20230620203915350.png)

**（记得选择msi）**

在安装界面选择“Custom”

![image-20230620204018129](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-06-20-1db13d-image-20230620204018129.png)

![image-20230620204035464](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-06-20-9e8cc6-image-20230620204035464.png)

![image-20230620204052696](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-06-20-b4184b-image-20230620204052696.png)

（这一步如果有navicat或者其他的数据库连接软件的话就不用勾选“Install MongoDB Compass”了）

安装完成记得重启

#### Mongosh

mongodb官方给出表示，在mongodb4.5的版本中逐渐弃用mongo的shell命令来连接数据库，改用mongosh，在6版本中已经完全弃用mongo这个shell命令，故而需要在下载一个shell来连接mongodb数据库

网址：https://www.mongodb.com/try/download/shell?jmp=docs

记得选msi

![image-20230620204239932](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-06-20-f1b16c-image-20230620204239932.png)

（安装界面选不选私有无所谓）

![image-20230620204329584](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-06-20-1e6426-image-20230620204329584.png)

### 配置环境变量

打开系统环境变量

在**path**变量下配置刚刚安装MongoDB的bin目录，如图：

![image-20230620204409634](https://raw.githubusercontent.com/Vikyanite/talks/main/images/2023-06-20-fc785f-image-20230620204409634.png)

### 修改配置文件

在MongoDB安装目录下找到 MongoDB 的配置文件 `mongod.cfg`

添加如下配置，注意格式：`replSetName` 前面是 4 个空格而不是 tab 字符

```yaml
replication:
    replSetName: rs0
```

然后重启 MongoDB 服务，任务管理器-服务-MongoDB-（右键）重新启动

### 运行命令行

打开cmd，输入

```bash
mongosh --port 27017
rs.initiate()
```

然后就可以使用MongoDB的事务啦~

## 参考资料

[mongodb副本集搭建](https://blog.csdn.net/weixin_43464076/article/details/126509254)

[ MongoDB 运行事务时报“Transaction numbers are only allowed on a replica set member or mongos“错误_](https://blog.csdn.net/qq_43665821/article/details/125579864)


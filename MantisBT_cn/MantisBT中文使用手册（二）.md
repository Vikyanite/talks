# MantisBT中文使用手册（二）—— 安装

本章介绍了如何安装或升级MantisBT。

### 概要

下表主要展示了部署安装与更新的步骤，因为此处英文是安装时遇到的步骤名，于是就直接照搬

| 部署安装                                                     | 更新                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 1. <a href="#system-requirements">System Requirements</a><br />2. <a href="#pre-installation--upgrade-tasks">Pre-installation / upgrade tasks</a><br />3. <a href="#new-installation">New Installation</a><br />4. <a href="##configure-your-installation">Configure your installation</a><br />5. <a href="#post-installation-and-upgrade-tasks">Post-installation and upgrade tasks</a> <br />6. <a href="#post-installation-tasks">Post-installation tasks</a> | 1. <a href="#system-requirements">System Requirements</a><br />2. <a href="#backups">Backups</a><br />3. 将正在运行的MantisBT停止<br />4. <a href="#upgrading">Upgrading</a><br />5. <a href="#post-installation-and-upgrade-tasks">Post-installation and upgrade tasks</a> <br />6. <a href="#post-installation-tasks">Post-installation tasks</a> |

## System Requirements

### 硬件要求

MantisBT的硬件要求不高。它需要一台能够运行服务器软件的计算机（见 下方"软件要求 "）。

- 服务器类型 

  服务器可以是一个共享的公共网络服务器，也可以是一个专用服务器（a dedicated co-located box）。

- CPU和内存 

  对于任何网络应用，你应该根据网站的流量来确定服务器的大小。

- 磁盘 

  应用程序代码小于50MB。数据库所需的磁盘空间的数量将取决于RDBMS和数据量以及附件数量和大小

### 软件要求

所有需要的软件对商业和非商业使用都是免费的（开放源码）。请参考 下方"版本兼容性表 "一节中的表格，了解最低和推荐版本。

- 操作系统

MantisBT可以在Windows、macOS、Linux、Solaris、BSD以及几乎所有支持所需软件的系统上运行。

- 网络服务器 

  MantisBT主要在微软IIS[https://docs.microsoft.com/en-us/iis]和Apache[https://www.apache.org/]上测试。然而，它也有望与任何最新的网络服务器软件一起工作。文件扩展名。MantisBT只使用.php文件。如果你的网络服务器被配置为其他扩展名（例如：.PHP3，.PHTML），那么你将不得不要求管理员添加对.PHP文件的支持。这应该是一个微不足道的修改。进一步的细节可以在 PHP 文档中找到 [https:// www.php.net/manual/en/install.php] 

- PHP [https://www.php.net/] 

  web 服务器必须支持 PHP。它可以作为CGI或任何其他集成技术来安装。

- PHP扩展 

  MantisBT被设计成可以在尽可能多的环境中工作。因此，所需的扩展是最小的，其中许多是可选的，只影响一个功能。

  | 拓展类型   | 拓展插件                                                     |
  | ---------- | ------------------------------------------------------------ |
  | 强制性拓展 | 1. RDBMS使用的扩展（mysqli, pgsql, oci8, sqlsrv）<br />2. mbstring - 需要支持Unicode（UTF-8）<br />3. date, hash, json, pcre, Reflection, session - 运行MantisBT需要。 |
  | 可选的扩展 | 1. Curl - Twitter集成功能所需 -<br />2. GD - 验证码功能所需<br />3. Fileinfo - 文件附件和大多数插件所需，没有这个扩展，文件附件预览和下载就不能工作，因为MantisBT将不能向请求附件的浏览器发送Content-Type头<br />4. LDAP - LDAP或活动目录认证所需要的（见 "LDAP and Microsoft Active Directory "）<br />5. zlib - 需要启用输出压缩（见 "速度优化 "一节） |

  ​	**注意** 

  ​	你可以通过在命令行上运行php -m，或者使用PHP脚本中的php_info()函数来检查哪些PHP模块被安装。

- 数据库 

  MantisBT需要一个数据库来存储其数据。支持的RDBMS是:

  - MySQL（或其中一个Fork，如MariaDB)
  
  - PostgreSQL
  
  - Microsoft SQL Server
  
  - Oracle
  
    **注意** 
  
    MantisBT开发团队主要使用MySQL，所以对其他驱动的测试并不广泛，因为我们主要依靠社区贡献来改善对其他RDBMS的支持和修复问题。因此，我们建议用MySQL来存储你的数据。

#### 版本兼容性表
<table>
	<tr>
    	<th>Category</th>
        <th>Package</th>
        <th>Minimum Version</th>
        <th>Recommended</th>
        <th>Comments</th>
	</tr >
	<tr>  
	    <td rowspan="5">RDBMS</td>
	    <td>MySQL</td>
	    <td>5.5.35</td>
        <td>5.6 or later</td>
        <td>PHP extension:</br>mysqli</td>
	</tr >
	<tr >
	    <td>MariaDB</td>
	    <td>5.5.35</td>
        <td>10.x or later</td>
        <td>PHP extension:</br>mysqli</td>
	</tr>
	<tr>
	    <td>PostgreSQL</td>
	    <td>9.2</td>
        <td>9.2 or later</td>
        <td>PHP extension:</br>pgsql</td>
	</tr>
	<tr>
	    <td>MS SQL Server</td>
	    <td>2012</td>
        <td>2012 or later</td>
        <td>PHP extension:</br>sqlsrv</td>
	</tr>
	<tr>
	  <td>Oracle</td>
	    <td>11gR2</td>
        <td>11gR2 or later</td>
        <td>PHP extension:</br>oci8</td>
	</tr>
	<tr>
        <td>PHP</td>
        <td>PHP</td>
	    <td>7.0</td>
        <td>7.2 or later</td>
        <td>See above for PHP extensions</td>
	</tr>
	<tr>
	    <td rowspan="4">Web Server</td>
	    <td>Apache</td>
	    <td>2.2.x</td>
        <td>2.4.x</td>
	</tr>
	<tr>
        <td>lighttpd</td>
	    <td>1.4.x</td>
        <td>1.4.x</td>
	</tr>
	<tr>
        <td>nginx</td>
	    <td>1.10.x</td>
        <td>1.16.x or later</td>
	</tr>
	<tr>
        <td>IIS</td>
	    <td>7.5</td>
        <td>8.0</td>
        <td>Windows Server 2008 R2 SP1 or later</td>
	</tr>
</table>
我们的最低要求通常是基于其各自供应商对基础软件的支持的可用性。在某些情况下，我们确实需要一个特定的版本，因为我们所依赖的功能在旧版本中是不可用的。

**警告** 

不支持在软件组件版本低于上述最低要求的情况下运行MantisBT

### 客户端要求

MantisBT应该可以在市场上所有最新的浏览器上运行，包括但不限于：

- Firefox
- Edge
- Chrome
- Safari
- Opera 

注意对Internet Explorer 11的支持在2.22.0版本中结束。

## Pre-installation / upgrade tasks

这些任务涵盖了MantisBT的下载和部署，应在任何新的安装或升级之前执行。

1. 下载MantisBT（见 ["Mantis下载"](https://www.cnblogs.com/Vikyanite/p/17072652.html#%E5%A6%82%E4%BD%95%E5%AE%89%E8%A3%85)一节） 

2. 将下载的文件传输到你的Web服务器上 

   这可以用你最喜欢的任何方法（ftp、scp等）来完成。在接下来的步骤中，你需要telnet/ssh进入服务器。

3. 解压release 

   强烈建议为每个版本保持一个单独的目录。这不仅可以避免版本之间的不匹配（文件可能被添加或删除），而且还提供了一个简单的路径来降级你的安装。

   通常的命令是（1步）：

   ```bash 
   tar -xzf filename.tar.gz
   ```

   或者（2步）： 

   ``` bash
   gunzip filename.tar.gz tar -xf filename.tar 
   ```

   其他文件工具，如[7-Zip](https://www.7-zip.org/) 也应该能够处理存档的解压缩。

   解压过程应该创建一个新的目录，如mantisbt-1.3。

4. 重命名目录

   对于新的安装，你可能想把刚刚创建的目录重命名为更简单的东西，例如mantisbt 

   ``` bash 
   mv mantisbt-1.3.x mantisbt
   ```

## New Installation

本章节解释了如何进行MantisBT的新安装。

首先要检查名为 [System Requeirement](#system-requirements)的部分，并安装所需软件的适当版本。

完成后，执行安装脚本。从你的Web浏览器中，访问

```
https://yoursite/mantisbt/admin/install.php 
```

安装过程将经历以下步骤：

1. 脚本检查网络服务器的基本参数 

2. 提供安装所需的信息 

   - 数据库类型 

   - 数据库服务器主机名

   - 用户和密码

     需要的权限：SELECT, INSERT, UPDATE, and DELETE 

   - 高权限的数据库帐户

     需要的额外权限：INDEX、CREATE、ALTER和DROP 

     如果没有指定这个账户，将使用数据库用户

 3.  点击 "Install/Upgrade Database" 按钮 

 4.  脚本会创建数据库和表

     在这一阶段创建默认的管理员用户帐户，以允许MantisBT的初始登录和设置。

 5.  脚本试图编写一个基本的config_inc.php文件来定义数据库的连接参数

     如果网络服务器的用户账户没有对该目录的写入权限，这一操作可能会失败。在这种情况下，你需要手动创建该文件并从页面上复制/粘贴内容。

 6.  脚本对系统进行安装后检查

     检查并纠正任何错误

## Upgrading

本章节解释了如何升级现有的MantisBT

首先，执行上面 [Pre-installation / upgrade tasks](#pre-installation--upgrade-tasks)一节中描述的步骤。

1. 将正在运行的MantisBT停止

   ```
   cp mantis_offline.php.sample mantis_offline.php 
   ```

   这将阻止用户在升级过程中使用系统。

2. 在升级前一定要备份你的代码、数据和配置文件 ! 这包括你的Mantis目录，你的附件，和你的数据库。请参考 "Backups"一节以了解详情。

3. 复制配置文件 

   为了保留你的系统设置，你应该把下面列出的文件复制到新安装的"config"子目录中。

   - config_inc.php 

   - custom_strings_inc.php

   - custom_constants_inc.php

   - custom_functions_inc.php

     **注意** 

     上述清单并不详尽。你可能还需要复制其他特定于你的安装的自定义文件，如标志、favicon、css等

4. 复制第三方插件 

   为了保持系统功能，你应该复制插件子目录中的任何额外插件。例如，在Unix系统中，你可以使用以下命令；它将复制所有已安装的插件（在本地子目录或链接中），不包括捆绑的插件。
```bash
cd /path/to/mantisbt-OLD/plugins

find -maxdepth 1 ! -path . -type d -o -type l | grep -Pv "(Gravatar|MantisCoreFormatting|MantisGraph|XmlImportExport)" | xargs -Idirs cp -r dirs /path/to/mantisbt-NEW/plugins
```
​	**警告** 

​      请确保你不要用旧版本覆盖以下列表中的任何捆绑插件:

​      - Avatars via Gravatar（Gravatar） 
​      - MantisBT Formatting （MantisCoreFormatting） 
​      - Mantis Graphs （MantisGraph） 
​      - Import/Export issues （XmlImportExport）

5. 执行升级脚本。从你的Web浏览器，访问

   ```
   https://yoursite/mantisbt-NEW/admin/install.php
   ```

   其中mantisbt-NEW是解压新版本的目录名称

6. 提供升级所需的信息 

   - 高权限的数据库账户

     需要的额外权限：INDEX、CREATE、ALTER和DROP 

     如果没有指定这个账户，将使用数据库用户。

7. 单击 "Install/Upgrade Database "按钮 

8. 在升级结束时，审查并纠正任何警告或错误

   **升级大型数据库** 

   在处理1.2以上版本的大型数据库时，升级脚本在转换日期字段时可能失败，使系统处于不一致（即部分更新）的状态。在这种情况下，你应该简单地重新启动升级过程，它将恢复到它离开的地方。注意，你可能要重复几次，直到正常完成。参考[MantisBT问题12735](https://mantisbt.org/bugs/view.php?id=12735)

## Configure your installation

你可以调整许多设置来配置和定制MantisBT。

请参阅[Chater 5. Configuration]()，以及 `config_defaults_inc.php` 文件，以深入了解可用的选项。还可以查看[Chater 7. Customizing MantisBT]()，以了解更多的选项，使你的安装更加个性化。

这一步通常只需要在新的安装中进行，但在升级时，你可能想查看并可能定制任何新的配置选项。

在编辑器中打开或创建子文件夹`config_inc.php`文件，根据需要添加或修改任何值，这些将覆盖默认值。

你可能想使用提供的`config_inc.php.sample`作为一个起点。

**警告**

你千万不要直接编辑config_defaults_inc.php文件，因为这可能导致未来升级的问题。始终将你的自定义配置存储在你自己的config_inc.php文件中。

**警告** 

MantisBT的配置文件（config_inc.php以及custom_strings_inc.php, custom_constants_inc.php, custom_functions_inc.php等）应该始终保存为UTF-8格式，不含BOM。如果不这样做可能会导致意外的显示问题。

## Post-installation and upgrade tasks

本节中的说明对新的安装和升级都是通用的，应该在完成任何一个过程后应用。

1. 测试你的配置 

   加载admin/check/index.php来验证所有的设置是否正确，并根据需要采取纠正措施。

2. 删除admin文件夹 

   一旦你确认了安装或升级过程是成功的，你就应该删除这个目录 

   ```bash 
   rm -r admin
   ```

   出于安全考虑，这个目录中的脚本不应该在MantisBT网站上随意访问，特别是通过互联网访问的网站，因为它们可以让未经授权的人（例如黑客）获得关于系统的技术知识，以及执行管理任务。

   **警告** 

   省略这一重要步骤将使你的MantisBT实例暴露于一些潜在的严重攻击，例如[问题#23173](https://mantisbt.org/bugs/view.php?id=23173) （如果php.ini中启用了[mysqli.allow_local_infile](https://www.php.net/manual/en/mysqli.configuration.php#ini.mysqli.allow-local-infile) ）。

## Post-installation tasks

本节中的说明只应在新安装后应用 

1. 登录到您的Bugtracker 

   使用默认的管理员帐户, ID和密码为administrator / root。

2. 创建一个新的管理员帐户 

   进入 "管理">"用户"，创建一个具有 "管理员 "访问级别的新帐户。

3. 禁用或删除默认的管理员账户 

4. 创建一个新的项目 

   进入管理 > 项目，创建一个新的项目

## Post-upgrade tasks

本节中的说明只应在升级现有安装后应用。

1. 测试新版本 

   进行任何适当的测试，以确保新版本不会产生冲突。

2. 将网站切换到新版本 

   下面的命令应该从网站根目录（或安装mantisbt脚本的地方）执行，并假设 "live "目录（旧版本）名为mantisbt，新版本目录为mantisbt-1.3.x. 

   ```bash
   mv mantisbt-old 
   
   mv mantisbt-1.3.x mantisbt 
   ```

3. 把网站重新上线 

   ```bash
   rm mantis_offline.php 

​	这应该是升级过程中的最后一步，因为它将让用户再次登录。

## Backups

我们强烈建议定期备份您的MantisBT数据库。执行这一操作的方法取决于你使用的RDBMS。

备份是一个复杂的话题，每个RDBMS的具体实施和处理方法都超出了本文的范围。为了方便你，下面的部分提供了一个简单的方法来备份MySQL数据库。

你也应该考虑对你的MantisBT代码（包括你的配置和可能的定制），以及问题附件（如果存储在磁盘上）和项目文件实施备份。

**警告** 

在升级之前，你应该始终备份你的系统（代码和数据库） !

## MySQL Backups 

MySQL数据库很容易使用mysqldump命令进行备份：

```bash
mysqldump -u<username> -p<password> <database name> > <output file> 
```

要恢复备份，你将需要有一个干净的数据库。然后运行： 

``` bash 
mysql -u<username> -p<password> <database name> < <input file> 
```

你也可以使用 [phpMyAdmin](https://www.phpmyadmin.net/) 执行这两项任务

另一个好办法是制作一个备份脚本，并通过 cron 或任务调度器定期运行。在文件名中使用当前日期可以防止覆盖并使编目更容易。

参考资料和有用的链接。

- [mysqldump文档](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html)
- [Percona XtraBackup](https://www.percona.com/software/mysql-database/percona-xtrabackup)
- [AutoMySQLBackup脚本](https://sourceforge.net/projects/automysqlbackup/)

## Uninstall

建议你做一个备份，以防你将来想使用你的数据，详见[ "Backups "](#backups)一节。

卸载MantisBT步骤如下： 

- 删除MantisBT目录以及所有文件和子目录
- 从数据库中删除所有的MantisBT表，这些表可以通过安装时配置的前缀来识别。默认的前缀是 "mantis"。
- 删除你可能已经做的任何定制或添加的内容。

如果你有创建/删除数据库的权限，并且你有一个特定的MantisBT数据库，不包含任何其他数据，你可以删除整个数据库。

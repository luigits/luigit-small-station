# RockyLinux安装MySQL


> 本文接[RedHat系Linux通过yum安装MySQL](/RedHat系Linux通过yum安装MySQL/index.html)
# 新系统，新旅程
&emsp;&emsp;经朋友推荐，我接触了Rocky Linux，经过几天的使用，我将虚拟机和个人租的服务器的系统切换为了Rocky。

&emsp;&emsp;不得不说，Rocky Linux真的舒服，本人目前使用的是Rocky8.6。上一篇文章讲述了使用包管理器安装MySQL，本篇自然也会讲。
```bash
# 我是使用的普通用户，所以需要加sudo，root用户不需要使用sudo

sudo dnf install -y @mysql # 截止到2022年11月，默认安装的是8.0.26的版本
```
&emsp;&emsp;想不到吧，就这一条，就能安装完成。下面再讲一下怎么设置。
## 1. 启动服务
这个就不用说了吧？
```bash
sudo systemctl start mysqld # 启动服务

sudo systemctl enable mysqld # 开机启动服务
```
## 2. 运行初始化脚本
```bash
sudo mysql_secure_installation
```
然后描述一下大致的流程，有点长，只关心有注释的地方就好
```bash
sudo mysql_secure_installation  # 运行脚本

Securing the MySQL server deployment.

Connecting to MySQL using a blank password.

VALIDATE PASSWORD COMPONENT can be used to test passwords and improve security. 
It checks the strength of password and allows the users to set only those passwords which are secure enough. 
Would you like to setup VALIDATE PASSWORD component?
# 此处是询问是否设置验证密码组件，选择y/Y会启用validate_password
Press y|Y for Yes, any other key for No: no  # 输入一个非y或Y的字符。

Please set the password for root here.

New password:  # 输入MySQL的密码，我这里输入的是123456

Re-enter new password:  # 再次输入MySQL的密码
By default, a MySQL installation has an anonymous user, allowing anyone to log into MySQL without having to have a user account created for them. 
This is intended only for testing, and to make the installation go a bit smoother.
You should remove them before moving into a production environment.
# 询问是否移除匿名用户
Remove anonymous users? (Press y|Y for Yes, any other key for No) : y # 从此处开始，后续都是y也是可以的
Success.


Normally, root should only be allowed to connect from 'localhost'. This ensures that someone cannot guess at the root password from the network.
# 询问是否禁止远程登录MySQL
Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y # 这里输入Y或no都是可以的，后面会处理，我这里是输入了y
Success.

By default, MySQL comes with a database named 'test' that anyone can access. This is also intended only for testing, and should be removed before moving into a production environment.

# 询问是否移除test数据库
Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y
 - Dropping test database...
Success.

 - Removing privileges on test database...
Success.

Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.
# 询问是否重新加载权限表
Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
Success.

All done!
```
## 3. 登陆MySQL
```bash
mysql -uroot -p123456 # 进入数据库

mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 8.0.26 Source distribution

Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```
此时已经进入了MySQL，我们只需要检查一下MySQL是否允许远程连接就可以了
```sql
use mysql;# 进入mysql数据库
select user,host from user;# 查看当前mysql的用户及权限

# 因为我上面在初始化脚本中选择的是不允许远程登录MySQL，所以我是需要修改权限的
update user set host='%' where user='root';
flush privileges;# 刷新权限表
```
<font color=red>警告</font>！此时对于MySQL而言，主机名已经由localhost更改为%

## 题外话
Rocky Linux是无法使用ntp进行时间同步的哦，CentOS8同样也不支持，所以还在使用CentOS系统的小伙伴们，记得看一下Chrony时间同步。
Rocky Linux可以理解为是基于CentOS8做的，对于遇到的问题，可以直接看CentOS8的解决方案。

# RedHat系Linux通过yum安装MySQL



> 提示：
> 1. 本次安装的是MySQL最新的版本
> 2. 使用的系统为CentOS7
> 3. 每个页面的链接在文档中都有，可以直接点击跳转

---

# RPM安装太费劲了！
RPM不会帮你解决依赖问题，所以安装是有安装顺序的QAQ。

如果有想折腾的可以参考这篇文章：[LinuxCentOs7下安装MySQL8.0.26详细教程，本人亲测可行，仅供大家避雷](https://blog.csdn.net/qq_45263520/article/details/124084190?spm=1001.2014.3001.5502)

---

# 零、首先要通过yum安装如下命令工具
1. wget命令行下载工具
2. vim编辑器，可选下载，Linux自带的vi可以替代
```bash
yum install -y wget vim 
```
# 一、导入官方仓库
## 1. 打开官网，进入下载界面
下载页面链接:[https://dev.mysql.com/downloads/](https://dev.mysql.com/downloads/)
![下载界面](/images/download-page.png)
可以看到蓝色框内的是官方提供的Linux软件仓库。其中红色框内的是RedHat系Linux使用的包管理器yum的仓库。

## 2. 点击MySQL Yum Repository进入官方仓库
yum库链接：[MySQL Yum Repository](https://dev.mysql.com/downloads/repo/yum/)
![系统版本](/images/OS-Version.png)
根据安装的系统版本点击相应的download即可。

## 3. 复制链接
![复制连接](/images/copy-link.png)
打开终端，执行以下命令
```bash
# 下载链接对应的文件，官方库的rpm文件
wget https://dev.mysql.com/get/mysql80-community-release-el7-5.noarch.rpm
# 安装下载的rpm文件
rpm -ivh mysql80-community-release-el7-5.noarch.rpm # 直接复制链接的后半部分即可
```
# 二、安装MySQL
```bash
yum install -y mysql-community-server 
# 不知道直接写mysql会不会成功，后面我会尝试。
# 如果成功，将会修改此处的内容
```
# 三、配置MySQL
## 1.启动MySQL
```bash
systemctl start mysqld

# 如果是出现的下面一行，表示MySQL安装没有问题
Redirecting to /bin/systemctl start  mysqld.service # 只有第一次启动会出现
```
**相关命令，此部分内容可以跳过，只是对mysqld服务简单操作的罗列**
```bash
# 查看MySQL服务运行状态
systemctl status mysqld 

# 启动MySQL
systemctl start mysqld

# 关闭MySQL
systemctl stop mysqld

# 重启MySQL
systemctl restart mysqld
```
## 2.初次登录
### 1. 获取MySQL随机生成的初始密码
```bash
grep 'temporary password' /var/log/mysqld.log
```
举例说明：
```bash
# 密码在最后,每个人都是不同的
grep 'temporary password' /var/log/mysqld.log

2016-11-14T04:34:41.742516Z 1 [Note] A temporary password is generated for root@localhost: sNKz9yEdzw%/
```
从执行结果来看
1. 账户是`root`
2. 主机名是`localhost`
3. 初始密码是`sNKz9yEdzw%/`
### 2. 修改密码
> 本处我采用的顺序
> 1. 修改初始密码
> 3. 修改配置文件
> 3. 修改为简单密码
> 
> 注意：顺序可以变换，比如先修改配置文件，再登录修改初始密码等

**MySQL默认开启了validate_password插件，该插件要求密码至少包含一个大写字母，一个小写字母，一个数字和一个特殊字符，并且密码长度至少8个字符**
#### 1. 登录MySQL
```bash
mysql -uroot -p
# 输入上一步获取到的初始密码，进入MySQL
```
#### 2. 修改密码
```sql
# 先将密码修改为Pw12345.可以在mysql终端中进行操作
ALTER user 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'Pw12345.';
```
#### 3. 查看当前的安全策略
```sql
show variables like 'varlidate%';
```
当前生效的安全策略如下![安全策略未修改](/images/Security-policy.png)
#### 4. 修改安全策略
> 修改安全策略有两种方式
> 1. 直接在mysql终端中使用set进行修改
> 2. 在my.cnf文件中修改。

1. 直接在MySQL终端中使用set修改
	```sql
	# 修改密码策略等级为low
	set global validate_password.policy=low;
	# 密码的最小长度
	set global validate_password.length=6;
	# 设置密码中至少要包含0个大小写字母
	set global validate_password.mixed_case_count=0;
	# 设置密码中至少包含0个数字
	set global validate_password.number_count=0;
	# 设置密码中至少包含0个特殊字符
	set global validate_password.special_char_count=0;
	```
2. 在配置文件中修改密码策略
**首先退出mysql终端**
	```sql
	quit;
	```
	**其次，使用shell编辑my.cnf文件**
	```bash
	vim /etc/my.cnf
	# 打开后，输入i进入编辑模式
	```
	**修改密码策略**
	```shell
	validate_password.check_user_name=OFF # 是否能将密码设置成当前用户名
	validate_password.length=4 # 密码的最小长度，也就是说密码长度必须大于或等于4
	validate_password.mixed_case_count=0 # 密码必须包含的大写、小写字符数
	validate_password.number_count=0 # 密码必须包含的数字个数
	validate_password.policy=0 # 密码强度 0只检查长度
	validate_password.special_char_count=0 # 密码必须包含的特殊字符个数
	```
3. 回到MySQL终端查看当前生效的安全策略
	```bash
	# 首先重启MySQL
	systemctl restart mysqld
	# 进入MySQL
	mysql -uroot -pPw12345.
	```
	```sql
	# 查看当前的安全策略
	show variables like 'varlidate%';
	```
	当前生效的安全策略如下![安全策略](/images/new-Security-policy.png)
	此时就可以设置简单密码1234了
#### 5. 修改密码
```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY '1234';
# 注意，此处的root是获取初始密码时得到的。同样localhost也是
```
## 3. SQL客户端连接虚拟机
### 1. 禁用firewall
```shell
systemctl stop firewalld# 临时禁用(关闭，虚拟机重启后会启动)
systemctl disable firewalld# 永久禁用(关闭开机自启动)
```
### 2. 进入MySQL操作，更新权限 
```sql
use mysql;# 进入mysql数据库
select user,host from user;# 查看当前mysql的用户及权限
# 如果账户所对应的host不是'%',使用下面的命令更新
update user set host='%' where user='root';
FLUSH PRIVILEGES;# 刷新内容
```
完成上述内容后，使用sql客户端链接即可
# 总结
> MySQL的在win/mac和Linux上的安装差距实在是大，差点疯掉
1. 下载MySQL的官方库
2. 导入官方库
3. 下载MySQL
4. 修改密码
5. 连接物理机sqlyog

以上就是本内容的五个部分

---

> 警告：不知为何MySQL的安全插件validate_password消失，卸载后重新安装也没有加载成功。
> 另，修改密码的另一个方式就是直接卸载密码安全插件
> 方式如下：
> 
> 卸载插件
> ```sql
> UNINSTALL COMPONENT 'file://component_validate_password';
> ```
> 安装插件
> ```sql
> INSTALL COMPONENT 'file://component_validate_password';
> ```


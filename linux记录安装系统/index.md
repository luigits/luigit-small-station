# Linux记录：安装系统


> 想要完整记录下来已经很久了，一直都是零零散散的，现在记录一下吧。
> 除非说明，我一般都是先说文字再配图
## 选择虚拟机
> 虚拟化程序大家也比较熟悉，基本上是VMware的**VMware Workstation Pro**和Oracle的**VirtualBox**两款软件

本记录中的虚拟化程序是VirtualBox，贴一下Oracle网站下VirtualBox的下载页面：[VirtualBox下载](https://www.oracle.com/cn/virtualization/technologies/vm/downloads/virtualbox-downloads.html?source=:ow:o:p:nav:mmddyyVirtualBoxHero_cn&intcmp=:ow:o:p:nav:mmddyyVirtualBoxHero_cn)

我也不会摆出VirtualBox的优势，因为我压根不知道。
从VMware换到VirtualBox的唯一原因是我感觉VMware占用系统资源高一些，而我的电脑性能比较差。
## 安装系统
到了选择系统安装系统的时候。

如果是个人使用，我建议选择[Arch Linux](https://archlinux.org/)，这里有着非常完善的[Wiki](https://wiki.archlinux.org/)供你参考，即使不认识英文也没关系，有中文。
如果是为了学习基础的使用或者为了工作使用，那还是选择一个服务器系统吧，比如RedHat(CentOS)、Rocky Linux或者Ubuntu以及其他。从我的使用上来说的话还是建议RedHat、CentOS、Rocky Linux入手。因为他们区别不大。
![Rocky Linux about页](/images/Rocky-about-page.png)

从上图也能了解一些RedHat、CentOS、Rocky Linux之间的关系。我所使用的便是Rocky Linux，同样的Rocky Linux也有文档可以看：[Rocky Linux Documentation](https://docs.rockylinux.org/zh/)，不过中文的翻译并不多，目前只有安装系统和迁移数据是中文。
### 1.下载系统
系统的下载有两种方式，一是取官网下载，二是在镜像站下载，这里我选择的是官网，如果觉得下载速度慢可以选择[阿里巴巴开源镜像站](https://developer.aliyun.com/mirror/)或其他镜像站下载
![主页](/images/Rocky-home-page.png)
在下载页面可以看到兼容的RedHat版本和停止维护的时间
![Rocky9下载页](/images/Rocky-download-page.png)
Minimal：最小化，没有常用软件包
DVD：完全，带有常用软件包，可以创建本地软件源
Boot：Live运行，没用过，不知道能不能安装，大概率不行
> 我的下载权重是Minimal>DVD>Boot，如果需要图形化界面直接选择DVD就好。

### 2. 设置虚拟机
下载完成之后来开始安装吧
![VirtualBox主界面](/images/vb-1.png)
点击新建，输入名称，选择安装的位置，选择下载好的ISO文件位置。VirtualBox会自动判断类型和版本，记得点击跳过自动安装
![新建操作系统](/images/vb-2.png)
如果按照图形化界面记得将内存调整到至少4GB（4096MB）大小，处理器调整到2～4
![设置内存和处理器数量](/images/vb-3.png)
虚拟硬盘的话至少50GB，如果不够在扩容，因为我是2TB机械所以我一般100GB起步
![虚拟硬盘](/images/vb-4.png)
这里可以看可以不看，反正不能操作
![摘要](/images/vb-5.png)
点击完成之后就能看到创建的系统信息了，然后我们需要点击设置，再修改一下配置
![创建完成](/images/vb-6.png)
关闭音频和USB设备
![关闭音频和USB设备](/images/vb-7.png)
完成之后点击确定在点击启动，打开虚拟机。
### 3. 安装系统
选择第一项并回车
![进入安装界面](/images/vb-8.png)
选择要使用的操作系有语言
![选择系统语言](/images/vb-9.png)
1. 点击**时间和日期**可以修改时区，中国的时区城市是上海
2. 点击**软件选择**，在此处可以设置安装的软件主要是最小化安装、服务器常用软件、带图形化界面安装。因为此处使用的ISO文件是Minimal版本，所以只有最小化安装一项。
3. 点击**安装目的地**，可以设置虚拟系统的分区信息
4. 点击**KDUMP**。可以选择是否启用内核崩溃时的日志收集器，虚拟机没必要，如果时生产环境中就需要打开

![在这里插入图片描述](/images/vb-10.png)


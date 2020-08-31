# 1 安装和部署

## 1、介绍

本课程是基于CentOS 6版本的学习。

我们之后要学习的消息队列（Kafka、RabbitMQ、RockeetMQ）、缓存（Redis）、搜索引擎（ES）、集群分布式（需要购买多台服务器，如果没有服务器，只能使用虚拟机）会使用到Linux操作系统。

Linux是一个开源的、免费的操作系统，其稳定性、安全性、处理多并发已经得到了业界的认可。同时，也是一套免费使用和自由传播的类Unix操作系统，是一个基于POSIX（可移植操作系统接口）和UNIX的多用户、多任务、支持多线程和多CPU的操作系统。很多的大型项目都是部署在Linux服务器上。Linux一切皆文件，文件读、写、(权限)。

**Linux发行版**

Linux是一个内核，在内核的基础上，做了一个包装。简单说就是将Linux内核与应用软件做一个打包。主要发行版有：Ubuntu(乌班图)、RedHat(红帽)、CentOS、Debain(蝶变)等。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102628.png" alt="img" style="zoom: 67%;" />

**应用领域**

通常服务器使用LAMP(Linux+Apache+Mysql+PHP)或LNMP(Linux+Nginx+Mysql+PHP)组合。

## 2、环境搭建

​		学习Linux需要一个环境，我们需要创建一个虚拟机VMware，然后在虚拟机上安装一个CentOS系统来学习。Linux的安装，安装步骤比较繁琐（操作系统也是一个软件），现在云服务器挺普遍的，价格也便宜，如果直接不想搭建的话可以直接买一台来学习。

**步骤**

1. 先安装virtual machine(vm12)。
2. 再安装Linux(CentOS)

他们之间的关系为：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102629.png" alt="image-20200701111424239" style="zoom:67%;" />

具体的安装步骤参考VMware_CentOS安装文档。

# 2 CentOS终端和联网的说明

在电脑中的管理员界面输入`ipconfig`查看自己电脑的网络信息，我们可以看到电脑上的IP地址：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102630.png" alt="image-20200701153340258" style="zoom:67%;" />

在电脑上会产生补一个网段的IP地址：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102631.png" alt="image-20200701153521211" style="zoom:80%;" />

也就是说我们的虚拟机会有一个和VMnet8相同网段的一个IP地址，我们在虚拟机界面右键点击“在终端中打开”进入CentOS的终端界面，然后在其中输入`ifconfig`发现没有这个IP地址：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102632.png" alt="image-20200701153711028" style="zoom:80%;" />

eth0表示的是第一块网卡。

主要是我们进行网络连接：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102633.png" alt="image-20200701153829247" style="zoom:80%;" />

此时我们再输入`ifconfig`,将会得到IP地址：会发现该地址和VMnet8处于相同一个网段。（这里是简单的动态分配ip(后面我们要学习固定分配ip)）

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102634.png" alt="image-20200701154043078" style="zoom:80%;" />

在虚拟机中的浏览器中输入百度网址，此时该浏览器可以上网，即虚拟机访问了万维网。

也可以和外面的ip连通，测试如下：我们让虚拟机ping我们的电脑，发现可以ping通：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102635.png" alt="image-20200701154451072" style="zoom:80%;" />

# 3 文件系统目录结构

​		Linux的文件目录是采用级层式的树状目录结构，在此结构中的最上层是根目录“/”,然后在此目录下再创建其他目录。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102636.png" alt="img" style="zoom:80%;" />

+ /bin(重点)（/usr/bin、/usr/local/bin），是Binary的缩写，该目录下存放着经常使用的命令。
+ /sbin（/usr/sbin、/usr/local/sbin）s的意思是Super User的意思，这里存放着系统管理员使用的系统管理程序。
+ /home(重点)，存放普通用户的主目录，在Linux中每一个用户都有一个自己的目录，一般该目录名是以用户的账号命名的。
+ /root(重点)该目录为系统管理员，也称作超级权限者的用户主目录。
+ /lib 系统开机所需要的最基本的动态连接共享库，其作用类似于Windows里的DLL文件，几乎所有的应用程序都需要用到这些共享库。
+ /lost+found 该目录一般情况下是空的，当系统非法关机后，这里存放了一些文件。
+ etc(重点) 所有的系统管理所需要的配置文件和子目录my.comf。
+ /usr(重点) 这是一个非常重要的目录，用户的很多应用程序和文件都在这个目录下，类似于windows下的program file目录。
+ /boot (重点) 存放着启动Linux时使用的一些核心文件，包括连接文件和镜像文件。
+ /proc 改目录下是一个虚拟的目录，它是系统内存的映射，访问这个目录来获取系统信息。
+ /srv 是service的缩写，该目录下存放一些活动启动之后需要提取的数据。
+ /sys 是Linux2.6 内核的一个很大的变化，该目录下安装了2.6内核新出现的一个文件系统。
+ /tmp 该目录是用来存放临时文件的。
+ /dev 类似于windows的设备管理器，把所有的硬件用文件的形式存储。
+ /media（重点）Linux系统会自动识别一些设备，例如U盘，光驱等等，当识别后，Linux系统会把识别的设备挂载到这个目录下。
+ /mnt(重点) 系统提供该目录是为了让用户临时挂载别的系统文件的，我们可以将外部挂载的存储挂载到/mnt/下，然后该目录就可以查看里面的内容了。
+ **/opt** 这是给主机额外安装软件所摆放的目录。如安装Mysql数据库就可存放到这个目录下，默认为空。
+ /usr/local（重点） 这是另一个给主机额外安装软件所安装的目录。一般是通过**编译源码方式安装的程序**。
+ /var（重点） 这个目录中存放着在不断扩充着的东西，习惯将经常被修改 的目录放到这个目录下。包括各种日志文件。
+ /selinux（security-enhanced linux）类似于360，该目录是一个安全子系统，它能够控制程序只能访问特定文件。

因此，在linux中，有很多目录，是安装后，有自动有目录，每个目录都会存放相应的内容，不要去修改。 在学习linux时，要尽快的在脑海中，形成一个 目录树。

# 4 远程登录Linux服务器

​		Linux服务器是开发小组共享的，正式上线的项目是运行在公网的，因此开发人员需要远程登录到CentOS进行项目管理和开发，我们使用xshell和xshell软件即可。其中，xshell是一个专门用于远程登录Linux的软件。xshell是一个远程上传和下载文件。

参考**Xshell_XFtp5_SecureCRT安装.md**文档。

# 5 关机、重启和注销

## 1、 关机和重启

|      命令       |          描述          |
| :-------------: | :--------------------: |
| shutdown -h now |        立即关机        |
|      halt       |        立即关机        |
|  shutdown -h 1  |      1分钟后关机       |
| shutdown -r now |        立即重启        |
|  shutdown -r 1  |      1分钟后重启       |
|     reboot      |        立刻重启        |
|      sync       | 把内存的数据，写入磁盘 |

不管是重启系统还是关闭系统，首先要运行`sync`命令，把内存中的数据写到磁盘中。

## 2、 用户的登录和注销

1. 登录时尽量少用`root`帐号登录，因为它是系统管理员，最大的权限，避免操作失误。可以利用普通用户登录，登录后再用 `su - 用户名(substitude代替)` 命令来切换成系统管理员身份。

2. 在提示符下输入 `logout` 即可注销用户。（不同的shell 可能不同(logout exit)）其中，`logout` 注销指令在图形运行级别(简单提一下：0-6个级别)无效，在运行级别 3下有效。

下面是在Xshell工具上的注销命令：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102637.png" alt="image-20200705220852343" style="zoom:80%;" />

# 6 实用指令

## 1、 运行级别和找回root密码

**指令运行级别**

| 级别 |                        描述                        |
| :--: | :------------------------------------------------: |
|  0   |                        关机                        |
|  1   | 单用户（类似于安全模式，该模式下可以帮助找回密码） |
|  2   |             多用户状态，没有网络服务。             |
|  3   |        多用户状态，有网络服务（使用最多）。        |
|  4   |               系统未使用保留给用户。               |
|  5   |                     图形界面。                     |
|  6   |                     系统重启。                     |

常用的运行级别是3和5,系统的运行级别配置文件为`/etc/inittab`。

修改运行级别的基本语法：

```ini
init [012356]
```

**案例1**：通过init 来切换不同的运行级别，比如动 5-3 ， 然后关机。

首先，在虚拟机控制界面上输入下面的命令，查看此时虚拟机所处的运行级别（默认为5）：

```ini
vim /etc/inittab
```

![image-20200706090120577](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102638.png)

输入下面的指令：

```ini
init 3
init 0
```

**找回root的密码**

思路：进入当用户模式，修改root密码，因为进入单用户模式，root不需要密码就可以登录。

步骤：

​		启动时->快速输入enter->输入e-> 进入到编辑界面-> 选择中间有kernel 项->
 输入e(edit)-> 在该行的最后写入 1 [表示修改内核，临时生效]-> 输入enter->
 输入b [boot]-> 进入到单用模式 【这里就可以做补救工作】

## 2、 文件和目录相关的指令

```ini
pwd	 --显示当前工作目录的绝对路径 

ls 	[选项] 	[目录或是文件]
	常用选项如下：
 		-a:显示当前目录所有的文件和目录，包括隐藏的 (文件名以.开头就是隐藏)。
      	-l:以列表的方式显示信息。
       	-h:显示文件大小时，以 k , m, G单位显示。

cd  [参数] --切换到指定目录
	  cd ~/cd :回到自己的家目录。
	  cd .. :回到当前目录的上一级目录。
```

cd中参数（绝对路径和相对路径的理解）

绝对路径就是从根路径`/`开始，相对路径就是从当前位置开始定位。

**案例：**

+ 使用绝对路径切换到root目录 [cd /root]。

+ 使用相对路径到/root 目录 [ cd ../root]（.. 代表返回上一级目录）。

![image-20200706102902151](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102639.png)

+ 表示回到当前目录的上一级目录 [ cd ..]。

+ 回到家目录 [ cd ~ ]。

**mkdir [make directory]**

该指令用于创建目录。

基本语法：

```ini
mkdir  [选项]  要创建的目录
	常用选项：
	-p ：创建多级目录（不加时，默认为单级目录）。
	
# 删除空目录(remove directory)，如果该目录下面有内容，该命令无法删除。需使用rm -rf 命令。
rmdir  [选项]  要删除的空目录 

rm -rf 要删除的目录 
	 r: 表示递归删除，就是将该目录下的文件和子目录全部删除
     f: 表示强制删除，就是不需询问
```

案例1：创建一个目录 /home/test。
![image-20200706104359706](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102640.png)

案例2：创建多级目录 /home/animal/tiger。

![image-20200706104437559](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102641.png)

![image-20200706104454237](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102642.png)

案例3：强制删除非空目录。

![image-20200706105113753](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102643.png)

**touch指令**

该指令创建空文件(和windows下新建一个文件一致)， 还可以更新文件的修改时间。

基本语法：

```ini
 touch 文件名称  -- 
```

案例1：创建一个空文件 hello.txt。

![image-20200706105510083](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102644.png)

也可以一次性创建多个文件。

**cp指令（copy）**

该指令用于拷贝文件到指定目录。

基本语法：

```ini
 cp [选项]  source【源】  dest【目的文件】
 	常用选项
      -r ：递归复制整个文件夹。
  	  强制覆盖不提示的方法：\cp。
```

案例1：将 /home/hello.txt 拷贝到 /home/bbb 目录下

![image-20200706105908473](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102645.png)

案例2：递归复制整个文件夹。

![image-20200706110313271](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102646.png)

**rm指令**

该指令用于移除文件或目录。

基本语法：

```ini
rm  [选项]  要删除的文件或目录
  常用选项
      -r ：递归删除整个文件夹
      -f ：强制删除不提示
```

**mv指令**

该指令用于移动文件与目录或重命名。

基本语法：

```ini
mv  oldNameFile newNameFile      --重命名
mv /temp/movefile /targetFolder  --移动文件或目录
```

案例1: 将 /home/aaa.txt 文件 重新命名为 pig.txt 。

案例2:将 /home/pig.txt 文件 移动到 /root 目录下。

![image-20200706141426519](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102647.png)

**cat指令**

该指令用于查看文件内容（只读的方式）。

基本语法：

```ini
cat  [选项] 要查看的文件 | more  -- 分页显示
	选项：
	-n ：显示行号
```

**输出重定向(>)和追加(>>)**

```ini
ls -l >文件	--列表的内容写入文件a.txt中（覆盖写）
ls -al >>文件 --列表的内容追加到文件aa.txt的末尾
cat 文件1 > 文件2 --将文件1的内容覆盖到文件2
echo "内容">> 文件
```

  案例1: 将 /home 目录下的文件列表 写入到 /home/info.txt 中

  	 ls–l /home/  > /home/info.txt

 假如如果文件不存在，则会自动创建。

   案例2: 将当前日历信息 追加到 /home/mycal 文件中 

  	 date >> /home/mycal

 **echo指令**

该指令用于输出内容到控制台。

基本语法：

```ini
echo  [选项]  [输出内容]
```

案例: 使用echo 指令输出环境变量，比如 PATH。

![image-20200706144745121](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102648.png)

**head指令**

​		该指令用于显示文件的开头部分内容，默认情况下head指令显示文件的前10行内容。

基本语法：

```ini
head 文件	--查看文件头10行内容。
head -n 5 文件    -- 查看文件头5行内容，5可以是任意行数。
```

**tail指令**

​		该指令用于输出文件中尾部的内容，默认情况下tail指令显示文件的后10行内容。

基本语法：

```ini
tail  文件 	-- 查看文件头10行内容。
tail  -n 5 文件 	-- 查看文件头5行内容，5可以是任意行数。
tail  -f  文件	-- 实时追踪该文档的所有更新（经常使用）。
```

​		案例1: 实时监控mydate.txt , 看看到文件有变化时，是否看到， 实时的追加日期。

![image-20200706145845221](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102649.png)

**ln指令**

​		该指令叫软链接也成为符号链接，类似于windows里的快捷方式，主要存放了链接其他文件的路径。

基本语法：

```ini
 ln -s [原文件或目录] [软链接名] 	--给原文件创建一个软链接。
 rm –rf  linkToRoot（软链接名，不要带“/”）  --删除软连接。
```

​		说明： 当我们使用pwd指令查看目录时，仍然看到的是软链接所在目录，但是我们确实通过该快捷方式到达指定目录。

案例1：在/home 目录下创建一个软连接 linkToRoot，连接到/root目录

 ![image-20200706163744264](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102650.png)

   **history指令**

​		该指令是查看已经执行过历史命令,也可以执行历史指令。

基本语法：

```ini
history  --显示所有的历史命令 。
history 10 --显示最近使用过的10个指令。
!5         -- 执行历史编号为5的指令(在查询所有指令后的基础上执行比较好一点)。
```

**时间日期类**

1. **date指令**

该指令用于显示当前时期和设置日期。

基本语法：

```ini
date	--显示当前时间。
date +%Y  --显示当前年份。
date +%m	-- 显示当前月份。
date +%d	-- 显示当前是哪一天。

date  -s  字符串时间 --设置时间。
```

显示当前日期：

![image-20200706165406973](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102651.png)

设置日期：

![image-20200706165657067](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102652.png)

2. **cal指令**

该指令用于查看日期指令。

基本语法：

```ini
cal [选项]	--不加选项，显示本月日历
```

![image-20200706165908982](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102653.png)

**搜索查找类**

1. **find指令**

   ​	该指令用于将从指定目录向下递归地遍历各个子目录，将满足条件的文件或者目录显示到终端上。

基本语法：

```ini
  find  [搜索范围]  [选项]   --搜索范围，可以是根目录或者home目录下等。
```

|      选项       |                        描述                         |
| :-------------: | :-------------------------------------------------: |
| -name<查询方式> | 按照指定的文件名查找模式查找文件，可以使用通配符*？ |
|  -user<用户名>  |            查找属于指定用户名的所有文件             |
| -size<文件大小> |             按照指定的文件大小查找文件              |

 	案例1:按文件名：根据名称查找/home 目录下的mydate.txt文件。

 	案例2：按拥有者：查找/opt目录下，用户名称为nobody的文件。

​	案例3：查找整个linux系统下大于10M的文件（+n 大于、 -n小于  、n等于）。

![image-20200706171132736](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102654.png)

2. **locate指令**

​       该指令可以快速定位文件路径，利用事先建立的系统中所有文件名称及路径的`locate`数据库实现快速定位给定的文件。

​		该指令无需遍历整个文件系统，查询速度较快。为了保证查询结果的准确度，管理员必须定期更新`locate`时刻。

基本语法：

```ini
locate 搜索文件
```

​		注意：

​		由于`locate`指令基于数据库进行查询，所以第一次运行前，必须使用`updatedb`指令创建`locate`数据库。

​		案例1: 请使用locate 指令快速定位 hello.txt 文件所在目录。

![image-20200706171725761](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102655.png)

  3. **grep指令和 管道符号 |** 

     ​		我们需要在一个文件内部查找关键字，其中`grep` 表示过滤查找 ， 管道符“|”，表示将前一个命令的处理结果输出传递给后面的命令处理。

基本语法：

```ini
grep [选项] 查找内容 源文件

grep –n（ni不区分大小写）if /etc/profile  --在/etc/profile 中查找 if ,并显示行，区别大小写。

```

| 选项 |        描述        |
| :--: | :----------------: |
|  -n  | 显示匹配行和行号。 |
|  -i  |  忽略字母大小写。  |

**压缩和解压缩类指令**

1. **gzip/gunzip 指令**

   其中，`gzip` 用于压缩文件， `gunzip` 用于解压的。（将原文件压缩后，原文件不保留，以压缩文件的形式存在）

基本语法：

```ini
gzip 文件 	--压缩文件，只能将文件压缩为*.gz文件。
gunzip 文件.gz --解压缩文件命令
```

​		案例1:首先使用命令 `gzip`压缩， 将 /home下的 mydate.txt文件进行压缩，然后使用 `gunzip`压缩， 将 /home下的 mydate.txt.gz 文件进行解压缩。![image-20200706173212882](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102656.png)

2. **zip/unzip 指令**

   zip 用于压缩文件， unzip 用于解压的，这个在项目打包发布中很有用的。

基本语法：

```ini
zip  [选项] XXX.zip（压缩后文件名） 需要压缩的内容  --压缩文件和目录的命令。
	常用选项:
	-r：递归压缩，即压缩目录。 
unzip [选项] XXX.zip	--解压缩文件。
	常用选项:
	-d<目录> ：指定解压后文件的存放目录。
```

案例1: 将 /home下的 所有文件进行压缩成 mypackage.zip。

![image-20200706174106915](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102657.png)

案例2：将 mypackge.zip 解压到 /opt/tmp目录下。

![image-20200706174211810](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102658.png)

3. **tar 指令**

   ​	tar 指令 是打包指令，最后打包后的文件是 .tar.gz 的文件。 （可以进行压缩和解压操作）。

基本语法：

```ini
tar  [选项]  XXX.tar.gz  打包的内容/目录   --打包目录，压缩后的文件格式.tar.gz。
```

| 选项 |           描述           |
| :--: | :----------------------: |
|  -c  | **压缩**产生.tar打包文件 |
|  -v  |       显示详细信息       |
|  -f  |    指定压缩后的文件名    |
|  -z  |       打包同时压缩       |
|  -x  |     **解压**.tar文件     |

案例1：压缩多个文件，将 /home/a1.txt 和 /home/a2.txt这两个文件一起压缩成 a.tar.gz文件。

![image-20200706175141601](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102659.png)  

案例2：将a.tar.gz 解压到当前目录。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102700.png" alt="image-20200706175439151"  />

 案例3：将a.tar.gz解压到/opt/目录(指定目录)下。

![image-20200706175657567](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102701.png)

注意：此时指定解压的目录要事先存在。

# 7 任务调度

​		任务调度：是指系统在某个时间执行的特定的命令或程序。

​		**任务调度分类**：

1. 系统工作：有些重要的工作必须周而复始地执行。如病毒扫描等。
2. 个别用户工作：个别用户可能希望执行某些程序，比如对mysql数据库的备份。

基本语法：

```ini
conrtab –r  --终止任务调度。
crontab –l --列出当前有那些任务调度
service crond restart   --重启任务调度

crontab [选项]
```

| 常用选项 |             描述              |
| :------: | :---------------------------: |
|    -e    |      编辑crontab定时任务      |
|    -l    |        查询crontab任务        |
|    -r    | 删除当前用户所有的crontab任务 |

​		注：如果只是简单的任务，可以不用书写脚本，直接在crontab中加入任务即可，反之，需要写脚本（shell编程）。

​		案例：每小时的每分钟执行 `ls –l /etc/ > /tmp/to.txt`命令。

```ini
1.crontab –e --设置个人任务调度
2.*/1 * * * * ls –l  /etc/ > /tmp/to.txt --输入任务到调度文件,当保存退出（:wq）后即生效。
```

**参数细节说明**

1. 5个占位符说明

|   项目    |         含义         |          范围           |
| :-------: | :------------------: | :---------------------: |
| 第一个“*” | 一个小时中的第几分钟 |          0-59           |
| 第二个“*” | 一天当中的第几个小时 |          0-23           |
| 第三个“*” |  一个月当中的第几天  |          1-31           |
| 第四个“*” |   一年当中的第几月   |          1-12           |
| 第五个“*” |   一周当中的星期几   | 0-7（0，7都代表星期日） |

2. **特殊符号的说明**

| 特殊符号 |                             含义                             |
| :------: | :----------------------------------------------------------: |
|    *     | 代表任何时间。比如第一个“*”就代表一小时中每分钟都执行一次的意思。 |
|    ，    | 代表不连续的时间。比如“0 8,12,16  * *  * 命令”，就代表在每天的8点0分，12点0分，16点0分都执行一次命令 |
|    -     | 代表连续的时间范围。比如“0  5 *   * 1-6命令”，代表在周一到周六的凌晨5点0分执行命令 |
|   */n    | 代表每隔多久执行一次。比如“*/10 *   * * * 命令”，代表每隔10分钟就执行一遍命令 |

3. **特定时间执行任务**

|     时间     |                             含义                             |
| :----------: | :----------------------------------------------------------: |
| 45 22  * * * |                      在22点45分执行命令                      |
| 0 17 * * * 1 |                   每周1 的17点0分执行命令                    |
| 0 5 1,15 * * |              每月1号和15号的凌晨5点0分执行命令               |
| 40 4 * * 1-5 |              每周一到周五的凌晨4点40分执行命令               |
| */10 4 * * * |            每天的凌晨4点，每隔10分钟执行一次命令             |
| 0 0 1,15 * 1 | 每月1号和15号，每周1的0点0分都会执行命令。注意：星期几和几号最好不要同时出现，因为他们定义的都是天。非常容易让管理员混乱。 |

案例：终止任务调度。

![image-20200708174927833](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102702.png)

# 8 磁盘分区和挂载

## 1、分区

1. **分区的方式(知道即可)**。

mbr分区:

1. 最多支持四个主分区

2. 系统只能安装在主分区

3. 扩展分区要占一个主分区

4. MBR最大只支持2TB，但拥有最好的兼容性

gtp分区（比较优越）:

1. 支持无限多个主分区（但操作系统可能限制，比如 windows下最多128个分区）

2. 最大支持18EB的大容量（1EB=1024 PB，1PB=1024 TB ）

3. windows7 64位以后支持gtp

![image-20200708175836379](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102703.jpg)

2. **Linux分区**

+ Linux来说无论有几个分区，分给哪一目录使用，它归根结底就只有一个根目录，一个独立且唯一的文件结构 , Linux中每个分区都是用来组成整个文件系统的一部分。
+ Linux采用了一种叫“载入”的处理方法，它的整个文件系统中包含了一整套的文件和目录，且将一个分区和一个目录联系起来。这时要载入的一个分区将使它的存储空间在一个目录下获得。
+ 下面是Linux分区示意图

![img](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102704.png)

3. **硬盘说明**

+ Linux硬盘分IDE硬盘和SCSI硬盘，目前基本上是SCSI硬盘。

+ 对于IDE硬盘，驱动器标识符为`hdx~`,其中`hd`表明分区所在设备的类型，这里是指IDE硬盘了。`x`为盘号（a为基本盘，b为基本从属盘，c为辅助主盘，d为辅助从属盘）,`~`代表分区，前四个分区用数字1到4表示，它们是主分区或扩展分区，从5开始就是逻辑分区。

  例：`hda3`表示为第一个IDE硬盘上的第三个主分区或扩展分区,`hdb2`表示为第二个IDE硬盘上的第二个主分区或扩展分区。 

+ 对于SCSI硬盘则标识为`sdx~`，SCSI硬盘是用`sd`来表示分区所在设备的类型的，其余则和IDE硬盘的表示方法一样。

  例：sdb1 表示第2块scsi 硬盘的第1个分区。

**注意**：

​		主分区：主分区是硬盘的启动分区，我们常说的“C盘”就是硬盘上的主分区。它被操作系统和主板认定为这个硬盘的第一个分区。所以C盘永远都是排在所有磁盘分区的第一的位置上。

​		扩展分区：除去主分区所占用的容量以外，硬盘剩下的容量就被认定为扩展分区（也就是说：一块硬盘除去主分区外的容量后，如果对剩下的容量进行了再分区，那么，这个再分区就是扩展分区）。

​		逻辑分区：扩展分区是不能直接使用的，他是以逻辑分区的方式来使用的，所以说扩展分区可以分成若干个逻辑分区。他们的关系是包含的关系，所有的逻辑分区都是扩展分区的一部分。扩展分区如果不再进行分区了，那么整个扩展分区就是逻辑分区了。

4. **查看所有设备的挂载情况**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102705.png" alt="image-20200708182158581" style="zoom:150%;" />

##  2、挂载的经典案例

**需求**：给虚拟机添加一块新硬盘。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102706.png" alt="image-20200708182714534" style="zoom:80%;" />

**步骤**

1. 增加一块硬盘 1G [到设置中添加一块硬盘即可]

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102707.png" alt="image-20200708183332445" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102708.png" alt="image-20200708183412997" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102709.png" alt="image-20200708183441585" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102710.png" alt="image-20200708183531513" style="zoom:80%;" />

完成界面：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102711.png" alt="image-20200708183614399" style="zoom:80%;" />

我们需要使用命令`reboot`，重启一下服务器后，才能查到相关的硬盘信息，在Xshell界面上查询：

![image-20200708203855782](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102712.png)

2. 给sdb 硬盘分区

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102713.png" alt="image-20200708204409096" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102714.png" alt="image-20200708204605995" style="zoom:80%;" />

其中对sdb分区中比较重要的指令：

```ini
m   显示命令列表
p   显示磁盘分区 同 fdisk  –l
n   新增分区
d   删除分区
w   写入并退出
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102715.png" alt="image-20200708205214290" style="zoom:80%;" />

3. 格式化 sdb1

```ini
mkfs -t  ext4   /dev/sdb1   --格式化磁盘,其中ext4是分区类型。
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102716.png" alt="image-20200708205459437" style="zoom:80%;" />

4. 挂载

   ​	所谓挂载就是将一个分区与一个目录联系起来。

+ 先创建一个/home/newdisk目录。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102717.jpg" alt="image-20200708210115119" style="zoom:80%;" />

+ 执行挂载命令

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102718.png" alt="image-20200708210239496" style="zoom:80%;" />

​		对于上面的方式，只是临时生效，当你重启系统，挂载的关系就会消失， 因此，我们要配置`Linux`的分区表，实现启动时，自动挂载.

命令执行如下：

```ini
vim /etc/fstab  --进入挂载文件页面
```

![img](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102719.png)

添加完成后，执行下面的操作自动挂载会即刻生效。

```ini
mount  –a  -- a：auto含义。
```

当我们重启虚拟机后，会发现硬盘自动挂载到/home/newdisk目录上。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102720.png" alt="image-20200708212440145" style="zoom:80%;" />

**卸载指令**

```ini
umount 设备名 | 挂载路径
umount /dev/sdb1 --将挂载到/home/newdisk的硬盘卸载
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102721.png" alt="image-20200708212942300" style="zoom:80%;" />

## 3、磁盘查询

**基本语法**：

```ini
df -h  --查询系统整体磁盘使用情况
du -h  /目录  --查询指定目录的磁盘占用情况，默认为当前目录
       -s 指定目录占用大小汇总
       -h 带计量单位
       -a 含文件
       --max-depth=1  子目录深度
       -c 列出明细的同时，增加汇总值
```

**实用指令**：

```ini
ls –l /home/ | grep “^-” | wc -l  --统计/home文件夹下文件的个数（wc为统计命令，-l 只统计行数;-w 只统计单词数;-m 只统计字符数）。
ls –l /home/ | grep “^d” | wc -l  --统计/home文件夹下目录的个数。
ls –lR /home/ | grep “^-” | wc -l --统计/home文件夹下文件的个数，包括子文件夹里的。
ls –lR /home/ | grep “^d” | wc -l --统计/home文件夹下目录的个数，包括子文件夹里的。
```

**以树状显示home目录结构**

当没有`tree`指令时，我们使用`yum`指令来进行安装。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102722.png" alt="image-20200708214841069" style="zoom:80%;" />

![image-20200708214903871](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102723.png)

# 9 网络配置

1、网络结构

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102724.png" alt="image-20200708220305828" style="zoom:67%;" />

2、查看虚拟网络编辑器

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102725.png" alt="image-20200708220540380" style="zoom:80%;" />

3、修改虚拟网卡的ip的地址

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102726.png" alt="image-20200708220643258" style="zoom: 67%;" />

4. **查看网关**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102727.png" alt="image-20200708220820346" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102728.png" alt="image-20200708220914103" style="zoom:80%;" />

5. **查看windows环境的中VMnet8网络配置**

方式1：在管理员界面输入ipconfig指令。

方式2：界面查看（等到下次连接到wifi时再查看）。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102729.png" alt="image-20200708221820578" style="zoom:80%;" />

6. **ping命令**

用来测试主机之间网络连通性。

基本语法：

```ini
ping 目的主机  --测试当前服务器是否可以连接目的主机
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102730.png" alt="image-20200708222201297" style="zoom:80%;" />

## 1、网络环境配置

方式1：**自动获取**

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102731.png" alt="image-20200708222747073" style="zoom:80%;" />

​		可以发现，这样的设置比较简单，每次启动Linux后，虚拟机分配的ip地址可能不一样，因此，该方式不适合做服务器。

方式2：**指定固定的ip**

直接修改配置文件来指定IP,并可以连接到外网(程序员推荐)，编辑 

```ini
vi /etc/sysconfig/network-scripts/ifcfg-eth0
```

 要求：将ip地址配置的静态的，ip地址为192.168.xxx.xxx  

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102732.png" alt="image-20200709084645823" style="zoom:80%;" />

**ifcfg-eth0文件说明**

```ini
DEVICE=eth0                #接口名（设备,网卡）
HWADDR=00:0C:2x:6x:0x:xx   #MAC地址 
TYPE=Ethernet               #网络类型（通常是Ethemet）
UUID=926a57ba-92c6-4231-bacb-f27e5e6a9f44  #随机id
#系统启动的时候网络接口是否有效（yes/no）
ONBOOT=yes                
# IP的配置方法[none|static|bootp|dhcp]（引导时不使用协议|静态分配IP|BOOTP协议|DHCP协议）
BOOTPROTO=static      
#IP地址
IPADDR=192.168.189.130  
#网关  
GATEWAY=192.168.189.2      
#域名解析器
DNS1=192.168.189.2 
```

注意，我们电脑的VMnet8的ip地址为`192.168.10.1`，因此，需要将上面的189这个网段改成10。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102733.png" alt="image-20200709085421498" style="zoom:80%;" />

改好之后，重启网络服务：

```ini
service  network restart  、reboot
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102734.png" alt="image-20200709085724639" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102735.png" alt="image-20200709085800506" style="zoom:80%;" />

​		也可以在虚拟机界面ping我们的windows的ip地址，或者在windows的命令界面ping我们的虚拟机地址。

## 2、修改Linux主机名

```ini
1)查看当前主机名
  hostname
2)修改linux的主机映射文件
  vim /etc/sysconfig/network
  文件中内容
NETWORKING=yes
NETWORKING_IPV6=no
HOSTNAME= hadoop01 //写入新的主机名
注意：主机名称不要有“_”下划线 
3)修改 /etc/hosts 增加ip和主机的映射
    192.168.10.130  hadoop01
    ....
4)并重启设备，生效.
5)如果希望windows也可以通过主机名来连接centos01, 进入C:\Windows\System32\drivers\etc\hosts  
192.168.10.130 hadoop01	
```

修改虚拟机主机名，ping主机：

![image-20200709091700030](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102736.png)

windows也可以通过主机名来连接centos01：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102737.png" alt="image-20200709092930900" style="zoom:80%;" />

windows能够ping通hadoop01主机名：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102738.png" alt="image-20200709093021269" style="zoom:80%;" />

# 10 进程管理

## 1、介绍

+ 在Linux中，每个执行的**程序（代码）**都称为一个进程。每一个进程都分配一个ID号。

+ 每一个进程，都会对应一个父进程，而这个父进程可以复制多个子进程。例如www服务器。

+ 每个进程都可能以两种方式存在的。前台与后台，所谓前台进程就是用户目前的屏幕上可以进行操作的。后台进程则是实际在操作，但由于屏幕上无法看到的进程，通常使用后台方式执行(sshd , crond)。 

+ 一般系统的服务都是以后台进程的方式存在，而且都会常驻在系统中。直到关机才才结束。

1. **显示系统执行的进程**

   基本语法：

   ```ini
   ps -a --显示当前终端的所有进程信息
   ps -u --以用户的格式显示进程信息
   ps -x --显示后台进程运行的参数
   ```

`ps`(Process Status)命令是用来查看目前系统中，有哪些正在执行，以及它们执行的状况。可以不加任何参数。

`ps`显示的信息选项：

|  字段   |                             描述                             |
| :-----: | :----------------------------------------------------------: |
|   PID   |                          进程识别号                          |
|  PPID   |                           父进程ID                           |
|    c    | CPU用于计算执行优先级的因子。数值越大，表明进程是CPU密集型运算，执行优先级会降低；数值越小，表明进程是I/O密集型运算，执行优先级会提高 |
|   TTY   |                   终端机号（终端名称缩写）                   |
|   VSZ   |              进程占用的虚拟内存大小（单位：KB）              |
|   RSS   |              进程占用的物理内存大小（单位：KB）              |
|  TIME   |                    此进程所消耗的CPU时间                     |
|   CMD   |                   正在执行的命令或者进程名                   |
|  %CPU   |                     进程占用CPU的百分比                      |
|  %MEM   |                   进程占用物理内存的百分比                   |
|  STAT   | 进程状态，其中S-睡眠，s-表示该进程是会话的先导进程，N-表示进程拥有比普通优先级更低的优先级，R-正在运行，D-短期等待，Z-僵死进程，T-被跟踪或者被停止等 |
| STARTED |                        进程的启动时间                        |
| COMMAND |        启动进程所用的命令和参数，如果过长会被截断显示        |

查看所有的进程信息：

![image-20200709114102892](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102739.png)

查看特定的进程信息：

![image-20200709114418312](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102740.png)

查看所有的父进程信息：

![image-20200709114626603](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102741.png)

查看特定的父进程信息：

![image-20200709114613389](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102742.png)

grep：全局正则表达式版本（Global Regular Expression Print）。

## 2、终止进程

​		若是某个进程执行一半需要停止时，或是已消了很大的系统资源时，此时可以考虑停止该进程。使用kill命令来完成此项任务。

基本语法：

```ini
kill  [选项] 进程号 --通过进程号杀死进程。
	常用选项：
	-9 :表示强迫进程立即停止
killall 进程名称 --通过进程名称杀死进程，也支持通配符，这在系统因负载过大而变得很慢时很有用。
```

案例1：踢掉某个非法登录用户，此时该登录用户就会掉线。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102743.png" alt="image-20200709115450794" style="zoom:80%;" />

案例2: 终止远程登录服务sshd, 在适当时候再次重启sshd服务。

​		适用场景，系统处于维护的过程中，不需要用户进行登录。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102744.png" alt="image-20200709115657062" style="zoom:80%;" />

案例3: 终止多个gedit 编辑器。

```ini
kill gedit --将父进程干掉后，子代也一锅端。
```

案例4：强制杀掉一个终端 对于 bash。

![image-20200709120314322](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102745.png)

## 3、服务管理

​		服务(service) 本质就是进程，但是是运行在后台的，通常都会监听某个端口，等待其它程序的请求，比如(mysql , sshd **防火墙**等)，因此我们又称为**守护进程**，是Linux中非常重要的知识点。

service管理指令：

```ini
service  服务名 [start | stop | restart | reload | status]
```

在CentOS7.0后 不再使用`service` ,而是 systemctl。

案例1：查看当前防火墙的状况，关闭防火墙和重启防火墙。

![image-20200709150033385](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102746.png)

![image-20200709150421568](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102747.png)

说明：关闭或者启用防火墙后，立即生效。在windows中使用下面的指令进行测试，某个端口即可。

```ini
telnet ip 端口
telnet 192.168.10.130 22
```

​		输入上面的指令在windows中进行测试：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102748.png" alt="image-20200709151955104" style="zoom:80%;" />

​		该指令需要计算机在控制面板中打开telent功能。这种方式只是临时生效，当重启系统后，还是回归以前对服务的设置。如果希望设置某个服务自启动或关闭永久生效，要使用`chkconfig`指令。

```ini
service iptables start
service iptables stop  --临时关闭防火墙
service iptables status

chkconfig iptables off  --关闭防火墙(chkconfig iptables off)
```

**查看服务名**

**方式1**：使用setup -> 系统服务 就可以看到。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102749.png" alt="image-20200709152405926" style="zoom:80%;" />

进入里面之后，按空格，即可将服务去除。

**方式2**:  /etc/init.d/服务名称。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102750.png" alt="image-20200709152626388" style="zoom:80%;" />

**服务的运行级别(runlevel)**

```ini
查看或者修改默认级别：  vi /etc/inittab 
 
Linux系统有7种运行级别(runlevel)：常用的是级别3和5
运行级别0：系统停机状态，系统默认运行级别不能设为0，否则不能正常启动
运行级别1：单用户工作状态，root权限，用于系统维护，禁止远程登陆
运行级别2：多用户状态(没有NFS)，不支持网络
运行级别3：完全的多用户状态(有NFS)，登陆后进入控制台命令行模式
运行级别4：系统未使用，保留
运行级别5：X11控制台，登陆后进入图形GUI模式
运行级别6：系统正常关闭并重启，默认运行级别不能设为6，否则不能正常启动
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102751.png" alt="image-20200709152910127" style="zoom:80%;" />

每个服务对应不同的运行级别。

**chkconfig指令**

该指令可以给每个服务的各个运行级别设置自启动关闭。

基本语法:

```ini
chkconfig  --list|grep  xxx    -- 查看服务 
chkconfig   服务名 --list 
chkconfig  --level  5   服务名  on/off   --可以指定某个服务，某各个运行级别，开启或关闭
chkconfig     服务名 on/off  --彻底关闭
```

举例：

```ini
Chkconfig –level 1 sshd off/on --sshd 服务在 1 运行级别 off
chkconfig  iptables off  --不管是哪个级别都关闭
```

## 4、监控服务

1、 动态监控进程

​		`top`与`ps`命令很相似。它们都用来显示正在执行的进程。`top`与`ps`最大的不同之处，在于`top`在执行一段时间可以更新正在运行的的进程(默认每3秒变化一次)。

​		基本语法：

```ini
top [选项]
```

|   选项   |                             描述                             |
| :------: | :----------------------------------------------------------: |
| -d  秒数 | 指定top命令每隔几秒进行更新，默认是3秒在top命令的交互模式中可以执行命令 |
|    -i    |              使top不显示任何闲置或者僵死的进程               |
|    -p    |          通过指定监控进程ID来仅仅监控某个进程的状态          |

交互操作说明：

| 操作 |              功能               |
| :--: | :-----------------------------: |
|  P   | 以cpu使用率排序，默认使用该选项 |
|  M   |       以内存的使用率排序        |
|  N   |            以PID排序            |
|  q   |             退出top             |

案例1.如何监视特定用户。

​		首先，输入 top命令，按回车键，查看执行的进程。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102752.png" alt="image-20200709154741637" style="zoom:80%;" />
		然后，输入`u`回车，再输入用户名，即可

案例2：如何终止指定的进程。
		首先，输入 top命令，按回车键，查看执行的进程。

​		然后输入`k`回车，再输入要结束的进程ID号。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102753.png" alt="image-20200709155056104" style="zoom:80%;" />

案例3:指定系统状态更新的时间(每隔10秒自动更新一次）。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102754.png" alt="image-20200709155305825" style="zoom:80%;" />

2、 监控网络状态

主要是查看网络状态。

基本语法：

```ini
netstat [选项]
    选项说明 
    -an  按一定顺序排列输出
    -p  显示哪个进程在调用
```

案例：查看服务名为 sshd 的服务的信息。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102755.png" alt="image-20200709155826899" style="zoom:80%;" />

# 11 RPM与YUM

## 1、介绍

​		RPM是`RedHat Package Manager`（RedHat软件包管理工具）的缩写，类似windows的setup.exe，这一文件格式名称虽然打上了RedHat的标志，但理念是通用的。它是一种用于互联网下载包的打包及安装工具，它包含在某些Linux分发版中。它生成具有.RPM扩展名的文件。

​		Linux的分发版本都有采用（suse,redhat, centos 等等），可以算是公认的行业标准了。

##  2、RPM包的管理

1、基本指令

rpm包的简单查询指令：

```ini
rpm  –qa | grep xx --查询已安装的rpm列表
rpm -qa | grep firefox --查询Linux中是否安装了火狐插件
```

![image-20200709160526561](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102756.png)

RPM包名基本格式解释：

```ini
名称:firefox
版本号：45.0.1-1 
适用操作系统: el6.centos.x86_64 
表示centos6.x的64位系统
如果是i686、i386表示32位系统，noarch表示通用。
```

RPM包的其它查询指令：

```ini
rpm -qa  --查询所安装的所有rpm软件包  
rpm -qa | more    
rpm -qa | grep X [rpm -qa | grep firefox ]

rpm -q 软件包名 --查询软件包是否安装
rpm -q firefox

rpm -qi 软件包名 --查询软件包信息
rpm -qi file

rpm -ql 软件包名 --查询软件包中的文件
rpm -ql firefox

rpm -qf 文件全路径名  --查询文件所属的软件包
rpm -qf /etc/passwd
rpm -qf /root/install.log
```

2、安装RPM包

基本语法：

```ini
rpm -ivh  RPM包全路径名称 
	参数说明:
	i=install 安装
   	v=verbose 提示
   	h=hash  进度条
```

注意：很多的rpm包，就在我们的centos安装的镜像文件中。

案例

安装firefox浏览器。

（1）先找到firefox的安装rpm包，此时，我们需要挂载上我们安装的centos的iso文件,然后到/media/目录下去查询软件包。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102757.png" alt="image-20200709164039427" style="zoom:80%;" />

此时，桌面上有一个光驱出来：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102758.png" alt="image-20200709164127818" style="zoom:80%;" />

​	使用cd /media/命令切换到该目录下，在该目录下，使用指令`ls`可以看到里面有个`CentOS_6.8_Final`文件，进入`CentOS_6.8_Final`后输入`ls`命令后，有一个`Packages`文件，然后执行下面图片中的操作。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102759.png" alt="image-20200709165325965" style="zoom:80%;" />

（2）安装firefox

上图中已写到：

```shell
rpm -ivh  RPM包全路径名称
```

 3、卸载RPM包

基本语法：

```ini
rpm -e RPM包的名称
rpm -e firefox  --删除firefox  软件包
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102800.png" alt="image-20200709163522348" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102801.png" alt="image-20200709163632456" style="zoom:80%;" />

注意：

+ 如果其它软件包依赖于您要卸载的软件包，卸载时则会产生错误信息。

如： 

```ini
$ rpm -e foo 
removing these packages would break dependencies:foo is needed by bar-1.0-1
```

​		如果我们就是要删除 foo这个RPM包，可以增加参数 `--nodeps` ,就可以强制删除，但是一般不推荐这样做，因为依赖于该软件包的程序可能无法运行

如：

```ini
$ rpm -e --nodeps foo  --小心使用
```

## 3、YUM

**介绍**：

​		Yum 是一个Shell 软件包**管理器**。基于RPM包管理，能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包。(前提要进行联网)

基本语法：

```ini
yum list|grep xx软件列表 --查询yum服务器是否有需要安装的软件
yum install xxx  下载安装 --安装指定的yum包，默认会安装最新的版本。
```

案例：使用yum的方式来安装firefox指令。

```ini
yum install firefox  --使用该命令，yum会将适合你系统的最新版本的软件包自动的下载到你的系统中。
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831102802.png" alt="image-20200709163310831" style="zoom:80%;" />


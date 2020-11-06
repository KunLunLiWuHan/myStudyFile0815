# 1、安装

# 2、操作

## 1、虚拟机操作

1、快照和系统恢复

可以使用虚拟机的快照功能来进行系统的恢复。

![image-20201106160046155](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201106160046155.png)

在对应的安装目录中有一个比较大的快照文件：

![image-20201106160133109](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201106160133109.png)

## 2、文件系统

1、用户目录

用户目录，就是用户自己的目录。Linux系统上支持多个用户，每个用户一个目录。

特例：超级用户 root ，其用户目录为 /root。

对于普通用户来说，他能操作的目录就只有用户目录。root用户没有限制，可以操作任意文件和目录

2、软链接

即 Windows 下的 "快捷方式"，使用下面的命令来进行软链接操作：

```shell
ln  -s  source  link ---s 表示 soft 软链接 ( 默认为硬 )。link指代快捷方式的名字。
#举例：
ln  -s  example  example2
```

（1）软链接特点：

删除软件接，对原文件没有影响，删除原文件，则软链接失效。

（2）使用下面的命令可以查看软链接详情

```
ls -l /
```

![image-20201106163159317](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201106163159317.png)

## 3、超级用户

1、操作

（1）首次使用时，需要给 root 设置密码

```shell
sudo passwd root
```

（2）切换到 root 用户

```shell
# su 表示 switch user。
# su root 仅仅对当前会话 (终端) 有效，不影响当前桌面环境。
su root
```

（3）退出

```shell
exit
```

## 4、用户环境变量

1、位置

```shell
# 使用 vim 来进行修改里面的配置
~/.profile 
```

当用户打开终端时，会自动运行 .profile ，将变量注入到当前环境中。

此配置只对当前用户有效，因为每个用户都有自己的配置文件 .profile。

## 5、系统环境变量

1、操作

定义在 /etc/profile 中，此中的环境变量对所有用户有效。

以 root 身份执行

```shell
vim /etc/profile
```

但是，一般不直接修改 /etc/profile，因为该文件中有一个执行脚本的嵌套程序，我们在 /etc/profile.d/ 创建一个自定义的脚本，会被执行到。

```shell
vim  /etc/profile.d/test.sh
# 定义一个环境变量
export TOMCAT=/opt/tomcat

# 更新配置文件
source 配置文件
#测试
echo $TOMCAT
```

## 6、PATH环境变量

1、介绍

当我们创建一个可执行脚本 hello.sh，然后直接执行时，会报出下面的错误：

![image-20201106200352841](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201106200352841.png)

PATH，是一个最常见的一个环境变量，用于描述可执行程序的搜索路径。当我们执行下面的指令，会显示出下面的路径，以冒号：分开：

![image-20201106200524067](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201106200524067.png)

默认的，系统从以下目录中搜索可执行程序：

```shell
# 常规的程序，普通用户就可以执行的程序。
/usr/bin  #比如，/usr/bin/tar

#sbin 为超级用户 root 才能执行的程序
/usr/sbin  #比如，/usr/sbin/useradd

#/usr/local/ 是用户安装的程序
/usr/local/bin
/usr/local/sbin
```

2、添加新的环境变量

我们可以在root用户或者普通用户中添加新的PATH变量，注意：需要以冒号：分开。

```shell
vim /etc/profile.d/myprofile.sh

export PATH=$PATH:/opt/tomcat/bin
```

# 3、网络环境

## 1、FTP

1、FTP与防火墙

网络防火墙 Firewall，是一种安全机制，用于减少服务器所受的攻击。

实际部署时，一般需要配置一个防火墙。Linux系统自带的防火墙 ( 一般不用 )，我们而是设立一个独立的防火墙，例如云防火墙。

（1）查看Ubuntu的防火墙状态

```shell
#查看状态(默认的防火墙状态是关闭的),Status: inactive
  ufw status
#关闭防火墙
  ufw disable 
#开启防火墙
  ufw enable 
```

仅仅开启防火墙（enable）还不够，还需要另外配置防火墙规则。

（2）防火墙规则

无论是哪一种防火墙，都需要配置防火规则。比如，允许哪些端口，阻止哪些端口。

```
WEB服务器，一般要允许 TCP/80 端口
MySQL : TCP / 3306
Redis : TCP / 6379

FTP服务器要开放的端口：
（1）控制端口 ：一般是 TCP / 21
（2）数据端口 ：一个自定义的范围，如 TCP / 5500 - 5600
```

## 2、SSH

1、介绍

我们可以使用SSH 方式进行远程登录，是一种C / S 模式。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201106205342227.png" alt="image-20201106205342227" style="zoom:80%;" />

默认的，Ubuntu没有SSH服务器，我们可以使用下面的命令进行检查：

```shell
ls /usr/sbin/sshd
```

2、安装SSH服务器

```shell
#1 安装服务
apt install openssh-server
#2 关闭防火墙
ufw disable
#3 启动服务
service ssh start
```

注意，一定要关闭防火墙，不然在下次启动的时候我们的FinalShell连接不上服务器。

3、细节

（1）在 /etc/init.d 下，是各个系统服务的启动脚本，当我们执行service ssh start命令的时候，其实系统执行的就是该目录下的ssh脚本。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/image-20201106205850139.png" alt="image-20201106205850139" style="zoom:80%;" />

（2）软件包搜索

```shell
apt list | grep ssh
```

（3）删除软件包

```shell
#卸载软件包 
apt remove xxx 
#卸载软件包、并清除配置文件
apt purge xxx 
```

4、SSH允许root用户登录

（1）切换到 root 

（2）先备份一下配置文件

```shell
cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bk
```

（3）编译sshd的配置文件

```shell
vim /etc/ssh/sshd_config

# 将下面的语句改成yes
#PermitRootLogin prohibit-password
PermitRootLogin yes
```

（4）重启ssh服务

```shell
service ssh restart
```

# 4、进程管理

1、前台和后台进程

前台进程: 运行在前台；后台进程: 运行在后台。

比如，执行下面的指令对应前台和后台进程：

```shell
./run_tomcat run  #以前台方式运行 ( CTRL+C 中止)
./run_tomcat start #以后台方式运行
```

区别如下：

（1）前台进程：有控制台, 输出至当前终端，当前进程会被阻塞；后台进程：无控制台, 看不到输出。

（2）前台进程：有父进程，父进程即为当前终端，当终端关闭时，前台进程被一同关闭；后台进程：父进程为系统进程 ( 1 号进程 )，当终端关闭时，后台进程不受影响。

（3）前台进程：使用 CTRL + C 强行终止；后台进程：使用 kill -9 NNN 强行终止 。
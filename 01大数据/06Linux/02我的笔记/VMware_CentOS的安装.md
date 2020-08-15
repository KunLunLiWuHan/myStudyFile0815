# 1 VMware的安装

1. 需要在开机的时候进入BISO界面，开启虚拟化支持。我的联想Y7000P默认已经开启虚拟化支持。我们可以通过任务管理器查看：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\clip_image002.jpg" alt="img" style="zoom:67%;" />

双击下面的文件进行安装：

![image-20200701114818894](D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701114818894.png)

自定义安装：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701114938973.png" alt="image-20200701114938973" style="zoom:80%;" />

用户体验设置：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701115029575.png" alt="image-20200701115029575" style="zoom:67%;" />

桌面选择快捷方式：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701115112840.png" alt="image-20200701115112840" style="zoom:80%;" />

然后，点击安装后，系统会对虚拟机驱动什么的进行注册。

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701115314492.png" alt="image-20200701115314492" style="zoom:80%;" />

输入许可证密钥：

```ini
5A02H-AU243-TZJ49-GTC7K-3C61N
```

点击输入后即可完成。此时在桌面上会出现一个VMware的图标。

到了这一步，我们只是安装好了虚拟机。

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701120020385.png" alt="image-20200701120020385" style="zoom:80%;" />

# 2 CentOS的安装

## 2.1 准备工作

我们装CentOS需要两步走：

1. **先分配虚拟机一个空间**。
2. **再安装CentOS文件**。

接下来的操作是给虚拟机分配一个空间。

以**管理员方式**运行VMware软件，创建一片空间：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701120401332.png" alt="image-20200701120401332" style="zoom:80%;" />![image-20200701120438639](D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701120438639.png)

稍后安装操作系统：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701120438639.png" alt="image-20200701120438639" style="zoom:67%;" />

选择客户端操作系统：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701120654154.png" alt="image-20200701120654154" style="zoom:67%;" />

虚拟机的命名：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701121137385.png" alt="image-20200701121137385" style="zoom:67%;" />

指定磁盘大小：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701121252529.png" alt="image-20200701121252529" style="zoom:67%;" />

完成后界面：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701125950196.png" alt="image-20200701125950196" style="zoom:67%;" />

之后，我们需要对新建立centos01进行设置：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701140827221.png" alt="image-20200701140827221" style="zoom:80%;" />

内存：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701140915949.png" alt="image-20200701140915949" style="zoom:80%;" />

处理器设置：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701141146558.png" alt="image-20200701141146558" style="zoom:67%;" />

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701141240837.png" alt="image-20200701141240837" style="zoom:67%;" />

CD/DVD(IDE)配置，在这里配置centos的安装文件：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701141627715.png" alt="image-20200701141627715" style="zoom:67%;" />

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701141429236.png" alt="image-20200701141429236" style="zoom:80%;" />

网络适配器：NAT模式

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701141721417.png" alt="image-20200701141721417" style="zoom:80%;" />

**虚拟机三种网络配置方式的说明**：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701142459683.png" alt="image-20200701142459683" style="zoom:80%;" />

1. 桥接模式

优点是大家都在同一个网段中，可以相互进行通讯。

缺点是ip地址有限，可能造成ip冲突。

2. NAT(网络地址转换模式)

优点是虚拟机不占用其他的ip,不会造成ip冲突。

缺点是其他人不能和虚拟机进行通讯。

3. 主机模式

单独的一台电脑。

其他的配置默认：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701142559864.png" alt="image-20200701142559864" style="zoom:80%;" />

点击确定后，就完成了下面图示的安装。

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701142722945.png" alt="image-20200701142722945" style="zoom:80%;" />

## 2.2 安装CentOS

点击开启虚拟机，进入到下面的界面。

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701143225834.png" alt="image-20200701143225834" style="zoom: 67%;" />

然后是对于虚拟机的安装，双击虚拟机的安装界面，才能进入，假如想退出，我们Ctrl+Alt键就退出虚拟机（或者释放光标）了。

之后检查界面我们选择`skip`，语言选择中文，键盘选择美国英语式即可。

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701143928805.png" alt="image-20200701143928805" style="zoom:80%;" />

忽略数据：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701144023303.png" alt="image-20200701144023303" style="zoom:80%;" />

为主机命名：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701144128435.png" alt="image-20200701144128435" style="zoom:80%;" />

设置根密码：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701144318057.png" alt="image-20200701144318057" style="zoom:80%;" />

可以使用复杂密码生成器进行设置。

创建自定义布局：手动分区

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701144725589.png" alt="image-20200701144725589" style="zoom:80%;" />

Linux至少有三个分区,下面开始创建分区：

![image-20200701144932235](D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701144932235.png)

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701145257285.png" alt="image-20200701145257285" style="zoom: 80%;" />

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701145636462.png" alt="image-20200701145636462" style="zoom: 80%;" />

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701145842321.png" alt="image-20200701145842321" style="zoom:80%;" />

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701150025339.png" alt="image-20200701150025339" style="zoom:80%;" />

之后进行下一步：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701150150249.png" alt="image-20200701150150249" style="zoom:80%;" />

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701150233448.png" alt="image-20200701150233448" style="zoom:80%;" />

安装CentOs 6的一些需要的软件：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701150347977.png" alt="image-20200701150347977" style="zoom:80%;" />

点击下一步，我们进行选择我们所需要的服务：

1. 基本系统：兼容程序库、基本和调试工具。
2. 应用程序：互联网浏览器。
3. 桌面：默认即可。
4. 语言支持：中文支持。

选择完成之后点击进行下一步，进入下面的安装界面：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701150956640.png" alt="image-20200701150956640" style="zoom:80%;" />

安装成功界面：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701151349119.png" alt="image-20200701151349119" style="zoom:80%;" />

点击“重新引导”即可。引导完毕之后，会进入欢迎界面，此时我们还有一些东西要设置一下：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701151645835.png" alt="image-20200701151645835" style="zoom:80%;" />

不创建用户：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701151809548.png" alt="image-20200701151809548" style="zoom:80%;" />

启用Kdump：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701152019169.png" alt="image-20200701152019169" style="zoom:80%;" />

继续前进：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701152155213.png" alt="image-20200701152155213" style="zoom:80%;" />

进行登录，进入主界面：

![image-20200701152353497](D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200701152353497.png)

## 2.3 克隆CentOS

参考网址：https://www.cnblogs.com/tijun/p/7520611.html，https://www.cnblogs.com/lijiaman/p/10924761.html。

1. 在root的命令下进入系统，编辑下面的文件

   首先，创建的全新的虚拟机文件名称为(我们使用的是完整克隆)：

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200715083453619.png" alt="image-20200715083453619" style="zoom:67%;" />

```ini
#删除eth0的内容，复制ATTR的地址，并将eth1修改该为eth0
vi /etc/udev/rules.d/70-persistent-net.rules
```

​		![image-20200715094732974](D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200715094732974.png)

2. 编辑下面的文件

```ini
#更改MAC地址和ip地址
vim /etc/sysconfig/network-scripts/ifcfg-eth0
```

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200715095022053.png" alt="image-20200715095022053" style="zoom:80%;" />

3. 重启网卡查看配置是否正确

```ini
service network restart
```

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200715095232531.png" alt="image-20200715095232531" style="zoom:80%;" />

​		我们需要使用命令`reboot`重启服务，然后使用上面的命令来重新查看网络配置。

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200715095450311.png" alt="image-20200715095450311" style="zoom:80%;" />

4. 修改主机名称

```ini
vi /etc/sysconfig/network

#之后使用该命令查看，会发现还没有配置成功
hostname
```

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200712153901728.png" alt="image-20200712153901728" style="zoom:80%;" />

5. 修改主机ip地址映射

```ini
#1.在虚拟机中修改映射
vi /etc/hosts
```

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200715095847552.png" alt="image-20200715095847552" style="zoom:67%;" />

```ini
#2.在windows的C:\Windows\System32\drivers\etc\hosts文件中配置
```

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200715100104586.png" alt="image-20200715100104586" style="zoom:67%;" />

6. 测试

```ini
# windows中连接服务器
ping hadoop101

# hadop102中连接外网
ping www.baidu.com 
```

额外操作：		

（1）配置zookeeper用户具有root权限（如果想要控制某个用户(或某个组用户)只能执行root权限中的一部分命令, 或者允许某些用户使用sudo时不需要输入密码）。

```ini
#在root权限下，使用下面命令
 vim /etc/sudoers
# 添加下面的代码
zookeeper    ALL=(ALL)       ALL
```

<img src="D:\04桌面\Spring Boot学习\03学习文件\VMware_CentOS的安装.assets\image-20200722150500287.png" alt="image-20200722150500287" style="zoom:80%;" />

（2）修改系统启动级别

```ini
sudo vim /etc/inittab

#进行如下更改
id:3:initdefault:
```

（3）免密登录

跟用户相关，必须是普通用户。对于Hadoop集群（Hadoop101、Hadoop102、Hadoop103）三台机器之间都要做（此时，打开三个窗口，输出下面的命令，注意：此时的SecureCRT对应的状态为<发送交互到所有标签>）。

```ini
#1、生成密钥
ssh-keygen

#2、将密钥进行配置
 ssh-copy-id hadoop102(主机名)
```

（4）时间同步

+ 方法1：手动同步

```ini
#-s<字符串>：根据字符串来设置日期与时间。字符串前后必须加上双引号
date -s "时间"
```

+ 方法2：和时间服务器同步

```ini
#也可以是：ntp2.aliyun.com
sudo ntpdate ntp1.aliyun.com

#使用下面命令检查,可以发现三台时间一致。
date
```








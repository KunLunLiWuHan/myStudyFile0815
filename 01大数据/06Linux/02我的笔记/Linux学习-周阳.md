## 1、介绍

1、概述

​		Linux 英文解释为 Linux is not Unix。

​		Linux内核最初只是由芬兰人林纳斯·托瓦兹（Linus Torvalds）在赫尔辛基大学上学时出于个人爱好而编写的。
Linux是一套免费使用和自由传播的类Unix操作系统，是一个基于POSIX和UNIX的多用户、多任务、支持多线程和多CPU的操作系统。Linux能运行主要的UNIX工具软件、应用程序和网络协议。它支持32位和64位硬件。


​		Linux继承了Unix以网络为核心的设计思想，是一个性能稳定的多用户网络操作系统。

​		目前市面上较知名的发行版有：Ubuntu、RedHat、CentOS、Debain、Fedora、SuSE、OpenSUSE

2、Linux应用领域

​		今天各种场合都有使用各种Linux发行版，从嵌入式设备到超级计算机，并且在服务器领域确定了地位，
通常服务器使用LAMP（Linux + Apache + MySQL + PHP）或LNMP（Linux + Nginx+ MySQL + PHP）组合。

​		目前Linux不仅在家庭与企业中使用，并且在政府中也很受欢迎。
​		巴西联邦政府由于支持Linux而世界闻名。
​		有新闻报道俄罗斯军队自己制造的Linux发布版的，做为G.H.ost项目已经取得成果。印度的Kerala联邦计划在向全联邦的高中推广使用Linux。
​		中华人民共和国为取得技术独立，在龙芯过程中排他性地使用Linux。

3、下载地址

centos6.4 下载地址：

```http
#网易镜像
http://mirrors.163.com/centos/6/isos/

#搜狐镜像
http://mirrors.sohu.com/centos/6/isos/
```

4、操作

（1）Linux里面一切皆文件。

（2）Linux里面没有后缀名这一说。

5、和windows区别

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132451.png" alt="image-20200826094229157" style="zoom:67%;" />

## 2、远程登录与SSH服务

1、Linux远程登录及相关工具介绍

​		Linux一般作为服务器使用，而服务器一般放在机房，你不可能在机房操作你的Linux服务器。这时我们就需要远程登录到Linux服务器来管理维护系统。

​		Linux系统中是通过SSH服务实现的远程登录功能（此时，开发人员可以在上面做一些操作），默认ssh服务端口号为 22。Window系统上 Linux 远程登录客户端有SecureCRT, Putty, SSH Secure Shell等

2、使用下面的指令进行操作ssh

```shell
/etc/init.d/sshd status
service sshd status


/etc/init.d/sshd restart

/etc/init.d/sshd stop
```

（1）SSH服务命令查看

```shell
/etc/init.d/sshd

#查看状态
/etc/init.d/sshd status
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132452.png" alt="image-20200826104539303" style="zoom:67%;" />

（2）重启和关闭

```shell
/etc/init.d/sshd restart

/etc/init.d/sshd stop
```

3、查看SecureCRT端编码格式

​		在SecureCRT端依然无法正常显示中文，可能是由于Linux系统中默认的，字符编码非UTF8所致。使用下面的方式查看：

用root用户登录

```shell
vi /etc/sysconfig/i18n
```

​		如果安装系统为中文系统，则修改【LANG=“zh_CN.UTF-8”】，如果安装系统为英文系统，则修改【LANG=“en_US.UTF-8”】。
保存文件。 断开SSH，重新登录。就正常了。

## 3、Linux启动和加载顺序

### 1、man介绍

1、显示说明

语法格式：

```shell
man date

#第二种
date --help
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132453.png" alt="image-20200826152212437" style="zoom:50%;" />

+ NAME  命令的名称和单行描述
+ SYNOPSIS 怎样使用命令
+ DESCRIPTION 命令功能的深入讨论
+ EXAMPLES  怎样使用命令的例子
+ SEE ALSO  相关主题（通常是手册页）

2、数字说明

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132454.png" alt="image-20200826152348216" style="zoom: 67%;" />

### 2、6个终端

1、基本指令

```ini
Ctrl+Alt+F1(->F6)

#图形化界面
Ctrl+Alt+F7
```

2、查看连接的tty

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132455.png" alt="image-20200826152840688" style="zoom:67%;" />

3、使用下面的指令查看运行系统运行级别

```shell
cat /etc/inittab
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132456.png" alt="image-20200826153023392" style="zoom: 67%;" />

Linux系统有7个运行级别(runlevel)：常用的是3和5

+ 运行级别0：系统停机状态，系统默认运行级别不能设为0，否则不能正常启动

+ 运行级别1：单用户工作状态，root权限，用于系统维护，禁止远程登陆

+ 运行级别2：多用户状态(没有NFS)，没有网络服务

+ 运行级别3：完全的多用户状态(有NFS)，登陆后进入控制台命令行模式

+ 运行级别4：系统未使用，保留

+ 运行级别5：X11表示控制台，进入图形界面

+ 运行级别6：系统正常关闭并重启，默认运行级别不能设为6，否则不能正常启动

### 3、启动过程

1、内核引导

​		接通电源BIOS自检，按照BIOS中设置的启动设备（通常是硬盘）来启动，操作系统接管硬件以后，首先读入 /boot 目录下的内核文件。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132457.png" alt="image-20200826153225728" style="zoom:67%;" />

2、运行init

​		init 进程是系统所有进程的起点，你可以把它比拟成系统所有进程的老祖宗，没有这个进程，系统中任何进程都不会启动。init 程序首先是需要读取配置文件 /etc/inittab。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132458.png" alt="image-20200826153252617" style="zoom:67%;" />

​		许多程序需要开机启动。它们在Windows叫做"服务"（service），在Linux就叫做"守护进程"（daemon）。init进程的一大任务，就是去运行这些开机启动的程序。

​		但是，不同的场合需要启动不同的程序，比如用作服务器时，需要启动Apache，用作桌面就不需要。Linux允许为不同的场合，分配不同的开机启动程序，这就叫做"运行级别"（runlevel）。
​		也就是说，启动时根据"运行级别"，确定要运行哪些程序。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132459.png" alt="image-20200826153320801" style="zoom:67%;" />

3、系统初始化

​		在init的配置文件中有这么一行：

```shell
 si::sysinit:/etc/rc.d/rc.sysinit
 
 #执行下面的命令
 /etc/rc.d/rc.sysinit
```

​		而rc.sysinit是一个bash shell的脚本，它主要是完成一些系统初始化的工作，rc.sysinit是每一个运行级别都要首先运行的重要脚本它主要完成的工作有：激活交换分区，检查磁盘，加载硬件模块以及其它一些需要优先执行任务。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132500.png" alt="image-20200826153506644" style="zoom:67%;" />

4、建立终端

​	rc执行完毕后，返回init。这时基本系统环境已经设置好了，各种守护进程也已经启动了。init接下来会打开6个终端，以便用户登录系统。

5、用户登录系统

一般来说，用户的登录方式有三种：
（1）命令行登录

（2）图形界面登录

（3）ssh远程登录


<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132501.png" alt="image-20200826153747565" style="zoom: 67%;" />

### 4、关机

​		在linux领域内大多用在服务器上，很少遇到关机的操作。毕竟服务器上跑一个服务是永无止境的，除非特殊情况下，不得已才会关机 。
​		正确的关机流程为：sync > shutdown > reboot > halt

```ini
（1）sync   将数据由内存同步到硬盘中

（2）shutdown –h 10 ‘This server will shutdown after 10 mins’ 这个命令告诉大家，计算机将在10分钟后关机，并且会显示在登陆用户的当前屏幕中

（3）Shutdown –h now 立马关机

（4）Shutdown –r now 系统立马重启

（5）reboot 就是重启，等同于 shutdown –r now

（6）halt 关闭系统，等同于shutdown –h now 和 poweroff
```

​	最后总结一下，不管是重启系统还是关闭系统，首先要运行sync命令，把内存中的数据写到磁盘中。

## 4、VI/VIM编辑器

### 1、介绍

​		所有的 Unix Like 系统都会内建 vi 文书编辑器，其他的文书编辑器则不一定会存在。但是目前我们使用比较多的是 vim 编辑器。

​		Vim 具有程序编辑的能力，可以主动的以字体颜色辨别语法的正确性，方便程序设计。Vim是从 vi 发展出来的一个文本编辑器。代码补完、编译及错误跳转等方便编程的功能特别丰富，在程序员中被广泛使用。

​		简单的来说vi 是老式的字处理器，不过功能已经很齐全了，但是还是有可以进步的地方。 vim 则可以说是程序开发者的一项很好用的工具。 连 vim 的官方网站 (http://www.vim.org) 自己也说 vim 是一个程序开发工具而不是文字处理软件。

### 2、三种模式

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132502.png" alt="image-20200705213239207" style="zoom:67%;" />

1、一般模式

​		以 vi 打开一个档案就直接进入一般模式了(这是默认的模式)。在这个模式中， 你可以使用『上下左右』按键来移动光标，你可以使用『删除字符』或『删除整行』来处理档案内容， 也可以使用『复制、贴上』来处理你的文件数据。

```ini
dd  #删除当前行
d5d #删除当前行后的5行

u   #撤销上一步操作
yy  #复制光标当前一行
y数字y #复制多行

p   #箭头移动到目的行粘贴

x   #删除一个字母，相当于del
X   #相当于backspace

dw  #删除一个词（放在首字母去识别）
yw  #复制一个词

shift+^  #移动到行头
shift+$  #移动到行尾

shift+g    #移动到页尾
1+shift+g  #移动到页头
N+shift+g  #移动到目标行
```

2、编辑模式

​		在一般模式中可以进行删除、复制、粘贴等等的动作，但是却无法编辑文件内容的！ 要等到你按下『i, I, o, O, a, A, r, R』等任何一个字母之后才会进入编辑模式。

​		注意了！通常在 Linux 中，按下这些按键时，在画面的左下方会出现『INSERT 或 REPLACE 』的字样，此时才可以进行编辑。而如果要回到一般模式时， 则必须要按下『Esc』这个按键即可退出编辑模式。

```ini
i  #当前光标前插入
a  #当前光标后插入
o  #当前光标行的下一行
ESC #退出编辑模式
```

3、指令模式

​		在一般模式当中，输入『 : / ?』3个中的任何一个按钮，就可以将光标移动到最底下那一行。
​		在这个模式当中， 可以提供你『搜寻资料』的动作，而读取、存盘、大量取代字符、离开 vi 、显示行号等的动作则是在此模式中达成的！	

```shell
w
q
! #感叹号强制执行  -> :wq! #保存并强制退出
:%s/old/new/g  #替换 将old 替换成new 

/被查找词 #查找 n是查找下一个，shift+n是往上查找
?被查找词 #查找 n是查找上一个，shift+n是往下查找
```

4、总结

（1）第一部分

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132503.png" alt="image-20200826163216714" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132504.png" alt="image-20200826163227449" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132505.png" alt="image-20200826163234737" style="zoom:67%;" />

（2）第二部分

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132506.png" alt="image-20200826163259120" style="zoom:67%;" />

（3）第三部分

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132507.png" alt="image-20200826163314357" style="zoom:67%;" />

## 5、用户和用户组

### 1、介绍


Linux系统是一个多用户多任务的分时操作系统，任何一个要使用系统资源的用户，都必须首先向系统管理员申请一个账号，然后以这个账号的身份进入系统。

用户的账号一方面可以帮助系统管理员对使用系统的用户进行跟踪，并控制他们对系统资源的访问；另一方面也可以帮助用户组织文件，并为用户提供安全性保护。每个用户账号都拥有一个惟一的用户名和各自的口令。

用户在登录时键入正确的用户名和口令后，就能够进入系统和自己的主目录。
​	实现用户账号的管理，要完成的工作主要有如下几个方面：

+ 用户账号的添加、删除与修改。
+ 用户口令的管理。
+ 用户组的管理。

### 2、用户

```ini
# 也可以在用户名之前加上-m参数，表示在 /home 下添加用户目录
useradd 用户名  #新增用户
useradd -g 用户组 用户名 #在创建用户的时候将用户加入用户组。
passwd 新建用户名，之后根据提示输入密码 #对用户设置密码
id 用户名 #用户存在否
whoami/who am i #查看当前用户

su - 用户名 #切换用户。#超级管理员，$普通用户

userdel    用户名 #删除用户，删除用户但保存用户主目录。
userdel -r 用户名 #删除用户，删除用户和用户主目录，都删除。

usermod -g 用户组 用户名 #修改用户组
```

在登录系统时，默认不允许以 root 用户登录。只有特殊的用户，才能执行 sudo。Linux下，把能执行sudo命令的用户叫 sudoer。

### 3、用户组

（1）介绍

Linux 下可以创建多个用户，可以用组进行管理用户。


每个用户都有一个用户组，默认的，系统会给当前用户同名的组 ，也就是说这个组里只有他一个人。不同Linux 系统对用户组的规定有所不同，如Linux下的用户属于与它同名的用户组，这个用户组在创建用户时同时创建。

用户组的管理涉及用户组的添加、删除和修改。组的增加、删除和修改实际上就是对/etc/group文件的更新。

用户组类似于角色，系统可以对有共性的多个用户进行统一的管理。

（2）命令

```ini
groupadd 组名 #新增组
groupdel 组名 #删除组

groupmod 组名 #修改组
groupmod -n 新组名 老组名

# 查看用户
cat /etc/group
cat /etc/passwd
```

### 4、与用户账号有关的系统文件

1、介绍


完成用户管理的工作有许多种方法，但是每一种方法实际上都是对有关的系统文件进行修改。与用户和用户组相关的信息都存放在一些系统文件中，这些文件包括/etc/passwd, /etc/shadow, /etc/group等。

2、/etc/passwd

```shell
cat /etc/passwd
```

Linux系统中的每个用户都在/etc/passwd文件中有一个对应的记录行，它记录了这个用户的一些基本属性。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132508.png" alt="image-20200826183209678" style="zoom:67%;" />

从上面的例子我们可以看到，/etc/passwd中一行记录对应着一个用户，每行记录又被冒号(:)分隔为7个字段，其格式和具体含义如下：

```ini
用户名:口令:用户标识号:组标识号:注释性描述:主目录:登录Shell
```

+ 用户名："用户名"是代表用户账号的字符串。通常长度不超过8个字符，并且由大小写字母和/或数字组成。登录名中不能有冒号(:)，因为冒号在这里是分隔符。为了兼容起见，登录名中最好不要包含点字符(.)，并且不使用连字符(-)和加号(+)打头。

+ 口令：“口令”一些系统中，存放着加密后的用户口令字。虽然这个字段存放的只是用户口令的加密串，不是明文，但是由于/etc/passwd文件对所有用户都可读，所以这仍是一个安全隐患。

  因此，现在许多Linux 系统（如SVR4）都使用了shadow技术，把真正的加密后的用户口令字存放到/etc/shadow文件中，而在/etc/passwd文件的口令字段中只存放一个特殊的字符，例如“x”或者“*”。

+ 用户标识号：“用户标识号”是一个整数，系统内部用它来标识用户。
  一般情况下它与用户名是一一对应的。如果几个用户名对应的用户标识号是一样的，系统内部将把它们视为同一个用户，但是它们可以有不同的口令、不同的主目录以及不同的登录Shell等。

  通常用户标识号的取值范围是0～65535。0是超级用户root的标识号，1～99由系统保留，作为管理账号，普通用户的标识号从100开始。在Linux系统中，这个界限是500。

+ 组标识号：该字段记录的是用户所属的用户组。它对应着/etc/group文件中的一条记录。

+ 注释性描述：该字段记录着用户的一些个人情况。
  例如用户的真实姓名、电话、地址等，这个字段并没有什么实际的用途。在不同的Linux 系统中，这个字段的格式并没有统一。

  在许多Linux系统中，这个字段存放的是一段任意的注释性描述文字，用做finger命令的输出。

+ 主目录：该目录也就是用户的起始工作目录。
  它是用户在登录到系统之后所处的目录。在大多数系统中，各用户的主目录都被组织在同一个特定的目录下，而用户主目录的名称就是该用户的登录名。各用户对自己的主目录有读、写、执行（搜索）权限，其他用户对此目录的访问权限则根据具体情况设置。

+ 登录shell：用户登录后，要启动一个进程，负责将用户的操作传给内核，这个进程是用户登录到系统后运行的命令解释器或某个特定的程序，即Shell。
  Shell是用户与Linux系统之间的接口。Linux的Shell有许多种，每种都有不同的特点。常用的有sh(Bourne Shell), csh(C Shell), ksh(Korn Shell), tcsh(TENEX/TOPS-20 type C Shell), bash(Bourne Again Shell)等。
  系统管理员可以根据系统情况和用户习惯为用户指定某个Shell。如果不指定Shell，那么系统使用sh为默认的登录Shell，即这个字段的为/bin/sh。
  用户的登录Shell也可以指定为某个特定的程序（此程序不是一个命令解释器）。利用这一特点，我们可以限制用户只能运行指定的应用程序，在该应用程序运行结束后，用户就自动退出了系统。有些Linux 系统要求只有那些在系统中登记了的程序才能出现在这个字段中。

韩老师讲授中的图片为：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132509.png" alt="image-20200706084342680" style="zoom:67%;" />

3、/etc/shadow

```shell
cat /etc/shadow
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132510.png" alt="image-20200826183952390" style="zoom:67%;" />

​		由于/etc/passwd文件是所有用户都可读的，如果用户的密码太简单或规律比较明显的话，一台普通的计算机就能够很容易地将它破解，因此对安全性要求较高的Linux系统都把加密后的口令字分离出来，单独存放在一个文件中，这个文件是/etc/shadow文件。 有超级用户才拥有该文件读权限，这就保证了用户密码的安全性。

​		它的文件格式与/etc/passwd类似，由若干个字段组成，字段之间用":"隔开。这些字段是：

```ini
登录名:加密口令:最后一次修改时间:最小时间间隔:最大时间间隔:警告时间:不活动时间:失效时间:标志
```

+ "登录名"是与/etc/passwd文件中的登录名相一致的用户账号

+ "口令"字段存放的是加密后的用户口令字，长度为13个字符。如果为空，则对应用户没有口令，登录时不需要口令；如果含有不属于集合 { ./0-9A-Za-z }中的字符，则对应的用户不能登录。

+ "最后一次修改时间"表示的是从某个时刻起，到用户最后一次修改口令时的天数。时间起点对不同的系统可能不一样。例如在SCO Linux 中，这个时间起点是1970年1月1日。

+ "最小时间间隔"指的是两次修改口令之间所需的最小天数。

+ "最大时间间隔"指的是口令保持有效的最大天数。

+ "警告时间"字段表示的是从系统开始警告用户到用户密码正式失效之间的天数。

+ "不活动时间"表示的是用户没有登录活动但账号仍能保持有效的最大天数。

+ "失效时间"字段给出的是一个绝对的天数，如果使用了这个字段，那么就给出相应账号的生存期。期满后，该账号就不再是一个合法的账号，也就不能再用来登录了。

韩老师讲授中的图片为：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132511.png" alt="image-20200706084543566" style="zoom:67%;" />

4、/etc/group

​		将用户分组是Linux 系统中对用户进行管理及控制访问权限的一种手段。每个用户都属于某个用户组；一个组中可以有多个用户，一个用户也可以属于不同的组。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132512.png" alt="image-20200706084722297" style="zoom:80%;" />

用户组的所有信息都存放在/etc/group文件中。此文件的格式也类似于/etc/passwd文件，由冒号(:)隔开若干个字段，这些字段有：

```ini
 组名:口令:组标识号:组内用户列表
```

+ "组名"是用户组的名称，由字母或数字构成。与/etc/passwd中的登录名一样，组名不应重复。
+ "口令"字段存放的是用户组加密后的口令字。一般Linux 系统的用户组都没有口令，即这个字段一般为空，或者是*。
+ "组标识号"与用户标识号类似，也是一个整数，被系统内部用来标识组。
+ "组内用户列表"是属于这个组的所有用户的列表/b]，不同用户之间用逗号(,)分隔。这个用户组可能是用户的主组，也可能是附加组。

## 6、常用基本命令

### 1、时间日期类

```
date
cal
```

### 2、文件目录类

1、ls-列出目录的内容

```shell
ls [选项] [目录或是文件]
选项与参数：
-a ：全部的文件，连同隐藏档( 开头为 . 的文件) 一起列出来(常用)
-d ：仅列出目录本身，而不是列出目录内的文件数据(常用)
-l ：长数据串列出，包含文件的属性与权限等等数据；(常用) 同ll
```

其中：每行列出的信息依次是： 

文件类型与权限 链接数 文件属主 文件属组   文件大小用byte来表示   建立或最近修改的时间 名字 

```shell
ls –R  #递归查看目录
```

2、mkdir-创建一个新目录

```shell
mkdir -p #创建多层目录
```

3、rmdir-删除一个空目录

4、mv-移动文件与目录或修改名称

5、cat-由第一行开始显示文件内容

```ini
选项与参数：
-A ：相当於 -vET 的整合选项，可列出一些特殊字符而不是空白而已；
-b ：列出行号，仅针对非空白行做行号显示，空白行不标行号！
-E ：将结尾的断行字节 $ 显示出来；
-n ：列印出行号，连同空白行也会有行号，与 -b 的选项不同；
-T ：将 [tab] 按键以 ^I 显示出来；
-v ：列出一些看不出来的特殊字符
```

tac-从最后一行开始显示

6、more-一页一页的显示文件内容

```ini
空白键 (space)：代表向下翻一页；
Enter       ：代表向下翻『一行』；
q           ：代表立刻离开 more ，不再显示该文件内容。
b 或 [ctrl]-b ：代表往回翻页，不过这动作只对文件有用，对管线无用。
```

7、less 与 more 类似

```ini
空白键   ：向下翻动一页；
[pagedown]：向下翻动一页；
[pageup] ：向上翻动一页；
/字串    ：向下搜寻『字串』的功能；
?字串    ：向上搜寻『字串』的功能；
n        ：重复前一个搜寻 (与 / 或 ? 有关！)
N        ：反向的重复前一个搜寻 (与 / 或 ? 有关！)
q        ：离开 less 这个程序；
```

8、head-只看头几行

9、tai-只看尾巴几行

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132513.png" alt="image-20200826204941495" style="zoom: 67%;" />

10、重定向命令

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132514.png" alt="image-20200826205019466" style="zoom:67%;" />

### 3、文件权限类

#### 1、介绍

（1）概述

在linux中的每个用户必须属于一个组，不能独立于组外。在linux中每个文件有所有者（归属于谁）、所在组（属于那个组）、其它组的概念。

其他组：除文件的所有者和所在组的用户外，系统的其它用户都是文件的其它组。

（2）举例

```shell
#执行指令ll abc.txt
-rwxrw-r-- 1 root police 1213 Feb 2 09:39 abc.txt
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132515.png" alt="image-20200708170144778" style="zoom:80%;" />

```ini
0-9位说明
第0位确定文件类型(说明: -:普通文件, d:目录，l : 连接文件, c: 字符设备文件[键盘,鼠标] b: 块设备文件[硬盘] )
第1-3位确定所有者（该文件的所有者）拥有该文件的权限。r: 读 ， w : 写权限 x:  执行权限（-表示没有权限）  
第4-6位确定所属组（同用户组的）拥有该文件的权限（只有读的权限）
第7-9位确定其他用户拥有该文件的权限 
1: 如果是文件，表示硬链接的数目； 如果是目录，则表示有多少个子目录
1213： 表示文件大小，如果是目录，则统一为 4096。
Feb 2 09:39：文件最后的修改时间。
```

#### 2、指令操作

1、chmod

改变文件或者目录权限。

文件: r-查看；w-修改；x-执行文件。

目录: r-列出目录内容；w-在目录中创建和删除；x-进入目录。

删除一个文件的前提条件:该文件所在的目录有**写权限**，你才能删除该文件。

方法1：通过数字变更修改权限

```shell
chmod 权限值（2进制） 文件/目录
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132516.png" alt="image-20200826230856389" style="zoom:67%;" />

同组用户不能创建新文件：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132517.png" alt="image-20200826231129045" style="zoom:80%;" />

方法2：通过+、-、=符号来变更权限。

其中，u:所有者 g:所有组 o:其他人 a:所有人(u、g、o的总和)

基本语法：

```shell
# 给所有者rwx, 给所在组的用户 rx, 给其他人 x
chmod   u=rwx,g=rx,o=x   文件、目录 

chmod   o+w    文件、目录 --给其它用户增加w 的权限
chmod   a-x    文件、目录 --给所有用户 去掉 x权限
```

2、chown

```shell
#改变文件或目录的所有者（change owner 修改文件的属主）
chown  newowner(最终落地用户)  file(将被修改的文件
)  --改变文件的所有者

#改变文件或者目录的所有者和所有组
chown  newowner:newgroup  file 
```

一般来说，每个用户只操作自己的用户目录，所以 chown 命令并不常用。

3、chgrp

```shell
#改变文件或目录的所有组
chgrp newgroup(最终目的组)  file(文件或目录) 
```

4、umask

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132518.png" alt="image-20200826231443888" style="zoom: 80%;" />

默认规则：
 文件是666 减去 022等于644，
   十进制的6等于二进制的110，所以第一组就是rw-
   十进制的4等于二进制的100,  所以第二组就是r--
   十进制的4等于二进制的100,  所以第三组就是r--

 目录是777 减去 022等于755，依次类推。

### 4、磁盘分区类

1、分区

（1）基本语法

```shell
fdisk -l
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132519.png" alt="image-20200826214809766" style="zoom:50%;" />

+ s表示scsi硬盘

+ d表示disk

+ a表示第一块盘(a为基本盘(第一块硬盘，两块就会有b)，b为基本从属盘，c为辅助主盘，d为辅助从属盘)

+ 数字，前四个分区用1~4表示，它们是主分区或扩展分区，从5开始才是逻辑分区

  上面四个放在一块表示我们计算机里面的第一块SCSI硬盘。sda1, sda2表示sda这块硬盘上的两个分区。

（2）挂载/卸载

​		对于Linux用户来讲，不论有几个分区，分别分给哪一个目录使用，它总归就是一个根目录、一个独立且唯一的文件结构Linux中每个分区都是用来组成整个文件系统的一部分，它在用一种叫做“挂载”的处理方法，它整个文件系统中包含了一整套的文件和目录，并将一个分区和一个目录联系起来，要载入的那个分区将使它的存储空间在这个目录下获得。

关键词：分区 目录 挂载

目录挂在那个分区下面。

```shell
mount 【参数】  设备名称   落地挂载点目录
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132520.png" alt="image-20200826215315098" style="zoom:50%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132521.png" alt="image-20200826215342089" style="zoom:67%;" />

2、硬盘

（1）基本语法

```shell
#-h ：以人们较易阅读的 GBytes, MBytes, KBytes 等格式自行显示；
df -h
```

列出文件系统的整体磁盘使用量,检查文件系统的磁盘空间占用情况。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132522.png" alt="image-20200826215531600" style="zoom: 67%;" />

### 5、搜索查找类

1、find-查找文件或者目录

```shell
#命令
find+搜索路径+参数+搜索关键字

#按文件名
find /home/esop -name  't*'
#按拥有者
find /home/esop -user esop
```

2、grep-在文件内搜索字符串匹配的行并输出

```shell
#命令
grep+参数+查找内容+源文件

参数：
－c：只输出匹配行的计数。
－I：不区分大小写(只适用于单字符)。
－h：查询多文件时不显示文件名。
－l：查询多文件时只输出包含匹配字符的文件名。
－n：显示匹配行及行号。（推荐）
－s：不显示不存在或无匹配文本的错误信息。
－v：显示不包含匹配文本的所有行。
```

### 6、进程线程类

程序 Program：指一个程序文件，如 notepad.exe

进程 Process：当一个程序运行起来，在操作系统内创建一条记录，用于描述和控制它的运行。比如，打开多个 notepad.exe ，则得到多个进程。

1、ps

（1）天字一号进程是所有进程的父进程

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132523.png" alt="image-20200826220035004" style="zoom:80%;" />

（2）查看进程

（a）ps -aux

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132524.png" alt="image-20200826220127383" style="zoom: 80%;" />

```shell
#再利用一个管道符号导向到grep去查找特定的进程,然后再对特定的进程进行操作
ps -aux|grep xx
```

（b）ps -ef

```shell
#以全格式显示当前所有的进程
ps -ef

参数
-e 显示所有进程。
-f 全格式。
```

（c）ps显示的信息选项

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132525.png" alt="image-20200826220325299" style="zoom:67%;" />

2、netstat

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132526.png" alt="image-20200826220345987" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831132527.png" alt="image-20200826220351998" style="zoom:80%;" />

3、top-进程监视

先用 ps 命令查找目标进程的PID号，然后使用下面的指令查看进程情况：

```
top -p NNN（进程的PID号）
```

### 7、压缩和解压类

（1）gzip/gunzip

压缩文件，只能将文件压缩为*.gz文件。

基本语法：

```shell
gzip 文件
gunzip 文件.gz
```

特点：

​		只能压缩文件不能压缩目录，不保留原来的文件。

（2）zip/gzip

压缩文件和目录的命令，window/linux通用且可以压缩目录且保留源文件。

基本语法：

```shell
#压缩。-r 压缩目录
zip mypackage.zip 1.txt 2.txt

#解压
unzip mypackage.zip
```

（3）tar

打包目录,压缩后的文件格式.tar.gz。

基本语法：

```shell
#压缩
tar -zcvf  XXX.tar.gz   n1.txt    n2.txt

#解压
tar -zxvf  XXX.tar.gz

参数：
-c 产生.tar打包文件
 
-v 显示详细信息
-f 指定压缩后的文件名
-z 打包同时压缩
 
-x 解包.tar文件
```
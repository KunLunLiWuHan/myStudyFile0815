# 1 shell编程

1、介绍

Shell是一个命令行解释器，它为用户提供了一个向Linux内核发送请求以便运行程序的界面系统级程序，用户可以用Shell来启动、挂起、停止甚至是编写一些程序。

2、学习shell的原因

（1）Linux运维工程师在进行服务器集群管理时，需要编写Shell程序来进行服务器管理。
（2）对于JavaEE和Python程序员来说，工作的需要，你的老大会要求你编写一些Shell脚本进行程序或是服务器的维护，比如编写一个定时备份数据库的脚本。
（3）对于大数据程序员来说，需要编写Shell程序来管理集群。

3、Shell 

它是一个用C语言编写的程序，它是用户使用Linux的桥梁。Shell既是一种命令语言，又是一种程序设计语言。

每个人在成功登陆Linux后，系统会出现不同的提示符号，比如#，$等，之后用户就可以输入需要的命令，让linux系统执行一直到注销系统为止，在从登录到注销期间，输入的每个命令都会经过解释及执行，负责该用户和Linux系统对话的机制就是shell。

4、Shell脚本

Shell 脚本（shell script），是一种为shell编写的脚本程序。

其实作为命令语言互动式的解释和执行用户的输入命令只是shell功能的一个方面。
​		shell还能用来进行程序设计，它提供了定义变量和参数的手段以及丰富的程序控制结构。
​		类似于Dos系统中的批处理文件，称为shell script。

5、查看目前使用什么shell

```shell
env | more
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131644.png" alt="image-20200827092644188" style="zoom:80%;" />

## 1.1 快速入门

1、脚本格式要求

（1）脚本以`#!/bin/bash`开头。

（2）脚本需要执行权限。

2、案例

创建一个shell脚本，输出Hello world。

```ini
#文件名：myShell.sh(后缀名可以为sh或者其他)
#!/bin/bash
echo "hello world"
```

3、脚本执行方式

（1）方式1：输出脚本的绝对路径或者相对路径

+ （a）要赋予 myShell.sh 脚本的+x权限(可执行)

```ini
#推荐使用这种
chmod u+x myShell.sh
```

+ （b）执行脚本

```ini
./myShell.sh
```

（2）方式2：sh+脚本

```ini
#不用赋予脚本+x权限，直接执行即可。
sh myShell.sh
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131645.png" alt="image-20200724203529871" style="zoom:80%;" />

## 1.2 shell变量

1、介绍

（1）Linux Shell中的变量分为：系统变量和用户自定义变量。

（2）系统变量：$HOME、$PWD、$SHELL、$USER等等。
				比如：echo $HOME 等等。

（3）显示当前Shell中所有变量: set。

案例：输出系统变量。

```ini
#!/bin/bash
echo "PATH=$PATH"
echo "user=$USER"
```

输出结果：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131646.png" alt="image-20200724204310747" style="zoom:80%;" />

2、Shell变量的定义

（1）基本语法

```ini
#定义变量
变量=值

#撤销变量
unset 变量

#声明静态变量（撤销变量时不能unset命令）
readonly 变量
```

（2）案例

+ 定义变量A

```ini
A=100
echo "A=$A"

#撤销变量A
unset A
echo "A=$A"
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131647.png" alt="image-20200724205211638" style="zoom:80%;" />

+ 声明静态的变量。

```ini
readonly A=99
echo "A=$A"
```

+ 可把变量提升为全局环境变量，可提供其他shell程序使用。

3、细节说明

定义变量的规则：

（1）变量名称可以由字母、数字和下划线组成，但是不能以数字开头。

（2）等号两侧不能有空格。

（3）变量名称一般习惯为大写。

将命令的返回值赋给变量：

（1）A=`ls -la`  命令（注意：有反引号），运行里面的命令，并把结果返回给变量A。

（2） A=$(ls -la 命令) 等价于上面的反引号。

```ini
#方法1
RESULT=`ls -la /home`
echo $RESULT

#空行
echo ""

#方法2（推荐使用）
RESULT2=$(ls -la /home)
echo $RESULT2
```

控制台输出结果：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131648.png" alt="image-20200724210445090" style="zoom:80%;" />

注：多行注释语法

```ini
:<<!
代码
..
!
```

## 1.3 设置环境变量

1、基本语法

```ini
#1、将shell变量输出为环境变量
export 变量名=变量值
#2、让修改后的配置信息立即生效
source 配置文件
#3、查询环境变量的值
echo 变量名
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131649.png" alt="image-20200724211551034" style="zoom:80%;" />

2、案例

（1）在/etc/profile文件中定义TOMCAT_HOME环境变量

```ini
#1、进入配置文件
vim /etc/profile

#2、配置tomcat
TOMCAT_HOME=/opt/tomcat
export TOMCAT_HOME

#3、使文件生效
source /etc/profile
```

（2）查看环境变量TOMCAT_HOME的值

```ini
echo $TOMCAT_HOME
```

（3）在另外一个shell程序中使用TOMCAT_HOME

```ini
vim myShell.sh

#在该文件中输入下面内容
echo "TOMECAT_HOME=$TOMECAT_HOME"

#执行指令，可以获取环境变量的值。
./myShell.sh
```

## 1.4 位置参数变量

当我们执行一个shell脚本时，如果希望获取命令行的参数信息，就可以使用到位置参数变量。

比如下面的代码，这个就是一个执行shell的命令行，可以在myshell脚本中获取到参数信息。

```
./myshell.sh 100 200
```

1、基本语法

```ini
#n为数字，$0代表命令本身，$1-$9代表第一到第九个参数，十以上的参数需要用大括号包含，如${10}
$n	

#这个变量代表命令行中所有的参数，$*把所有的参数看成一个整体
$*	

#这个变量也代表命令行中所有的参数,不过$@把每个参数区分对待
$@	

#这个变量代表命令行中所有参数的个数
$#	
```

2、案例

（1）编写一个shell脚本`positionPara.sh`，在脚本中获取到命令行的各个参数信息。

```ini
#!/bin/bash

echo "$0 $1 $2"
echo "$*"
echo "$@" 
echo "参数个数=$#"
```

注意，要为该文件添加可执行权限。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131650.png" alt="image-20200724213516170" style="zoom:80%;" />

## 1.5 预定义变量

预定义变量就是shell设计者事先已经定义好的变量，可以直接在shell脚本中使用。

1、基本语法

```ini
#当前进程的进程号(PID)
$$		

#后台运行的最后一个进程的进程号(PID)
$!

#最后一次执行的命令的返回状态。如果这个变量的值为0，证明上一个命令正确执行；否则(具体是哪个数，由命令自己来决定)，则证明上一个命令执行不正确。
$?		
```

2、案例

在一个shell脚本中简单使用一下预定义变量 preVar.sh。

```ini
#!/bin/bash

echo "当前的进程号=$$"
#后台的方式运行 myShell.sh
./myShell.sh & 
echo "最后的进程号=$!"
echo "执行的值=$?"
```

![image-20200724214440257](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131651.png)

## 1.6 运算符

### 1、基本语法

```ini
#方式1：推荐使用中括号（中间无空格）
"$((运算式))"或"$[运算式]"

#方式2
#注意expr运算符间要有空格和反引号
expr m + n
expr m - n

#\*代表转移字符
expr \*，/，% 乘，除，取余
```

### 2、案例

（1）计算(2+3)*4的值

```ini
#1. $(运算符)
RESULT1=$(((2+3)*4))
echo "result1=$RESULT1"

#2. $[运算式]
RESULT2=$[(2+3)*4]
echo "result2=$RESULT2"

#3. expr
TEMP=`expr 2 + 3`
RESULT3=`expr $TEMP \* 4`
echo "result3=$RESULT3"
```

![image-20200724215511725](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131652.png)

（2）请求出命令行的两个参数[整数]的和。

```ini
#执行指令为：./demo.sh  1 2
SUM=$[$1+$2]
echo "sum=$SUM"
```

## 1.8 条件判断

### 1、常用的判断条件

（1）两个整数的比较

```ini
=		字符串比较
-lt		小于 little
-le		小于等于 little equal
-eq		等于 equal
-gt		大于 greater
-ge		大于等于 greater equal
-ne		不等于 not equal
```

（2）按照文件权限进行判断

```ini
-r		有读权限
-w		有写权限
-x		有执行权限
```

（3）按照文件类型进行判断

```ini
-f		文件存在并且是一个常规的文件 file
-e		文件存在 exit
-d		文件存在并且是一个目录 directory
```

### 2、案例

（1）"ok"是否等于"ok"

```ini
#!/bin/bash
if [ "ok"="ok" ]
then
        echo "equal"
fi
```

运行./judge.sh后输出`equal`。

（2）23是否大于等于22

```ini
if [ 23 -gt 22  ]
then
        echo "大于"
fi
```

运行./judge.sh后输出`大于`。

（3）判断/home/zookeeper/a.txt目录中的文件是否存在

```ini
if [ -e /home/zookeeper/a.txt  ]
then
        echo "存在"
fi
```

## 1.9 流程控制

### 1、if分支

1、基本语法

```ini
if [ condition ]
then
	程序
elif [ condition ]
then
	程序
fi
```

注意：中括号和condition之间必须有空格。

2、案例

如果输入的参数大于等于60，则输出"及格了"，如果小于60，则输出"不及格"。

```ini
#!/bin/bash
if [ $1 -ge 60 ]
then
        echo "及格了"
elif [ $1 -lt 60 ]
then
        echo "不及格"
fi
```

### 2、case分支

1、基本语法

```ini
case $变量名 in
"值1")
如果变量的值等于值1，则执行程序1
;;
"值2")
如果变量的值等于值2，则执行程序2
;;
*)
如果变量的值都不是以上的值，则执行此程序
;;
esac
```

2、案例

当命令行参数是1时，输出"周一"，是2时，就输出"周二"，其它情况输出"other"

```ini
#!/bin/bash
case $1 in
"1")
        echo "周一"
;;
"2")
        echo "周二"
;;
*)
        echo "其它"
;;
esac
```

### 3、for循环

1、基本语法

```ini
#语法1
for 变量 in 值1 值2 值3 ...
do
	程序
done

#语法2
for((初始值;循环控制条件;变量变化))
do
	程序
done
```

2、案例

（1）打印命令行输入的参数

```ini
#!/bin/bash 
for i in "$*"
do
        echo "the num is $i"
done
```

`for i in "$*"`含义是将$*中的值赋给 `i`。

输出结果：

```ini
#测试
./testFor.sh  1 2 3

#用$*输出的结果（当作整体输出）
the num is 1 2 3

#用$@输出的结果（推荐这样的方式）
the num is 1
the num is 2
the num is 3
```

 （2）从1加到100的值输出显示

```ini
#!/bin/bash
#定义一个变量
SUM=0
for((i=0;i<=100;++i))
do
#外层是一个运算符，然后将sum+i的值计算后，赋值给sum
        SUM=$[$SUM+$i]
done
echo "SUM=$SUM"
```

控制台输出

```ini
#执行
./testFor2.sh

#输出结果
SUM=5050
```

### 4、while循环

#### 1、基本语法

```ini
while[条件判断式]
do
	程序
done
```

#### 2、案例

从命令行输入一个数n，统计从1+..+n的值。

```ini
#!/bin/bash
SUM=0
i=0
while [ $i -le $1 ]
do
        SUM=$[$SUM+$i]
        #自加1
        i=$[$i+1]
done
echo "SUM=$SUM"
```

控制台输出：

```ini
#执行
./testWhile.sh 10

#输出结果
SUM=55
```

### 5、读取控制台输入

1、基本语法

```ini
read [选项] {参数}
    选项：
    -p		指定读取值时的提示符
    -t		指定读取值时等待的时间(秒)，如果没有在指定的时间内输入，就不再等待了
    参数
    	变量	指定读取值得变量名
```

2、案例

（1）读取控制台输入一个num值。

```ini
#!/bin/bash
# 有提示信息，并在提示信息后添加一个空格后添加参数NUM1
# 当用户执行该代码时，程序会阻塞在这里，等待用户输入一个值，并将其交给NUM1
read -p "请输入一个数num1=" NUM1
echo "你输入的值是num1=$NUM1"
```

控制台输出：

```ini
#执行
./testRead.sh 1

#输出结果
请输入一个数num1=1
你输入的值是num1=1
```

（2）读取控制台输入一个num值，在10秒内输入。

```ini
#!/bin/bash
read -t 10 -p "请输入一个数num1=" NUM1
echo "你输入的值是num1=$NUM1"
```

控制台在10秒内没有输入时，程序会取消阻塞后执行下一句。

## 1.10 系统函数

​		shell编程和其他编程语言一样，有系统函数，也可以自定义函数。

### 1、basename

返回完整路径最后`/`的部分，常用于获取文件名。

（1）基本语法

```ini
basename [pathname] [suffix]
```

​		在选项中，`suffix`表示后缀，如果`suffix`被指定了，`basename`会将`pathname`或`string`中的`suffix`去掉。

（2）案例

返回`/home/zookeeper/a.txt`的`a.txt`部分。

直接在控制台写入下面的命令：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131653.png" alt="image-20200803160122138"  />

### 2、dirname 

返回完整路径最后`/`的前面的部分，常用于返回路径部分。

（1）基本语法

```ini
#从给定的包含绝对路径的文件名中取出文件名(非目录的部分)，然后返回剩下的路径(目录的部分)
dirname 文件绝对路径
```

（2）案例

返回`/home/zookeeper/a.txt`的`/home/zookeeper`部分。

![image-20200803160413757](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831131654.png)

## 1.11 自定义函数

1、基本语法

```ini
function funname()
{
    Action;
    [return int;]
}
```

调用时，直接写函数名：funname [值]。

2、案例

计算输入两个参数的和，read方式输入。

```ini
#!/bin/bash
function getSum()
{
        SUM=$[$n1+$n2]
        echo "sum=$SUM"
}
# 输入计算的参数
read -p "请输入第一个参数n1=" n1
read -p "请输入第一个参数n2=" n2
# 函数的调用                            
getSum $n1 $n2
```

控制台输出：

```ini
#执行
./testFun.sh 

#根据提示输入参数后，程序会输出结果
请输入第一个参数n1=10
请输入第一个参数n2=20
sum=30
```

## 1.12 综合举例

1、需求

定时维护数据库，将脚本放到`/usr/sbin`（这个脚本比较高权限）。

（1）每天凌晨2:10分备份数据库atguiguDB到`/home/zookeeper/data/db`。
（2）备份开始和备份结束能够给出相应的提示信息。
（3）备份后的文件要求以备份时间为文件名，并打包成.tar.gz的形式，比如2018-03-12_230210.tar.gz（年-月-日_时分秒.tar.gz）。
（4）在备份的同时，检查是否有10天前备份的数据库文件，如果有就将其删除。
2、执行

（1）编写代码

```ini
#!/bin/bash

#定义备份数据库的路径
BACKUP=/home/zookeeper/data/db
#当前的时间作为文件名
DATETIME=${date +%Y-%m-%d_%H%M%S}

#可以输出变量进行调试
echo ${DATETIME}
echo "-------------begin-------------"
echo "-----------开始备份------------"
echo "-----------备份的路径是$BACKUP/$DATETIME.tar.gz------------"

#主机
HOST=localhost
#用户名
DB_USER=root
#密码
DB_PWD=root
#备份数据库名
DATABASE=atguiguDB

#创建备份的路径
#如果备份的路径文件夹存在，就使用，否则就创建。&& 表示不使用if语句来执行创建文件。
[ ! -d "$BACKUP/$DATETIME" ] && mkdir -p "$BACKUP/$DATETIME"
#执行mysql的备份数据库的指令,$DATETIME 只是一个临时文件
mysqldump -u${DB_USER} -p${DB_PWD} --host=${HOST} $DATABASE | gzip > $BACKUP/$DATETIME/$DATETIME.sql.gz
#打包备份文件，首先切换到 $BACKUP 路径下，然后对临时文件夹$DATETIME 打包
cd $BACKUP
tar -zcvf $DATETIME.tar.gz $DATETIME
#删除临时目录,也可以直接使用 rm -rf $DATETIME
rm -rf $BACKUP/$DATETIME

#删除十天前的备份文件
find $BACKUP -mtime +10 -name "*.tar.gz" -exec rm -rf {} \;
echo "----------备份结束-----------"
```

（2）定时执行

```ini
#执行定时任务
crontab -e

#假如下面的内容，程序就会每天凌晨2:10分备份到数据库。
10 2 * * * /usr/sbin/mysql_db_backup.sh
```


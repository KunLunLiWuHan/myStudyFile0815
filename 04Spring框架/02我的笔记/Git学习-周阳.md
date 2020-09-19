# 1 入门讲解

1、了解

（1）是什么

Git是目前世界上最先进的分布式版本控制系统。

（2）能干嘛

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134311.png" alt="image-20200813185832060" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134312.png" alt="image-20200813190123267" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134313.png" alt="image-20200813190138671" style="zoom:80%;" />

（3）在哪下

（4）怎么玩

请查看下面的Git实操章节。

# 2 Git实操

Git常用命令

```bash
mkdir：         XX (创建一个空目录 XX指目录名)
pwd：          显示当前目录的路径。
git init          把当前的目录变成可以管理的git仓库，生成隐藏.git文件。
touch           xx文件或者新建文件
git add XX       把xx文件添加到暂存区去。
git commit –m “XX”  提交文件 –m 后面的是注释。
git status        查看仓库状态
git diff  XX      查看XX文件修改了那些内容
git log          查看历史记录
git reset  --hard HEAD^
cat XX         查看XX文件内容
git reflog       查看历史记录的版本号id
git checkout -- XX  把XX文件在工作区的修改全部撤销。
git rm XX          删除XX文件
git remote add origin https://github.com/zzyybs/testgit 关联一个远程库
git push –u(第一次要用-u 以后不需要) origin master 把当前master分支推送到远程库
git clone https://github.com/arjrzhouyang/testgit  从远程库中克隆
git checkout –b dev  创建dev分支 并切换到dev分支上
git branch  查看当前所有的分支
git checkout master 切换回master分支
git merge dev    在当前的分支上合并dev分支
git branch –d dev 删除dev分支
git branch name  创建分支
git remote 查看远程库的信息
git remote –v 查看远程库的详细信息
git push origin master  Git会把master分支推送到远程库对应的远程分支上  
```

## 1、创建版本库

初始化一个新的git仓库，并且该仓库是git类型

```shell
34938@LAPTOP-5NUHM8TD MINGW64 /d/oa
$ git init
Initialized empty Git repository in D:/oa/.git/
```

在`D:/oa/`文件夹下产生一个.git/文件。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134314.png" alt="image-20200813192418475" style="zoom:67%;" />

## 2、新建文件并放入git仓库

```bash
# 新建一个文件 touch/vi
touch a.txt 

# 将新建的a.txt纳入git管理
git add a.txt

#查看文件在git仓库中的状态
git status

#完成了首次提交。假如还要新增文件内容，可以再次尝试提交。
git commit -m "提交修改的信息说明" a.txt
```

具体操作如下：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134315.png" alt="image-20200813220115242" style="zoom:80%;" />

提交文件没有添加注释会报错：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134316.png" alt="image-20200813193147840" style="zoom:67%;" />

## 3、git的日志和跟踪管理

```bash
#查看每次操作的日志情况。
git log a.txt(file)

#一行显示，查看关键信息（比较简明扼要） pretty：漂亮的
git log --pretty=oneline a.txt(file)

#查看内容不同
git diff a.txt(file)
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134317.png" alt="image-20200813220929588" style="zoom:80%;" />

## 4、版本的回退

```bash
#退一步，指针回退一步
git reset --hard HEAD^

#退多步，有多个箭号
git reset --hard HEAD^^^^^

#退n步
git reset --hard HEAD~n（数字步数）

#穿梭穿越
#1、获得头7位版本号
git reflog a.txt(文件)
#2、将原来回退的版本进行恢复
git reset --hard 7位版本号
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134318.png" alt="image-20200813222419458" style="zoom:80%;" />

## 5、git三区

1、工作区+版本库+暂存区

工作区(Working Directory)：就是你电脑本地硬盘目录。

版本库(Repository)：工作区有个隐藏目录.git，它就是Git的本地版本库

暂存区(stage)：一般存放在"git目录"下的index文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134319.png" alt="image-20200813222543043" style="zoom:80%;" />

​		Git为我们自动创建的第一个分支master，以及指向master的一个指针叫HEAD。

2、文件向Git版本库添加的执行过程

我们把文件往Git版本库里添加的时候，是分两步执行的：

第一步是用“git add”把文件纳入Git管理，实际是把本地文件修改添加到暂存区；

第二步是用“git commit”提交更改，实际上就是把暂存区的所有内容提交到当前分支 。

因为我们创建Git版本库时，Git自动为我们创建了唯一一个master分支，所以commit就是往master分支上提交更改。

可以简单理解为，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。一旦提交完后，如果你又没有对工作区做任何修改，那么工作区就是“干净”的。即：

```ini
nothing to commit (working directory clean)
```

三种文件的撤销操作：

```bash
#查看工作区和暂存区里面最新版本的区别
git diff HEAD -- filename

#撤销已新建过未add（文件一直为红色）
git restore a.txt

#撤销已add未commit
#1、第一步（将a.txt从暂存区中开始回退，此时a.txt的状态由绿色将会变成红色，即此时到了工作区）
git  reset HEAD a.txt(文件名) 
#2、第二步（将工作区中的a.txt进行回退）
git restore a.txt

#撤销已add已commit
git reset --hard HEAD^ a.txt
```

3、删除文件

```bash
git rm -f d.txt

#删完提交
git commit -m "delete file d.txt"
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134320.png" alt="image-20200813230418568" style="zoom:80%;" />

## 6、git分支

```bash
#查看git上的分支
git branch 

#作用是新建分支
git branch 分支名字(dev/test)

#作用是切换分支
git checkout 分支名

#将目的分支的内容合并进当前分支
git merge 目的分支

#删除分支
git branch -d 分支名

#新建（dev）+切换（将当前分支切换到dev）一步搞定
git checkout -b 分支名(dev)	
```

## 7、冲突

### 1、冲突-内容冲突

同一行分支合并后的冲突（同一行数据不同，将提交的同一行的错开，解决冲突）。

注意：不能删除数据，需要人工干预解决冲突。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134321.png" alt="image-20200813232846709" style="zoom: 80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134322.png" alt="image-20200813231815120" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134323.png" alt="image-20200813233043974" style="zoom:80%;" />

### 2、冲突2-git本地命令和github的交互冲突

​		我们将改好的代码推送（push）提交到Github服务器上后的内容冲突，首先，将Github服务器代码拉取（pull）到本地,然后进行人工干预收工合并后再推送（push）到Github服务器上。

1、将远程库文件拉取到本地进行修改

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134324.png" alt="image-20200814112330388" style="zoom:67%;" />

2、将修改好的文件进行提交到远程

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134325.png" alt="image-20200814112957122" style="zoom:80%;" />

### 3、冲突3-TortoiseGit分支合并冲突

​		此时是将提交到本地库（master,dev）的文件进行整合，没有上传到远端的Github服务器中。

1、master合并dev分支出错

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134326.png" alt="image-20200814200055502" style="zoom:67%;" />

2、在master中打开合并异常文件

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134327.png" alt="image-20200814200135074" style="zoom:67%;" />

3、解决冲突

（1）点击下面的按钮

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134328.png" alt="image-20200814200557983" style="zoom:67%;" />

（2）按照提示和业务需求，合并文件内容，解决冲突

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134329.png" alt="image-20200814200351232" style="zoom:67%;" />

介绍：

+ Theirs窗口为服务器上当前最新版本。

+ Mine窗口为本地修改后的版本。

+ Merged窗口为合并后的文件内容显示。

Mine窗口为本地修改后的版本   ，只看红色的，鼠标右键有四个选项：

```ini
#右边这行把左边的红色行给冲抵覆盖掉。
use this text bloc

#右边整个文件覆盖左边的，最不人道的做法。
use this whole file

#我的放在前面
use this text block from mine before theirs

#我的放在后面
use this text block from theirs before mine   
```

(3)解决完成后，将代码提交到本地库中

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134330.png" alt="image-20200814202251450" style="zoom: 67%;" />

# 3 Github

### 1、本地 —> github远程库

（1）情景

​		在本地创建了一个OA项目后，我又想在GitHub创建一个OA项目，并且让这两个仓库进行远程同步。

（2）实现步骤

（a）先在本地新建好一个git项目

​		将.git文件放到git项目里面，同步效果更好。

（b）到Github上新建一个同名的空项目

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134336.png" alt="image-20200814095736813" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134337.png" alt="image-20200814095919086" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134338.png" alt="image-20200814103004425" style="zoom:80%;" />

（c）本地和github上的仓库进行关联

```bash
#远程库的名字默认都是origin。
#https:表示创建git项目且自己要进行代码上传的一个地址
git remote add origin https://github.com/zzyybs/oa.git
```

（d）把本地库的内容推送到远程

```bash
#git push表示把当前分支master推送到远程
#commit代表提交到本地库
#origin：远程库的名字；master:远程库的分支。
git push -u origin master
```

​		由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134339.png" alt="image-20200814102301505" style="zoom:67%;" />

（e）把远程仓库中的内容拉取到本地

在远程仓库中，对a.txt文件进行修改，使用`git status`命令，未看出任何异样，我们需要使用下面的命令将修改拉取到本地。

```bash
 git pull origin master
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134340.png" alt="image-20200814102540160" style="zoom:80%;" />

### 2、github远程库 —> 本地

1、情景

从零开发，那么最好的方式是先创建远程库，然后从远程库克隆。

2、步骤

（1）登陆GitHub，创建一个新的仓库，名字叫OA2

（2）远程库OK，使用命令git clone克隆代码到本地库

```basH
$ git clone  https://github.com/KunLunLiWuHan/oa2.git
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134341.png" alt="image-20200814103946007" style="zoom:80%;" />

### 3、交互模型

1、 本地修改，同步给远程；远程修改，同步到本地。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134342.png" alt="image-20200814104122648" style="zoom:80%;" />

2、一般流程

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134343.png" alt="image-20200814104143286" style="zoom:80%;" />

### 4、Fork

​		GitHub 上有好多开源的好的项目，我们可以下载下来查看、借鉴别人的代码。但是如果我们修改了，由于不是对方的合作伙伴，我们无法将代码上传到别人的仓库，此时我们可以选择使用 fork 和 pullrequest 操作 。即：非合作伙伴如何共同开发项目 。

1、情景

​		用叉子把别人的东西（copy no cut）叉到你碗里。就是把别人的项目clone一份，但是owner变成自己，这样你就可以在遵守Open source license的前提下任意修改这个项目了。

​		相当于你在原项目的主分支上又建立了一个分支，你可以在该分支上任意修改，如果想将你的修改合并到原项目中时，可以`pull request`，这样原项目的作者就可以将你修改的东西合并到原项目的主分支上去，这样你就为开源项目贡献了代码，开源项目就会在大家共同的努力下不断壮大和完善。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134344.png" alt="image-20200814104614657" style="zoom:80%;" />

2、fork过程

（1）搜查对方的github地址

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134345.png" alt="image-20200814105831487" style="zoom:67%;" />

（2）fork一份对方的代码，进行更改

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134346.png" alt="image-20200814105731091" style="zoom:80%;" />

（3）更改代码进行提交

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134347.png" alt="image-20200814111439980" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134348.png" alt="image-20200814110712111" style="zoom:80%;" />

（4）对方接收到我的通知后，来进行判断代码是否进行合并。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134349.png" alt="image-20200814110831400" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134350.png" alt="image-20200814111107773" style="zoom: 50%;" />

### 5、解决git push时重复输入用户名密码

1、方法1

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134351.png" alt="image-20200814113512598" style="zoom:80%;" />

2、方法2

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134352.png" alt="image-20200814113530548" style="zoom:80%;" />

```bash
machine github.com
login 你的用户名(xiaolun)
password 你的密码
```

# 4 图形化工具-TortoiseGit

## 1、安装

1、选择64bits进行安装。安装过程同QQ，只需要选择一下安装的路径就可以了。

2、我们需要对安装的软件进行配置。

（1）安装完成后，右键点击，会有如下的界面

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134353.png" alt="image-20200814175740300" style="zoom:80%;" />

（2）进入setting进行配置

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134354.png" alt="image-20200814175912676" style="zoom:80%;" />

开始和本地库交互，需要设置账号和邮件：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134355.png" alt="image-20200814180307640" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134356.png" alt="image-20200814180535980" style="zoom:80%;" />

## 2、操作-与本地库交互

1、初始化Git仓库

​		右键鼠标，选择`Git Create repository here ...`,就可以创建一个Git仓库，会出现`.git`的隐藏文件。

2、add、commit等基础操作

（1）add操作

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134357.png" alt="image-20200814182325316" style="zoom:80%;" />

（2）commit操作

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134358.png" alt="image-20200814181511201" style="zoom:67%;" />

查看提交情况：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134359.png" alt="image-20200814182548917" style="zoom:80%;" />

3、版本回退和版本撤销

Revert与Reset区别：

Reset：是将当前的工作目录完全回滚重置到指定的版本号，一般选`hard`模式，会将当前`Head`的内容重置，不留存记录日志，尸骨无存彻底删干净

Revert：是撤销某次提交，这次撤销也会作为一次提交进行保存，不会像Reset那样日志会完全删除。

（1）Reset操作

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134400.png" alt="image-20200814183129985" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134401.png" alt="image-20200814183156124" style="zoom:80%;" />

（2）Revert操作

​		类似于快捷键`Ctrl + Z`的撤销操作，举例：

​		当我们对一个新建的文件新增记录，先执行一次Revert操作，之前新增记录会被撤销，但是日志会被保留，当执行再次Revert操作时，原来的撤销就再次撤销，新增记录会出现。

4、分支操作

（1）创建分支

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134402.png" alt="image-20200814185636766" style="zoom:67%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134403.png" alt="image-20200814185736490" style="zoom:67%;" />

（2）切换分支

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134404.png" alt="image-20200814185824657" style="zoom:80%;" />

（3）分支的合并

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134405.png" alt="image-20200814190453290" style="zoom:80%;" />

（4）分支的删除

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134406.png" alt="image-20200814190632025" style="zoom:80%;" />

## 3、操作-与远程库Github交互

1、情况1

本地新建一个git项目，远程同名新建空项目，同步上传到远程。

（1）点击push

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134407.png" alt="image-20200814205738168" style="zoom: 50%;" />

（2）将Github服务器上的地址复制下来后就行粘贴

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134408.png" alt="image-20200814205826957" style="zoom:67%;" />

（3）进行上传，可以看到下面的上传成功

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134409.png" alt="image-20200814205703640" style="zoom:67%;" />

此时，Github上面就有你上传的内容了。

2、情况2

远程库上已经有一个git项目，从远程Clone到本地。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134410.png" alt="image-20200814210504454" style="zoom:80%;" />

# 5、IDEA上Git操作

##   1、全局配置

1、配置Git核心程序

点击工具栏中的 settings→Version Control→Git

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134411.png" alt="image-20200814221053753" style="zoom:80%;" />

2、配置GitHub账户

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134412.png" alt="image-20200814221218788" style="zoom:80%;" />

## 2、创建本地库

1、新建一个Maven工程

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134413.png" alt="image-20200814221341978" style="zoom:80%;" />

2、创建本地库

假如是一个项目的话，可以选择当前模块目录进行Git管理。下图我是选的HelloGit这个目录（一个工程进行管理，后来改成上面的模块进行管理）。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134414.png" alt="image-20200814221627500" style="zoom:80%;" />

3、看到下面的界面说明本地库创建成功

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134415.png" alt="image-20200814221741390" style="zoom:80%;" />

4、是否将新文件添加到暂存区

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134416.png" alt="image-20200814221944640" style="zoom:80%;" />

5、设置忽略文件

暂且不知道怎么设置，先保留。

6、将文件添加到暂存区

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134417.png" alt="image-20200814224744184" style="zoom:67%;" />

此时文件颜色由红变绿色。

7、将文件添加到本地库

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134418.png" alt="image-20200814224829884" style="zoom:80%;" />

也可以采用下面的方式：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134419.png" alt="image-20200814225120785" style="zoom:80%;" />

此时，IDEA中文件颜色回归到正常颜色状态。

## 3、切换版本

1、更改内容进行提交

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134420.png" alt="image-20200814225521653" style="zoom:67%;" />

2、版本回退

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134421.png" alt="image-20200814225730665" style="zoom:80%;" />

选择`hard`属性完全删除痕迹

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134422.png" alt="image-20200814225751110" style="zoom:80%;" />

## 4、创建分支并合并

1、创建分支

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134423.png" alt="image-20200814231940899" style="zoom:67%;" />

2、为新分支命名

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134424.png" alt="image-20200814232002638" style="zoom:67%;" />

当我们点击上图的`Create`后，上面的代码会切换到新的分支上。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134425.png" alt="image-20200814232138462" style="zoom:67%;" />

3、在dev分支上对代码进行修复成功后，将分支切换到主分支

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134426.png" alt="image-20200814232411635" style="zoom:67%;" />

选择合并的分支，并添加注释：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134427.png" alt="image-20200814232444710" style="zoom:80%;" />

## 5、解决冲突

让主干和分支在同一个位置添加一行代码 。

（1）创建分支并在同一行编写不同的代码

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134428.png" alt="image-20200814234814772" style="zoom:67%;" />

（2）合并分支

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134429.png" alt="image-20200814234855203" style="zoom:67%;" />

（3）解决冲突

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134430.png" alt="image-20200814234027793" style="zoom:80%;" />

## 6、上传本地库到GitHub上

1、在GitHub上创建仓库，和自己项目名字同名不同名都可以。

2、将新建仓库的地址复制下来。

3、点击push

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134431.png" alt="image-20200814235559244" style="zoom:80%;" />

4、将复制的地址粘贴到URl

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134432.png" alt="image-20200814235639418" style="zoom:67%;" />

5、推送到远程仓库

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134433.png" alt="image-20200814235701192" style="zoom:67%;" />

此时，可以在GitHub上看到自己上传的代码，表示上传成功。

## 7、更新本地库

1、IDEA中本地库修改文件，添加到暂存库、添加到本地库，然后上传到Gitub中，会发现上传被拒绝 。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134434.png" alt="image-20200814235943202" style="zoom:80%;" />

2、解决方法

![image-20200815000837720](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134435.png)

## 8、克隆

1、点击CSV，进行克隆操作

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134436.png" alt="image-20200815001917585" style="zoom:67%;" />

2、输入GitHub中仓库的地址并指定项目的存放路径

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134437.png" alt="image-20200815001954212" style="zoom:80%;" />

3、为克隆的项目创建一个新工程

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134438.png" alt="image-20200815002031777" style="zoom:67%;" />

## 9、添加合作伙伴

​		在项目的协同开发过程中，如果 GitHub 上的仓库不是你创建的，你克隆下来的项目完成代码的编辑之后上传会失败。此时如果想要上传成功，必须让 GitHub 上仓库的拥有者（项目经理）添加你为合作伙伴。

1、让仓库拥有者在仓库上点击 settings（对应一个仓库）

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134439.png" alt="image-20200815004428669" style="zoom:67%;" />

2、点击Manage Access 

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134440.png" alt="image-20200815004458690" style="zoom:80%;" />

3、搜索合作伙伴，即搜索合作伙伴的 GitHub 账户

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134441.png" alt="image-20200815004525398" style="zoom:67%;" />

4、点击邀请

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134442.png" alt="image-20200815004552000" style="zoom:67%;" />

此时在你的界面处于合作伙伴确认的状态：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134443.png" alt="image-20200815004651961" style="zoom:67%;" />

5、仓库拥有者可以将链接发送给你让你确认，当然你的邮箱也会收到等待确认的邮件 。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134444.png" alt="image-20200815004748520" style="zoom:67%;" />

​	等合作伙伴接收邀请之后，就与仓库拥有者成为了合作伙伴，就可以在克隆项目之后向仓库上传项目了。
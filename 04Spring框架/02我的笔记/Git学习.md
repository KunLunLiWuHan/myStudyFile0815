# 1 版本介绍

版本控制（Revision Control）是一种在开发的过程中用于管理我们对文件、目录或工程等内容的修改历史，方便查看更改历史记录，备份以便恢复以前的版本的软件工程技术。

+ 实现跨区域多人协作开发。
+ 追踪和记载一个或多个文件的历史记录。
+ 阻止和保护你的源代码和文档。
+ 统计工作量。
+ 并行开发、提高开发效率。
+ 追踪记录整个软件的开发过程。
+ 减轻开发人员的负担、节省时间、同时降低人为错误。

简单的说，就是用于管理多人协作开发项目的技术。

没有进行版本控制或者版本本身缺乏正确的流程管理，在软件开发中会引入很多问题，如软件开发过程的安全性以及软件的整合等问题。

项目开发阶段

+ 项目解决方案
+ 项目解决方案改1
+ 项目解决方案完成版1
+ 项目解决方案最终版1
+ 项目解决方案绝对不修改版
+ 遗书

需要一个版本控制器，来解决这个问题。

## 1.1 版本控制分类

常见版本控制工具-主流版本控制器

+ Git
+ SVC(Subversion)
+ CVS(Concurrent Version System)
+ VSS(Microsoft Visual SourceSafe)
+ TFS(Team Foundation Server)

现在影响力最大的是Git和SVC。、

### 1.1.1 本地版本控制

记录文件每次的更新，可以对每个版本做一个快照，或是记录补丁文件，适合个人用。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134043.png" alt="image-20200414213044563" style="zoom:80%;" />

### 1.1.2 集中版本控制

所有的版本数据都存放到服务器上，协同开发者从服务器上同步更新或上传自己的修改。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134044.png" alt="image-20200414213213662" style="zoom:80%;" />

所有的版本都存在服务器上，用户的本地只有自己以前同步的数据，如果不联网的话，就看不到之前的历史版本，也无法切换版本验证问题，而且，所有数据都存放到单一的服务器上，有很大风险这个服务器会损坏，这样的话就会丢失所有保存的数据，当然，要定期更改备份。代表产品：SVN,CVS,VSS。

### 1.1.3 分布版本控制

所有版本信息仓库全部同步到本地的每个用户，这样就可以在本地查看所有版本历史，可以离线在本地提交，只需联网时push到相应的服务器或其他用户那里。由于每个用户那里保存了所有的版本数据，只要有一个用户的设备没有问题，就可以恢复到所有数据，但这增加了本地存储空间的占用，但每个人拥有全部的代码。代表产品：Git。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134045.png" alt="image-20200414214113949" style="zoom:80%;" />

## 1.2 Git和SVN区别

SVN是集中式版本控制系统，版本库是存放在中央服务器的，而工作的时候，用的是自己的电脑，所有首先要从中央服务器得到最新的版本，然后工作，完成工作后，需要把自己做完的活推送到中央服务器。集中式版本控制系统是必须联网才能工作，对网络和宽带要求较高。

Git是分布式版本控制系统，没有中央服务器，每个人的电脑就是一个完整的版本库，工作的时候不需要联网，因为版本都在自己电脑上，同时Git可以直接看到更新了那些代码和文件。协同的方法为：比如说自己在电脑上修改了文件A，其他人也在电脑上改了文件A，这时，他们两个之间只需把各自的修改推动给对方，就可以看到对方的修改了。Git是目前世界上最先进的分布式版本控制系统。

# 2 Git环境配置

1. **下载**

打开git官网，下载git对应操作系统的版本。

```http
https://git-scm.com/
```

使用镜像地址下载：

```http
https://cdn.npm.taobao.org/dist/git-for-windows/v2.26.0.windows.1/Git-2.26.0-64-bit.exe
```

2. **卸载**

直接清理环境变量（和git相关的），然后控制面板进行卸载。其中环境变量只是为了全局使用而已（任意地方使用）。

3. **安装步骤**

无脑下一步就好（其中一项选择Linux环境），安装完毕后就可以使用了。

4. **启动**

安装完成后，在开始菜单中会有Git项，菜单下有三个程序。或者任意文件夹下右键，也可以看到对应的程序。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134046.png" alt="image-20200414221750120" style="zoom:80%;" />

Git Bash：Unix与Linux风格的命令行，使用较多，推荐最多。

Git CMD：Windows风格的命令行。

Git GUI：图形界面的Git，不建议初学者使用，尽量先熟悉常用命令。

在Git Bash中，按住Ctrl+鼠标滚轮改变字体大小。

5. **Linux命令**

|  命令   |                             内容                             |
| :-----: | :----------------------------------------------------------: |
|   cd    |                           改变目录                           |
|  cd ..  |             回退到上一级目录，直接cd进入默认目录             |
|   pwd   |                    显示当前所有的目录路径                    |
| IS(II)  | 都是列出当前目录中的所有文件，只不过II(两个II)列出内容更为详细 |
|  touch  | 新建一个文件夹，touch index.js,就会在当前目录下新建一个index.js的文件 |
|   rm    | 删除一个文件，rm index.js,就会在当前目录下删除一个index.js的文件 |
|  mkdir  |                        新建一个文件夹                        |
|  rm-r   |             删除一个文件夹，rm-r src删除src目录              |
|   mv    | 移动文件，mv index.html src，index.html 是我们要移动的文件，src是目标文件夹。这样写，必须在同一文件夹 |
|  reset  |                     更新初始化终端/清屏                      |
|  cleat  |                             清屏                             |
| history |                         查看命令历史                         |
|  help   |                             帮助                             |
|  exit   |                             推出                             |
|    #    |                             注释                             |

平时一定多使用这些基础的命令。

## 2.1 Git配置

1. 设置用户名和邮箱（用户标识）

当你安装Git后首先要做的事情就是设置你用户名称和e-mail地址，这时非常重要的，因为，每次Git提交都会使用该信息。它会被永远的嵌入到你的提交中：

```java
git config --global user.name "xiaolun" #名称
git config --global user.email 3493826518@qq.com #邮箱
```

只需要做一次这个设置，如果你传递了 --global选项，因为Git将总是会使用该信息来处理你在系统中所做的一切操作。

如果你需要在一个特定的项目中使用不同的名称或者e-mail地址，你可以在该项目中运行命令而不使用--global选项。总之，--global为全局配置，不为某个项目的特定配置。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134047.png" alt="image-20200414225455568" style="zoom:80%;" />

+ 查看不同级别的配置文件

```java
#查看系统config
git config --system --list

#查看当前用户配置config
git config --global --list
```

2. Git相关配置文件

+ Git\etc\gitconfig   Git安装目录下的gitconfig  --system系统级。

![image-20200414231037609](https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134048.png)

+ C:\Users\Administrator\\.gitconfig(C:\Users\老鼠\\.gitconfig\) 只适用于当前登录用户的配置  --global全局。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134049.png" alt="image-20200414231251257" style="zoom:80%;" />

在这里，可以直接编辑配置文件，通过命令设置后会响应到这里。

# 3 Git基本理论

## 3.1 工作区域

Git本地有三个工作区域：工作目录（Work Directory）、暂存区（Stage/index）和资源库（Repository或Git Directory）。如果再加上远程的git仓库(Remote Directory)就可以分为四个工作区域。文件中四个工作区域之间的转换关系如下：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134050.png" alt="image-20200414231918875" style="zoom:80%;" />

+ Work Directory：平时存放项目代码的地方。
+ Stage/index：用于临时存放你的改动，事实上它是一个文件，保存即将提交的文件的列表信息。
+ Repository或Git Directory：是安全存放数据的位置，这里有你提交到所有版本的数据，其中HEAD指向最新放入仓库的版本。
+ Remote Directory：托管代码的服务器，可以简单的认为是你项目中的一台电脑用于远程数据交换。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134051.png" alt="image-20200414232636447" style="zoom:80%;" />

+ Directory：用于Git管理的一个目录，也就是一个仓库，包含我们的工作空间和Git的管理空间
+ WorkSpace:需要通过Git进行版本控制的目录和文件，这些目录何文件组成了工作空间。
+ .git:存放Git管理信息的目录，初始化仓库时自动创建。
+ Local Repo:本地仓库，一个存放在本地的版本库，HEAD只是当前开发的一个分支（branch）。
+ stash:隐藏，是一个工作状态保存栈，用于保存/恢复WorkSpace中的临时状态。

## 3.2 工作流程

1. 在工作目录里添加，修改文件(eg：UserMapper.xml)。
2. 将需要进行版本管理的文件放入暂存区域(git add.)。
3. 将暂存区域的文件提交到git仓库(git commit)。

因此，git管理的文件有三种状态：已修改（modified）、已暂存（staged）和已提交（committed）。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134052.png" alt="image-20200414233708972" style="zoom:80%;" />



# 4 Git项目创建与克隆

## 4.1 创建工作目录与常用指令

工作目录（WorkSpace）一般就是你希望Git帮助你管理的文件夹，可以是你创建的目录，也可以是一个空目录，建议不要有中文。

日常使用只要记住下面6个命令

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134053.png" alt="image-20200414234038217" style="zoom:80%;" />

### 4.1.1 创建本地仓库

创建本地仓库的方式一般有两种：一种是创建全新的仓库，一种是克隆远程仓库。

1. 创建全新的仓库，需要用Git管理的项目的根目录执行：

```javascript
#在当前目录创建一个Git代码库
$ git init
```

+ 执行时可以看到，仅仅在项目目录中多出一个.git目录，关于版本等的所有信息都在这个目录中。

2. 克隆远程仓库

将远程服务器上的仓库完全镜像一份到本地。

```javascript
#克隆一个项目和它的整个代码历史（版本信息）
$ git clone[url]
```

去gitee或者github上克隆一个测试。   

# 5 Git文件操作

## 5.1 文件的四种状态

+ Untracke：未跟踪，此文件在文件夹中，并没有添加到git库，不参与版本控制，通过git add状态变为staged。
+ Unmodefied：文件已经入库，未修改，即版本库中的文件快照内容与文件夹完全一致，这种类型的文件有两种去处，如果它被修改，变为Modefied，如果使用 git rm移除版本库，变成untracked文件。
+ Modified：文件已修改，仅仅是修改，并没有进行其他的操作，有两个去处，通过git add 可进入暂存staged态，使用git checkout 则丢弃修改过，返回unmodify状态，这个git ckeckout 即从库中取出文件，覆盖当前修改。
+ Staged：暂存状态，执行git commit，则将修改同步到库中，这时库中的文件和本地文件变为一致，文件为Unmodify，执行 git reset HEAD filename取消暂存，文件变为Modified。

1. 查看文件状态

```bash
#查看指定文件状态
git status [filename]

#查看所有文件状态
git status

#添加所有文件到暂存区
git add.

#提交暂存区的内容到本地仓库 -m；message
git commit -m "消息内容"
```

2. 忽略文件

有时候，我们不想把某些文件纳入到控制版本中，比如数据库文件，临时文件，设计文件等。

在主目录下建立`.gitgnore`文件，此文件有如下规则：

+ 忽略文件汇总的空行或者井号（#）,开始的行将会被忽略。
+ 可以使用Linux通配符。例如：星号（*）代表多个字符，问好（？）代表一个字符，方括号[abc]代表可选字符范围，大括号（string1 ,string2....）代表可选的字符串等。
+ 如果名称的最前面有一个感叹号（!），表示例外规则，将不被忽略。
+ 如果名称中最前面有一个路径分隔符（/），表示要忽略的文件在这个目录下，而子目录中的文件不忽略。若最后面有一个路径分隔符（/），表示要忽略的是该目录下该名称的子目录，而非文件。

```bash
#注释
*.txt #忽略所有，.txt结尾的文件
！lib.txt #但lib.txt除外
/tmp   #仅忽略项目根目录下的TODO文件，不包含其他目录temp(往上走)
build/  #忽略build/目录下的所有文件（往下走）
doc/*.txt #忽略doc/notes.txt，但不包括doc/server/arch.txt
```

# 6 IDEA集成Git操作

1. 新建项目，绑定git。
   + 将我们远程的git文件目录拷贝到项目中即可。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134103.png" alt="image-20200415093148661" style="zoom:80%;" />

​								图7.1 拷贝到Spring Boot文件中的git文件

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134104.png" alt="image-20200415093659460" style="zoom:80%;" />

​										图7.2 Spring Boot界面的图标变化

其中红色代表选中状态，右上角有Git图表显示。

2. 修改文件，使用IDEA操作git。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134105.png" alt="image-20200415094525375" style="zoom:80%;" />

​										图7.3 将文件提交到本地仓库的图形显示

在控制端使用命令方式

+ git add. 将文件添加到暂存区
+ git commit -m "修改信息"

3. 提交测试。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134106.png" alt="image-20200415100617120" style="zoom:80%;" />

然后在控制台使用命令`git push`上传到远端仓库

# 7 Git分支

​		分支在Git中相对较难，分支就是科幻世界中的平行宇宙，如果两个平行宇宙互不干扰，那对于现在的你也没啥影响。不过，在某个时间点，两个宇宙合并了，我们就需要处理一些问题了。

+ git分支中的常用命令

```bash
#列出所有本地分支
git branch

#列出所有远程分支
git branch -r

#新建一个分支，但依然停留在当前分支
git branch [branch-name]

#新建一个分支，并切换到该分支
git merge [brance]

#删除分支
git branch -d[brance-name]

#删除远程分支
git push origin --delete [brance]
git branch -dr[remote/branch]
```

如果一个文件在合并分支时都被修改了则会引起冲突;解决方法是我们修改冲突文件后重新提交。

master主分支非常稳定，用来发布新版本，一般情况下不允许在上面工作，工作一般情况下再新建一个`dev`后，比如上要发布，或者说`dev`分支代码稳定后可以合并到主分支`master`上来。

# 8 SourceTree

## 8.1 安装过程

1. 将下载软件解压后选择下面的文件：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134107.png" alt="image-20200620122038139" style="zoom:80%;" />

管理员运行到安装阶段就可以了，不要打开之后点击关闭。

2. 破解，新建accounts.json文件。

目录为：

```ini
C:\Users\34938\AppData\Local\Atlassian\SourceTree
```

文件内容为：

```json
[ 
  { 
    "$id": "1", 
    "$type": "SourceTree.Api.Host.Identity.Model.IdentityAccount, SourceTree.Api.Host.Identity", 
    "Authenticate": true, 
    "HostInstance": { 
      "$id": "2", 
      "$type": "SourceTree.Host.Atlassianaccount.AtlassianAccountInstance, SourceTree.Host.AtlassianAccount", 
      "Host": { 
        "$id": "3", 
        "$type": "SourceTree.Host.Atlassianaccount.AtlassianAccountHost, SourceTree.Host.AtlassianAccount", 
        "Id": "atlassian account" 
      }, 
      "BaseUrl": "https://id.atlassian.com/" 
    }, 
    "Credentials": { 
      "$id": "4", 
      "$type": "SourceTree.Model.BasicAuthCredentials, SourceTree.Api.Account", 
      "Username": "", 
      "Email": null 
    }, 
    "IsDefault": false 
  } 
]
```

3. 打开SourceTree软件，运行。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831134108.png" alt="image-20200620121317072" style="zoom:80%;" />

选择第四个选项，这个前提是基于你已经安装了Git之后。


















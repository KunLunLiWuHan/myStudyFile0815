# 1 介绍

## 1、概述

​		Nginx ("engine x") 是一个高性能的 HTTP 和反向代理服务器,特点是占有内存少，并发能力强， 事实上nginx的并发能力确实在同类型的网页服务器中表现较好， 中国大陆使用nginx网站用户有：百度、京东、新浪、网易、腾讯、淘宝等。

## 2、Nginx作为web服务器

​		Nginx 可以作为静态页面的 web 服务器，同时还支持 CGI 协议的动态语言，比如 perl、php等。 但是不支持 java。 Java 程序只能通过与 tomcat 配合完成。 Nginx 专为性能优化而开发，性能是其最重要的考量，实现上非常注重效率 ，能经受高负载的考验,有报告表明能支持高达 50000 个并发连接数。

## 3、正向代理 

​		正向代理：如果把局域网外的 Internet 想象成一个巨大的资源库，则局域网中的客户端要访问 Internet，则需要通过代理服务器来访问，这种代理服务就称为正向代理。 

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831164722.png" alt="image-20200818090104312" style="zoom:67%;" />

​		在客户端（浏览器）配置代理服务器，通过代理服务器进行互联网的访问。

​		反向代理和正向代理的区别，参考网址：https://www.cnblogs.com/taostaryu/p/10547132.html。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831164723.png" alt="image-20200818090716585" style="zoom:50%;" />

​		正向代理中，proxy和client同属一个LAN，对server透明；反向代理中，proxy和server同属一个LAN，对client透明。

​		实际上proxy在两种代理中做的事都是代为收发请求和响应，不过从结构上来看正好左右互换了下，所以把后出现的那种代理方式叫成了反向代理

## 4、反向代理

​		反向代理，其实客户端对代理是无感知的，因为客户端不需要任何配置就可以访问，我们只需要将请求发送到反向代理服务器， 由反向代理服务器去选择目标服务器获取数据后， 再返回给客户端， 此时反向代理服务器和目标服务器对外就是一个服务器， 暴露的是代理服务器地址，隐藏了真实服务器 IP 地址。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831164724.png" alt="image-20200818090814544" style="zoom:67%;" />

## 5、负载均衡

​		客户端发送多个请求到服务器，服务器处理请求，有一些可能要与数据库进行交互，服务器处理完毕后，再将结果返回给客户端。 
  这种架构模式对于早期的系统相对单一， 并发请求相对较少的情况下是比较适合的， 成本也低。但是随着信息数量的不断增长，访问量和数据量的飞速增长，以及系统业务的复杂度增加，这种架构会造成服务器相应客户端的请求日益缓慢，并发量特别大的时候，还容易造成服务器直接崩溃。 
​		首先想到的可能是升级服务器的配置， 比如提高 CPU 执行频率， 加大内存等提高机器的物理性能来解决此问题， 但是我们知道摩尔定律的日益失效， 硬件的性能提升已经不能满足日益提升的需求了。最明显的一个例子，天猫双十一当天，某个热销商品的瞬时访问量是极其庞大的，那么类似上面的系统架构，将机器都增加到现有的顶级物理配置，都是不能够满足需求的。 
​		上面的分析我们去掉了增加服务器物理配置来解决问题的办法， 也就是说纵向解决问题的办法行不通了， 那么横向增加服务器的数量呢？这时候集群的概念产生了， 单个服务器解决不了，我们增加服务器的数量，然后将请求分发到各个服务器上，将原先请求集中到单个服务器上的情况改为将请求分发到多个服务器上， 将负载分发到不同的服务器， 也就是我们所说的负载均衡。 

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831164725.png" alt="image-20200818091010956" style="zoom:67%;" />

## 6、动静分离

​		为了加快网站的解析速度， 可以把动态页面和静态页面由不同的服务器来解析， 加快解析速度。降低原来单个服务器的压力。 

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831164726.png" alt="image-20200818091207916" style="zoom:67%;" />

# 2 Nginx部署

## 1、安装

1、进入官网，下载压缩包

```http
http://nginx.org/en/download.html
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831164727.png" alt="image-20200819144650234" style="zoom:50%;" />

​		nginx是c语言开发，笔记中使用Centos6.9作为开发环境。

​	为了安装nginx，我们还需要安装 prce-xx.tar.gz、openssl-xx.tar.gz和zlib-xx.tar.gz（prce-xx.tar.gz为了重写rewrite，zlib-xx.tar.gz为了gzip压缩）。

2、安装pcre

​		PCRE(Perl Compatatible Regular Expressions)是一个Perl库，包含perl兼容的正则表达式库。Nginx的http模块使用pcre来解析正则表达式。我们需要在Linux中安装一个pcre库。

推荐以root身份进行安装，不然会有类似权限等的许多问题。

（1）执行命令

```ini
yum install -y pcre pcre-devel
```

注：pcre-devl是使用pcre开发的一个二次开发库。nginx也需要此库。如果报错基本上是因为，pcre没有安装上，实在不行，下载安装包后进行下面的安装。如果安装后，请忽略下面的安装

下载后安装步骤：进入pcre安装包目录，编译安装  

```shell
#解压缩后的第一步
./configure

#第二步
make && make install
```

遇到下面的报错时：

```shell
configure: error: You need a C++ compiler for C++ support
```

​		说明我们系统中缺少c++环境。因为安装nginx需要先将官网下载的源码进行编译，编译时依赖gcc环境。

我们在联网的情况下，root身份执行下面的命令：

```shell
yum install -y gcc gcc-c++
```

3、安装openssl 、zlib 

执行下面的命令进行安装：

```shell
#统一安装（推荐）
yum -y install make zlib zlib-devel gcc-c++ libtool  openssl openssl-devel 

#分步安装
yum install -y zlib zlib-devel
yum install -y openssl openssl-devel
```

​		OpnSSL是一个强大的安全套接字层密码库，囊括主要的密码算法，常用的密钥和整数封装和管理功能以及SSL协议，并提供丰富的应用程序供测试或者其他目的使用。

​		ngin不仅支持http协议，还支持https(即在ssl协议上传递的http),所以需要在Linux中安装OpenSSL库。

4、安装nginx

```shell
#进入解压缩目录
./configure

#编译安装
make && make install 
```

5、防火墙配置

​		在nginx中（conf/nginx.conf），服务器的监听端口默认为80,因此，我们要在防火墙中开放该端口

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831164728.png" alt="image-20200818104513643" style="zoom: 67%;" />

（1）查看防火墙中开放的端口号

```shell
#方法1
service iptables status

#方法2
/etc/init.d/iptables status

-A OUTPUT -o eth0 -d 224.0.0.18 -j ACCEPT 
-A OUTPUT -o eth0 -s 224.0.0.18 -j ACCEPT
-A INPUT -i eth0 -d 224.0.0.18 -j ACCEPT
-A INPUT -i eth0 -s 224.0.0.18 -j ACCEPT
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831164729.png" alt="image-20200818102925929" style="zoom: 67%;" />

（2）设置开放端口号80并保存

```shell
[root@centos xx]# /sbin/iptables -I INPUT -p tcp --dport 80 -j ACCEPT

[root@centos xx]# /etc/rc.d/init.d/iptables save

[root@centos xx]# /etc/init.d/iptables restart
```

此时在`/etc/sysconfig/iptables`中会有如下的配置：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831164730.png" alt="image-20200818103132561" style="zoom:67%;" />

在云服务器上不用配置了，在本地虚拟中还是配置一下吧。

## 2、常用命令

1、启动命令

​		在`/usr/local/nginx/sbin` 目录下执行，下面几个目录也都是在这个目录下执行的。

```shell
./sbin/nginx 

#按照指定目录启动
./sbin/nginx -c conf/nginx1.conf
```

通过下面的指令可以查看启动状态：

```shell
 ps -ef| grep nginx
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831164731.png" alt="image-20200818103629352" style="zoom:50%;" />

此外，我们也可以通过下面的网址来查看nginx是否启动（默认端口80）：

```http
http://39.96.161.64/
proxy_pass http://39.107.140.8:8080;
```

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831164732.png" alt="image-20200818104133887" style="zoom: 50%;" />

2、停止nginx

```shell
 ./nginx  -s  stop
```

3、重新加载nginx

针对nginx中conf/nginx.conf文件改动后，不使用重启操作，使用重新加载操作来完成系统的重启。

```shell
./sbin/nginx -s reload 

#修改配置文件
[root@iZ2zehzqu74u7m655akuk5Z nginx]# vim conf/nginx1.conf
#按照指定目录重新加载Nginx
./sbin/nginx -c conf/nginx1.conf  -s reload 
```

出现下面的报错：

```ini
nginx: [error] open() ＂/usr/local/nginx/logs/nginx.pid＂ failed
```

我们使用nginx -c的参数指定nginx.conf文件的位置：

```shell
/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
```

4、查看nginx版本号

```shell
./nginx -v
```

## 3、配置文件

​		nginx 安装目录下，其默认的配置文件都放在这个目录的 conf 目录下，而主配置文件 nginx.conf 也在其中，后续对 nginx 的使用基本上都是对此配置文件进行相应的修改 。

nginx.conf  配置文件分为三部分： 

1、全局块

​		从配置文件开始到 `events` 块之间的内容，主要会设置一些影响 nginx 服务器整体运行的配置指令，主要包括配置运行 Nginx 服务器的用户（组）、允许生成的 worker process 数，进程 PID 存放路径、日志存放路径和类型以及配置文件的引入等。 

比如：

```ini
worker_processes  1;
```

​		这是 Nginx 服务器并发处理服务的关键配置，worker_processes 值越大，可以支持的并发处理量也越多，但是会受到硬件、软件等设备的制约。

2、events块

​		events 块涉及的指令主要影响 Nginx 服务器与用户的网络连接，常用的设置包括是否开启对多 work process 下的网络连接进行序列化，是否允许同时接收多个网络连接，选取哪种事件驱动模型来处理连接请求，每个 word 
process 可以同时支持的最大连接数等。 

```ini
events {
    worker_connections  1024;
}
```

​		上述例子就表示每个 `work process` 支持的最大连接数为 1024.。这部分的配置对 Nginx 的性能影响较大，在实际中应该灵活配置。 

3、http块

​		这是 Nginx 服务器配置中最频繁的部分， 代理、 缓存和日志定义等绝大多数功能和第三方模块的配置都在这里。 需要注意的是：http 块也可以包括 http 全局块、server 块。 

（1）http全局块

​		http 全局块配置的指令包括文件引入、MIME-TYPE 定义、日志自定义、连接超时时间、单链接请求数上限等。 

（2）server 块 

​		这块和虚拟主机有密切关系，虚拟主机从用户角度看，和一台独立的硬件主机是完全一样的，该技术的产生是为了节省互联网服务器硬件成本。 
​		每个 http 块可以包括多个 server 块，而每个 server 块就相当于一个虚拟主机。  而每个 server 块也分为全局 server 块，以及可以同时包含多个 locaton块。

（a）全局 server 块

最常见的配置是本虚拟机主机的监听配置和本虚拟主机的名称或 IP 配置。

（b）location块

​		一个 server 块可以配置多个 location 块。 

​		这块的主要作用是基于 Nginx  服务器接收到的请求字符串（比如：server_name/uri-string），对虚拟主机名称（也可以是 IP 别名）之外的字符串（例如 前面的 /uri-string）进行匹配，对特定的请求进行处理。地址定向、数据缓存和应答控制等功能，还有许多第三方模块的配置也在这里进行。 

# 3 反向代理

## 1、测试1

1、实现效果

​		使用 nginx 反向代理，访问39.96.164.64网址（省略80端口）直接跳转到 39.107.140.8:8080（tomcat）。

1、更改配置

```ini
server {
        listen       80;
        server_name  localhost;
        
        location / {
            proxy_pass http://39.107.140.8:8080;
        }
}
```

localhost对应的是本机，即39.96.164.64服务器。

2、重新加载和启动Nginx。

```shell
./sbin/nginx -c conf/nginx1.conf  -s reload 
```

3、测试

​		输入网址：39.96.164.64，浏览器显示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831164733.png" alt="image-20200819143426412" style="zoom:67%;" />

​		不过浏览器的网址是39.96.164.64，不是39.107.140.8，即实现了反向代理。

注意：

（1）当我们在本地进行如下的域名转换时：

```http
39.96.161.64 c.test.com
```

​		使用`c.test.com`代替ip号访问Nginx时，会被拒绝。测试时也发现：加上端口号80时也不行，但是加上其他的端口号时，比如8080,却可以正常访问Nginx。解释：本机内的域名转化不适合外网内的域名转换。

## 2、测试2





# 4 虚拟主机

## 1、测试1

1、需求

更换Nginx的默认端口，使用http://39.96.161.64:10000/来访问Nginx。

2、将云服务器的10000端口的安全组打开，不需要开启防火墙。

3、按照下图更改配置

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831164734.png" alt="image-20200819100103985" style="zoom:67%;" />

4、重新加载和启动Nginx。

```shell
./sbin/nginx -c conf/nginx1.conf  -s reload 
```

5、测试

输入网址：http://39.96.161.64:10000/，浏览器输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831164735.png" alt="image-20200819102358872" style="zoom: 50%;" />

此时，原先的80端口失效，访问不到。（禁用缓存，效果更佳）。

## 2、测试2

1、需求

​		将两个域名（a.test.com,b.test.com）指向同一台机器。用户访问不同的域名指向不同的网页内容。服务器使用阿里云服务器39.96.161.64代替。

2、新建html文件

```shell
#路径/usr/local/nginx/html/a(b)
a.html
b.html
```

3、更改域名映射

```http
39.96.161.64 a.test.com
39.96.161.64 b.test.com
```

在测试中，这样的域名转换可以被39.96.161.64被识别。

4、更改配置文件，进行访问配置

```ini
 server {
        listen       80;
        server_name  a.test.com;

        location / {
            root   html/a;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
    
     server {
        listen       80;
        server_name  b.test.com;

        location / {
            root   html/b;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
```

5、重新加载nginx

```shell
./sbin/nginx -c conf/nginx1.conf  -s reload 
```

6、测试

（1）输入网址：http://a.test.com/a.html，显示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831164736.png" alt="image-20200819101527053" style="zoom:67%;" />

（2）输入网址：http://b.test.com/b.html，显示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831164737.png" alt="image-20200819101558085" style="zoom:67%;" />

（3）输入网址：http://a.test.com/a,找不到，只有上面两个网址可以找到相应的网址。

注意：为了观察更好的效果，我们应该清除浏览器缓存。

# 5 location模块的匹配

参考博客：

https://www.cnblogs.com/duhuo/p/8323812.html

1、语法规则

location指令用于匹配URL，语法如下：

```
location[ = | ~ | ~* |^~ ]/uri/{...}
```

（1）= ：用于不含正则表达式的 uri 前，要求请求字符串与 uri 严格匹配，如果匹配成功，就停止继续向下搜索并立即处理该请求。 

（2）~：用于表示 uri 包含正则表达式，并且区分大小写。 

（3）~*：用于表示 uri 包含正则表达式，并且不区分大小写。 

（4）^~：用于不含正则表达式的 uri 前，要求 Nginx 服务器找到标识 uri 和请求字符串匹配度最高的 location 后，立即使用此 location 处理请求，而不再使用 location 块中的正则 uri 和请求字符串做匹配。 

即：URL以某个常规字符串开头，理解为匹配URL路径即可。

（5）/：通用匹配，任何请求都会匹配到。如下所示：

```ini
 location / {
      proxy_pass http://39.96.161.64:8080; 
 }
```

  注意：如果 uri 包含正则表达式，则必须要有 ~ 或者 ~* 标识。

2、精准匹配

（1）案例

到html_tx目录下访问demo.html。

（2）修改配置文件nginx.conf

```ini
 server {
        listen       80;
        server_name  localhost;
        
		#这行代码是通用匹配
        location / {
                root html;
                index index.html index.htm;
        }
		#精准匹配
        location = /demo.html {
                root html_tx;
        }
 }
```

（3）在根目录（/usr/local/nginx）下创建文件夹`html_tx`，然后在文件夹中添加`demo.html`文件。

（4）测试

输入网址：http://39.96.161.64/demo.html，浏览器显示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831164738.png" alt="image-20200819153953266" style="zoom:80%;" />

3、路径匹配

（1）案例

匹配通过static访问的路径。

（2）修改配置文件

```ini
server {
        listen       80;
        server_name  localhost;

        location / {
                root html;
                index index.html index.htm;
        }
		
		#html_js/static_js匹配路径
        location ^~ /static_js/ {
                root html_js;
        }
        location ^~ /static_css/ {
                root html_css;
        }
}
```

（3）创建目录/usr/local/nginx/html_js/static_js、/usr/local/nginx/html_css/static_css，并在该目录中添加demo.html文件。

（4）测试

输入网址：http://39.96.161.64/static_css/demo.html，浏览器输出：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831164739.png" alt="image-20200819160023138" style="zoom:50%;" />

由于/static_css符合当前的location，所以当前location被匹配，访问的是

/usr/local/nginx/html_css/static_css。

4、正则匹配

（1）案例

匹配jpg，png，gif结尾的URL。

（2）修改配置文件

```
location ~\.(jpg|png|gif){
	root img;
}
```

# 6 负载均衡

## 1、测试

1、需求

​		浏览器地址栏输入地址http://39.96.161.64/edu/a.html ，负载均衡效果，将请求平均分配到39.96.161.64和39.107.140.8中去。 

2、在两台 `tomcat` 里面 `webapps`目录中，创建名称是`edu`文件夹，在`edu`文件夹中创建页面 `a.html` 。其中39.96.161.64/39.107.140.8中a.html中分别为为8080/8081，重新启动tomcat。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831164740.png" alt="image-20200819164547645" style="zoom:80%;" />

3、修改配置文件nginx.conf

```ini
http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;
    keepalive_timeout  65;
    
    #上游负载均衡配置
    upstream myserver{
        server 39.96.161.64:8080;
        server 39.107.140.8:8080;
    }

    server {
        listen       80;
        server_name  localhost;
		
		#路径匹配
        location / {
            proxy_pass http://myserver;
        }


        error_page   500 502 503 504  /50x.html;
                location = /50x.html {
                        root   html;
                 }
         }
}
```

4、测试

输入网址：http://39.96.161.64/edu/a.html，浏览器显示：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831164741.png" alt="image-20200819164947339" style="zoom:67%;" />

## 2、服务分配策略

1、轮询（默认） 

​		每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器 down 掉，能自动剔除。 

2、weight 

​		weight 代表权,重默认为 1,权重越高被分配的客户端越多。指定轮询几率，weight 和访问比率成正比，用于后端服务器性能不均的情况。 例如：

```ini
upstream myserver{
    server 39.96.161.64:8080 weight=10;
    server 39.107.140.8:8080 weight=10;
}
```

3、 ip_hash

​		每个请求按访问 ip 的 hash 结果分配， 这样每个访客固定访问一个后端服务器， 可以解决 session 的问题。 例如：

```ini
upstream myserver{
	ip_hash;
    server 39.96.161.64:8080;
    server 39.107.140.8:8080;
}
```

4、fair（第三方） 

按后端服务器的响应时间来分配请求，响应时间短的优先分配。

```ini
upstream myserver{
    server 39.96.161.64:8080;
    server 39.107.140.8:8080;
    fair; 
}
```

# 7 动静分离

## 1、介绍

​		Nginx 动静分离简单来说就是把动态跟静态请求分开， 不能理解成只是单纯的把动态页面和静态页面物理分离。**严格意义上说应该是动态请求跟静态请求分开，可以理解成使用 Nginx 处理静态页面（指向一个静态资源服务器去处理），Tomcat 处理动态页面。**

​		动静分离从目前实现角度来讲大致分为两种：一种是纯粹把静态文件独立成单独的域名， 放在独立的服务器上， 也是目前主流推崇的方案； 另外一种方法就是动态跟静态文件混合在一起发布，通过 nginx 来分开。 通过 location 指定不同的后缀名实现不同的请求转发。

​		通过 expires 参数设置，可以使浏览器缓存过期时间，减少与服务器之前的请求和流量。具体 Expires 定义：是给一个资源设定一个过期时间， 也就是说无需去服务端验证， 直接通过浏览器自身确认是否过期即可，所以不会产生额外的流量。此种方法非常适合不经常变动的资源。（如果经常更新的文件，不建议使用 Expires 来缓存）。

​		假设我这里设置 3d，表示在这 3 天之内访问这个 URL，发送一个请求，比对服务器该文件最后更新时间没有变化，则不会从服务器抓取，返回状态码 304，如果有修改，则直接从服务器重新下载，返回状态码 200。 

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831164742.png" alt="image-20200819170056208" style="zoom:67%;" />

## 2、测试

参考第5章的location模块的匹配。

# 8 高可用配置

## 1、介绍

​		Nginx作为一个负载均衡器，所有请求都到了Nginx，如果Nginx发生宕机，后端的Web服务将无法提供服务。

​		因此，我们需要建立一个备份机。主服务器和备份机上都运行高可用（High Avaliablity）的监控程序，通过传送注入`I am alive`这样的信息来监控对方的运行状况。当备份机不能在一定的时间内接收这样的信息是，它就接管主服务器的服务IP并继续提供负载均衡服务；当备份机又从主服务器接收

`I am alive`这样的信息时，它就释放服务IP地址，这样主服务器就可以再次提供负载均衡服务。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200831164743.png" alt="image-20200819204821648" style="zoom:80%;" />

本测试的高可用环境如下：

（1）两台Nginx，一主一备：192.168.10.101和192.168.10.102。

（2）一台tomcat服务器：192.168.10.101。

## 2、Keepalived+nginx实现主从模式

1、介绍

`keepalived`是集群管理中保证集群高可用的一个服务组件，可以用来防止单点故障。

`keepalived`的作用是检测web服务器的状态，如果有一台web服务器死机，或工作出现故障，`keepalived`将检测到，并将单点故障的web服务器从系统中剔除，当web服务器工作正常后`keepalived`自动将web服务器加入到服务器群中，这些工作全部自动完成，不需要人工干预，需要人工做的就是修复故障的web服务器。

2、工作原理

`keepalived`是以虚拟路由冗余协议（VRRP：Virtual Router Redundancy Protocol）协议为实现基础的。

该协议可以认为是实现路由器高可用的协议，即将N台提供相同功能的路由去组成一个路由器组，这个组里面有一个master和多个backup，master上面有一个对外提供服务vip(该路由器所在局域网内其他机器默认路由为该vip)，master会发组播，当backup收不到VRRP包时就认为master宕机了，这时就需要根据VRRP的优先级来选举一个backup当master.

`keepalived`主要有三个模块，分别是core、check和VRRP。core模块为`keepalived`的核心，主要负责进程的启动、维护以及全局配置文件的加载和解析，check模块负责健康检查，包括常见的各种检查方式。VRRP是用来实现VRRP协议的。

### 3、keepalived的安装

1、以root身份在/opt/目录下执行命令

```shell
yum install keepalived –y
```

2、安装完成之后，有配置文件：/etc/keepalived/keepalived.conf

3、修改该配置文件，设置主从模式

```bash
global_defs { 
   notification_email { 
   		#指定keepalived在发生切换时需要发送email的对象，一行一个
   		xxx@xx.com
   } 
   notification_email_from xxx@xx.com #指定发件人 
   #smtp_server 192.168.17.129 #指定smtp服务器地址
   #smtp_connect_timeout 30  #指定smtp连接的超时时间
   router_id LVS_DEVEL   #运行keepalived机器的一个标识
} 
  
vrrp_script chk_http_port { 
   script "/usr/local/src/nginx_check.sh"  #监控脚本
   interval 2      #（检测脚本执行的间隔） 2秒
   weight 2        #权重
} 
  
vrrp_instance VI_1 { 
    state MASTER   #标识状态为MASTER备份服务器上将其改为 BACKUP   
    interface eth0  #设置实例绑定的网卡 
    virtual_router_id 51  #同一实例下，主、备机的 virtual_router_id 必须相同
    priority 100     #主、备机取不同的优先级，主机值较大，备份机值较小
    advert_int 1  #MASTER和BACKUP负载均衡器之间同步检查的时间间隔,单位/s
    authentication {  #设置认证方式
        auth_type PASS 
        auth_pass 1111 #主从密码一致
    } 
    
    virtual_ipaddress {  #设置vip,可以设置多个换行即可
        192.168.0.106 // VRRP H 虚拟地址 
    } 
} 
```

4、添加检测脚本

`keepalived`是通过检查`keepalived`进程是否存在来判断服务器是否宕机，如果`keepalived`进程在，但是`Nginx`进程不在了，那么`keepalived`不会做主备切换，所以我们需要写一个脚本来监控`Nginx`进程是否存在，如果不存在就将其杀掉。

```bash
#在/usr/local/src目录下添加,名字：nginx_check.sh
#!/bin/bash 
#查看是否有nginx进程，把值赋给变量
# -C by command name
#wc -l统计行数
A=`ps -C nginx –no-header |wc -l` 
if [ $A -eq 0 ];then  #如果没有进程值为0
    /usr/local/nginx/sbin/nginx 
    sleep 2 
    if [ `ps -C nginx --no-header |wc -l` -eq 0 ];then 
        #killall keepalived 
        service keepalived stop #则结束keepalived进程
    fi 
fi 
```

5、修改keepalived.conf文件

添加脚本定义检测。

```ini
vrrp_script chk_http_port { 
   script "/usr/local/src/nginx_check.sh"  #监控脚本
   interval 2      #（检测脚本执行的间隔） 2秒
   weight 2        #权重
} 
```





```shell
#!/bin/bash
A=`ps -C nginx--no-header |wc -l`  
if [ $A -eq 0];then               
       service keepalived stop   
fi
```


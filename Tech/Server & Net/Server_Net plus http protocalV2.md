### 服务器搭建

#### Apache

搭建环境为:Ubuntu 18.04.6 LTS

1.安装apache组件

```c
$ sudo apt-get install apache2
```

2.安装进程结束后重启apache2服务

```c
$ sudo /etc/init.d/apache2 restart
```

3.检测apache是否成功安装

打开浏览器，地址栏输入127.0.0.1（有些版本可能是127.0.1.1）或http://localhost ;若成功打开apache默认页面则安装成功。

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151210267.png)

4.有关文件目录

#在该服务器中可以访问的地址所对应的文件目录为/var/www；

#有关apache的配置文件（.conf）文件目录为/etc/apache2；（如上图所示）

5.一些其他必要组件的安装

在ubuntu（aliyun镜像源）下载的apache资源包只包含apache服务本身。

下载组件：SSH/Mysql-server/PHP

*该版本Ubuntu自带percona-xtradb-cluster-server-5.7，需要卸载才能安装mysql。需要注意的是，默认安装方式mysql与cluster-server互相冲突，需要其他方式安装（即先添加仓库后爬取文件）。

操作方式见链接：https://blog.csdn.net/weixin_39681724/article/details/113470791；

验证是否安装完成：

```c
service mysql status / systemctl status mysql
```

若命令行出现详细信息，且提示服务已开始运行则安装成功。

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151210268.png)

可以使用该命令检查端口和是否正在监听：

```c
netstat -tap | grep mysql
```

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151210269.png)附：Ubuntu apt卸载方式的介绍与区别：https://blog.csdn.net/u012843189/article/details/81720854

安装PHP：

```c
sudo apt install php7.2-cli
```

验证安装完成：

```
php -v
```

若要下载PHP5，由于ubuntu18.04版本过高无法get下载，需要使用和mysql类似的下载方式。

https://blog.csdn.net/tdbtx_j/article/details/79464535

6.如何上传一个简单的php文件？

首先新建一个文件，后缀为a.php；在其中输入

```php
<?php phpinfo(); ?>
```

将其移动到/var/www/html（即apache2根目录中）

接下来给php安装必要的链接apache的组件

```c
sudo apt-get install libapache2-mod-php
```

php的扩展组件数量较多，下面是大概率用到的组件集合：

```c
apt-get install -y php7.2-bcmath php7.2-bz2 php7.2-dba php7.2-enchant php7.2-fpm php7.2-imap php7.2-interbase php7.2-intl php7.2-mbstring php7.2-phpdbg php7.2-soap php7.2-sybase php7.2-xsl php7.2-zip php7.2-xmlrpc php7.2-xml php7.2-tidy php7.2-sqlite3 php7.2-snmp php7.2-recode php7.2-readline php7.2-pspell php7.2-pgsql php7.2-opcache php7.2-odbc php7.2-mysql
```

这个时候仍然无法打开php页面，需要在apache2.conf中加两段代码：

```c
AddHandler php-script .php .html

AddType text/html .php .html
```

再次重启apache服务，成功。

7.SSH的基本原理

通过监听客户端请求，交换公钥，加解密的方式完成加密登录。

基本原理：https://www.cnblogs.com/ftl1012/p/ssh.html

8.下载phpmyadmin

```c
sudo apt-get install phpmyadmin
```

进行环境配置：

详见https://www.cnblogs.com/xpwi/p/9821371.html

教程中需要注意的一点是，若安装版本大于教程中的版本需要在php文件夹内找版本最高的文件夹（例如8.1）只有最高版本文件夹内才会含有php.ini文件。

途中出现未知报错，疑似原因为PHP7.4之后的版本不再能够使用花括号来访问数组或者字符串的偏移.需要将{}修改成[]。目前该问题暂无解决方案。

##### 关于.conf配置文件与.ini

apache2开始删除了httpd.conf，改为apache2.conf；.conf文件在apache2服务启动时连带启动，读取该文件内的信息。在文件夹中还有其他许多.conf文件，它们在apache2.conf文件中以#include形式被调用后被apache2读取。

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151210270.png)

详细参数解读：https://blog.csdn.net/heyun51/article/details/83391196

PHP.ini的作用是浏览器php语言的配置文件，负责初始化并且控制php行为。

详细参数解读：https://www.cnblogs.com/zllo/p/11726414.html

#### Docker

一些新的命令和快捷键：

vim命令行在命令模式下按x可清除光标所在位置的字符并对齐。

uname -a：查看当前linux系统内核版本

ctrl+shift+'+'：放大shell窗口字体

ctrl+'-'：减小shell窗口字体

netstat -antlp：查看当前被占用的端口

#需要注意的是，如果apache2服务开机自启动，默认会占用docker需要的80：80端口，需要kill pid.

docker ps：查看运行中的container

docker inspect ：查看某一个运行中的

systemctl enable (service_name)：开机自启动

*systemctl对应systemd服务，为新的init.d初始化服务，相比于system提高了多任务并发载荷。

常见命令：

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151210271.png)

docker exec -it  (id) /bin/bash 从宿主机进入到已运行的镜像服务器

详细命令见：https://blog.csdn.net/weixin_46318141/article/details/121168812?spm=1001.2014.3001.5506

### NET前置

#### ~~有关子网掩码，网段~~

仍在尝试理解

网段？：一般指一个计算机网络中使用同一物理层设备（传输介质，中继器，集线器等）能够直接通讯的那一部分。例如，从192.168.0.1到192.168.255.255这之间就是一个网段。

A,B,C类地址的网段区别：

一般常用的C类IP地址，子网掩码都是`255.255.255.0` ,就是说前3段表示网段.。

如`192.168.0.X` 最后一位表示主机号，IP地址前3段一模一样就是一个网段 ，如 192.168.0.1和192.168.0.5是一个网段，最后一位从1到254都可以 都是一个网段 。192.168.2.1和192.168.2.X也是一个网段，但192.168.0.X和192.168.2.X就不是一个网段。

A类地址[子网](https://so.csdn.net/so/search?q=子网&spm=1001.2101.3001.7020)掩码是255.0.0.0 ，如 10.0.0.5和10.5.2.1是一个网段 ，只要第一段一样就是一个网段了 。

B类地址子网掩码是255.255.0.0， 就是说前2段一样就是一个网段的。

子网掩码：用来划分**子网**的网段和**遮掩**部分IP地址。换个说法就是：它是用来划分IP地址中哪一部分是网络号哪一部分是机器号。

将子网掩码（例如255.255.255.0）转换为二进制后，掩码有多少个1，ip地址的前多少位就是网络位。

#### IP/网段/子网掩码

#网段如何确定：网段由IP地址和子网掩码共同确定；子网掩码的位数取决于单个网段内部的机器数量。

#子网掩码：用来指明一个[IP地址](https://baike.baidu.com/item/IP地址?fromModule=lemma_inlink)的哪些位标识的是[主机](https://baike.baidu.com/item/主机/455151?fromModule=lemma_inlink)所在的子网，以及哪些位标识的是主机的位掩码。打个比方，如果子网掩码为255.255.255.0，则该子网掩码的网段下的IP地址内只能存储254台机器（比如192.168.0.1-254），255为二进制的11111111，子网掩码为1的位数为网络位，为0的为机器位。

#三类IP地址：A类，B类，C类，其容纳的主机数量不同，默认子网掩码的位数当然也不同。

https://blog.csdn.net/sz76211822/article/details/83307119

#如何解决IP地址不够用的问题：公网内最多可以容纳2^32台机器，为了解决IP地址短缺，而每个公网IP下又可以组成一个局域网，局域网内又可以有最多2^32台主机（虽然一般情况下到不了这个值），这样做就解决了IP地址短缺的问题。

#内网IP局域网，网线都是连接在同一个交换机上，因此IP地址是由交换机或者路由器进行分配，而且每一个IP也是有所不同，但此类IP地址只可以在局域网内访问，无法直接访问internet。如果要访问internet，则需要通过NAT或者PROXY等技术转换，把内网ip和端口映射为公网的ip和端口，方可访问internet。

#内网专用的IP地址：

10.0.0.0~10.255.255.255

172.16.0.0~172.31.255.255

192.168.0.0~192.168.255.255

其它一些内网专用IP：https://blog.csdn.net/licheric/article/details/109630533

#### HTTP协议

##### 概述及连接过程

超文本传输协议（英文：HyperText Transfer Protocol，缩写：HTTP）是一种用于分布式、协作式和超媒体信息系统的应用层协议。HTTP是万维网的数据通信的基础。

HTTP是一个客户端终端（用户）和服务器端（网站）请求和应答的标准（TCP）。通过使用网页浏览器、网络爬虫或者其它的工具，客户端发起一个HTTP请求到服务器上指定端口（默认端口为80）。我们称这个客户端为用户代理程序（user agent）。应答的服务器上存储着一些资源，比如HTML文件和图像。我们称这个应答服务器为源服务器（origin server）。在用户代理和源服务器中间可能存在多个“中间层”，比如代理服务器、网关或者隧道（tunnel）

通常，由HTTP客户端发起一个请求，创建一个到服务器指定端口（默认是80端口）的TCP连接。HTTP服务器则在那个端口监听客户端的请求。一旦收到请求，服务器会向客户端返回一个状态，比如"HTTP/1.1 200 OK"，以及返回的内容，如请求的文件、错误消息、或者其它信息。

##### 请求报文

HTTP协议采用了请求/响应模型。客户端向服务器发送一个请求报文，请求报文包含请求的方法、URL、协议版本、请求头部和请求数据。服务器以一个状态行作为响应，响应的内容包括协议的版本、成功或者错误代码、服务器信息、响应头部和响应数据。

实际过程：

1.客户端连接到Web服务器
一个HTTP客户端，通常是浏览器，与Web服务器的HTTP端口（默认为80）建立一个TCP套接字连接。

2.发送HTTP请求
通过TCP套接字，客户端向Web服务器发送一个文本的请求报文，一个请求报文由请求行、请求头部、空行和请求数据4部分组成。

3.服务器接受请求并返回HTTP响应
Web服务器解析请求，定位请求资源。服务器将资源复本写到TCP套接字，由客户端读取。一个响应由状态行、响应头部、空行和响应数据4部分组成。

4.释放连接TCP连接
若connection 模式为close，则服务器主动关闭TCP连接，客户端被动关闭连接，释放TCP连接;若connection 模式为keepalive，则该连接会保持一段时间，在该时间内可以继续接收请求;

5.客户端浏览器解析HTML内容

客户端浏览器首先解析状态行，查看表明请求是否成功的状态代码。然后解析每一个响应头，响应头告知以下为若干字节的HTML文档和文档的字符集。客户端浏览器读取响应数据HTML，根据HTML的语法对其进行格式化，并在浏览器窗口中显示。

*TCP套接字：

在TCP/IP网络中，区分不同的应用程序进程间的网络通信和连接时主要有以下3个参数：通信的目的地址、使用的传输协议（TCP或UDP）和使用的端口号（此处说明一下，Socket不仅在TCP有，在UDP同样有）。通过将这3个参数结合起来，与一个Socket进行绑定，应用层就可以与传输层一起通过套接字接口区分来自不同应用程序进程或网络连接的通信，实现数据传输的并发服务。为了区别不同的应用程序进程和连接，许多计算机操作系统为应用程序与TCP/IP交互提供了称为套接字（Socket）的接口。

参考文档：https://blog.csdn.net/u011784495/article/details/72622379

如果在浏览器里输入一个地址，浏览器会先向DNS服务器发送请求，请求解析URL所对应的域名和IP地址。当IP地址解析完成后会根据IP，加上开放的某个端口（很多时候都是80）和服务器建立TCP连接。浏览器向服务器发送读取文件的请求报文，作为TCP三次握手的最后一次发送数据。浏览器响应并发送网页给浏览器，最终释放TCP连接，浏览器显示内容。

HTTP是一种无状态协议。这个名词的意思就是不对请求和响应之间的对应状态做长时间保存（除非模式改变，比如变为keepalive）所以在burpsuite的请求和相应报文中都有一行'Connection:close'

而很明显的是，现在需要登陆账户的网站，如果保存账户密码，在之后就不需要重复登陆了。这一功能依靠原始的HTTP1.1是无法实现的。事实上，cookies保存的就是这一“登陆状态”（这个后面再看吧）

回到现在可以接触到的实际应用部分。

这是一张Burpsuite抓包的请求报文：

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151210273.png)

请求报文包含三个部分

1.请求方法

第一行：第一部分GET说明请求方式；第二部分斜杠内的内容表明请求的是网站根目录下的某个页面；第三部分表示协议版本为HTTP1.1

2.请求头

从HOST开始一直到空白行为请求头。

一些参数的解释：

User-agent：表示浏览器的名称和版本。当然，使用python等编写的爬虫也会被识别出来。

Referer：表明当前这个请求是从哪个url过来的。这个一般也可以用来做反爬虫技术。如果不是从指定页面过来的，那么就不做相关的响应。

Cookie：http协议是无状态的。也就是同一个人发送了两次请求，服务器没有能力知道这两个请求是否来自同一个人。因此这时候就用cookie来做标识。

Connection:close表示要求服务器响应后自动停止连接，keepalive相反。

Accept：被允许的文件格式

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151210274.png)

16.application/x-www-form-urlencoded ： < form encType=””>中默认的encType，form表单数据被编码为key/value格式发送到服务器（表单默认的提交数据的格式）

*常见的请求方法：

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151210275.png)

*对于现阶段遇到比较多的GET/POST，二者方法类似，但POST相比GET多了具体的请求内容。

##### 响应报文

这是刚才的请求报文对应的响应报文：

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151210276.png)

第九行向下的就是返回的具体html文件了。

上面的是HTTP响应头。

1.一些响应头参数的解释

Allow：服务器支持的请求方式

Content-length：返回的报文长度

Server：服务器类型

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151210277.webp)

##### HTTP状态码

*五大类：

1xx：信息提示，表示请求已被成功接收，继续处理。
2xx：请求被成功提交。
3xx：客户端被重定向到其他资源。
4xx：客户端错误状态码，格式错误或者不存在资源。
5xx：描述服务器内部错误。

常见状态码：

200：客户端请求成功，是最常见的状态。
302：重定向。
404：请求资源不存在，是最常见的状态。
400：客户端请求有语法错误，不能被服务器所理解。
401：请求未经授权。
403：服务器收到请求，但是拒绝提供服务。

500：服务器内部错误，是最常见的状态。
503：服务器当前不能处理客户端的请求。

429：一段时间内请求次数过多，服务器无法处理。

#### Cookie

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151210278.png)

Cookies的基本定义已经明晰了，下面写一些cookie有关的性质和操作。

##### 性质

Name:伴随每个cookie的生命周期内都不可改变。

Value：值。可能采用unicode字符编码或base64等二进制编码等。

Domain：在指定的domain区域内cookie都有效。Domain的默认值是创建cookie生成的网页所在服务器的主机名。

命名规则：

1. 必须是1-9、a-z、A-Z、. 、- （注意是-不是_）这几个字符组成
2. 必须是数字或字母开头
3. 必须是数字或字母结尾

Path：指定cookie的有效路径，包括一个网址下的所有子路径（一般），需要使用/结尾。

Expires/Max-age

Expires和Max-age均为Cookie的有效期，Expires是该Cookie被删除时的时间戳，格式为GMT,若设置为以前的时间，则该Cookie立刻被删除，并且该时间戳是服务器时间，不是本地时间！若不设置则默认页面关闭时删除该Cookie。

Max-age也是Cookie的有效期，但它的单位为秒，即多少秒之后失效，若Max-age设置为0，则立刻失效，设置为负数，则在页面关闭时失效。Max-age默认为 -1。
注意：如果max-age为0，则表示删除该cookie。cookie机制没有提供删除cookie的方法，因此通过设置该cookie即时失效实现删除。

Size：此Cookie的大小。在所有浏览器中，任何cookie大小超过限制都被忽略，且永远不会被设置。各个浏览器对Cookie的最大值和最大数目有不同的限制。

SameSite
SameSite用来限制第三方 Cookie，从而减少安全风险。它有3个属性，分别是：

Strict
Scrict最为严格，完全禁止第三方Cookie，跨站点时，任何情况下都不会发送Cookie
Lax
Lax规则稍稍放宽，大多数情况也是不发送第三方 Cookie，但是导航到目标网址的 Get 请求除外。
None
网站可以选择显式关闭SameSite属性，将其设为None。不过，前提是必须同时设置Secure属性（Cookie 只能通过 HTTPS 协议发送），否则无效。

##### 传输过程

1.浏览器客户端从服务器应答包中提取所有cookie并分析组成。如果主机允许设置这些cookie则先保存在本地

2.浏览器客户端判断本地cookie属性和url值判断可以发送给服务器的cookie并加入到HTTP请求头中。



https://blog.csdn.net/m0_51545690/article/details/123359959?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166446041716782390524610%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166446041716782390524610&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-2-123359959-null-null.142^v51^pc_rank_34_queryrelevant25,201^v3^control_2&utm_term=cookie&spm=1018.2226.3001.4187

#### 网络基本结构

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151210279.png)

##### OSI七层协议

1.应用层：提供应用服务，比如http/ftp服务器。

2.表示层：通过对不同客户发送的数据进行转换解决不同系统的兼容问题。

3.会话层：建立与应用程序的会话连接

4.运输层：提供端口/传输协议（比如下面要提到的TCP）

5.网络层：路由器/交换机所在的层级，实现数据交互。

6.数据链路层：将即将发送的信息封装成帧并传递给物理层。

参考文档：https://blog.csdn.net/weixin_46215617/article/details/118839570?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166451673316800184182652%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166451673316800184182652&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-118839570-null-null.142^v51^pc_rank_34_queryrelevant25,201^v3^control_2&utm_term=%E6%95%B0%E6%8D%AE%E9%93%BE%E8%B7%AF%E5%B1%82&spm=1018.2226.3001.4187

7.物理层：通过光纤/卫星等方式在物理意义上透明传输bit flow.

##### 五层协议

1.应用层：通过应用交互完成特定的网络行为：例如HTTP/FTP/DNS/POP3/Telnet等

2.传输层：提供端口/传输协议

3.网络层：为不同碰撞域的主机提供通信服务，IP协议位于这一层。

**IP协议**
与IP配合使用的协议还有其他三个协议，
地址解析协议ARP
网际控制报文协议ICMP
网际组管理协议IGMP

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151210280.png)

4.数据链路层：即将发送的信息封装成帧并传递给物理层。

5.物理层：通过光纤/卫星等方式在物理意义上透明传输bit flow.

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151210281.png)

*.广播域和碰撞域的区别：https://blog.csdn.net/xyisv/article/details/90084869

#### TCP协议

为了通过IP数据报实现可靠性传输，就必须要考虑到在传输过程中，数据被破坏、丢包、重复以及数据包到达时的顺序混乱的问题，如果解决不了这些问题，那么无法保证传输的可靠性了，TCP通过检验和，序列号、确认应答、重发控制、连接管理以及窗口控制等机制实现了可靠性传输。TCP协议通过“源IP,源端口，目的IP,目的端口，协议”来定位一个通信，每个被定义的信道是唯一的。

##### 端口号：

- 0~1023：知名端口号，其中HTTP、FTP、SSH等这些广泛使用的应用层协议，端口号都固定在前1024个端口号之中。
- 1024~65535：操作系统动态分配的端口号，客户端程序的端口号，会由操作系统在这个范围内进行分配。
- 一些常见端口：SSH-22/FTP-21/Telnet-23/HTTP-80/HTTPS-443

一个进程可以绑定多个端口号，但一个端口号只能同时被一个进程占用。

##### 协议格式

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151210282.png)

首先需要说明的是4位报头长度，它表示该TCP头部有多少个32位bit位（即有多少个4字节），因此TCP头部的最大长度应该是(2^4-1) * 4 = 15 * 4 = 60个字节，又由示意图可以知道报头占据了20个字节，因此选项字段最多可以有40个字节。（TCP报头为四位二进制，所以为2的四次方减去1（没有0000））选项字段和TCP报头共同组成了TCP的头部，在读取数据时应该先读取20字节，取出4位报头长，如果4位报头长度的值为5，那么说明TCP首部长度只有4*5 = 20字节，刚刚读取的就是所有的头部内容，下面接着的就是数据内容；如果报头长度的值大于5，则后面还要读取一部分选项字段。
![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151210283.png)

因为TCP是安全的传输协议，因此它采用了确认应答机制，这种机制的可靠性体现在：只要收到了对应的应答，就可以认为，之前的数据对方已经收到了！后面的超时重发就要用到ACK。

32位序号：因为TCP是可靠传输，所以发送的信息顺序也要保证接收方接受的顺序一致，但是如果有多条数据，在发送过程中因为路由选择不同，一定是会有先发出的后到达，后发出的先到达的现象。所以就需要给数据进行编号，这样接收方根据编号再将数据进行排列，这样就可以保证数据报文的顺序了，32位序号应该有发送方在发送数据报之前填写完毕。

32位确认序号：我们已经知道了TCP是确认应答机制，所以如果发送方发送了三份报文，分别编号5,6,7。接收方也会返回三条ACK确认信息，我们怎么知道这三条ACK分别确认的是哪条报文呢？这里就要用到确认序号，但是这里有一个重要的点：ACK所携带的确认序号是报文序号 + 1,原因是确认序号告知的是发送方，前面的报文我已经收到了，下一份报文应该从确认序号开始发送！如果在接收方发送ACK过程中，7,8两个ACK丢失，发送方只收到了9号ACK，发送方还会去发送6,7两份报文吗？不会！9号ACK代表这前面所有的报文接收方都收到了，发送方只需要继续从9号报文开始发送就行了。
*.需要注意的一点是TCP是全双工通信协议。在建立TCP连接后既可以发送也可以接收。所以每一条消息必须要有一对对应的出现序号和接收序号。

*.六个标志位

URG：紧急指针是否有效
ACK：确认号是否有效
PSH：提示接收端应用程序立刻从TCP缓冲区将数据读走
RST：对方要求重新建立连接；我们将携带RST标识的报文称为复位报文段
SYN：请求建立连接；将携带SYN标识的报文称为同步报文段
FIN：通知对方，本端即将关闭，将携带了FIN标识的报文称为结束报文段

参数详解：https://blog.csdn.net/weixin_45523353/article/details/123708866?spm=1001.2014.3001.5501

##### TCP特殊机制——超时重发和握手

超时重发：分两种情况。

当发送方发送报文失败时，因为接收方没有接收到报文所以就不会返回ACK；此时发送方就会重新发送报文。

当发送成功但是接收方无法返回ACK报文时，发送方就会不断重复发送行为。此时接收方根据TCP报文头中的序列号就可以快速锁定重复报文并直接删除重复的数据包。TCP要求不论在何种网络环境下都提供高性能的通信，并且无论网络拥堵情况发生何种变化，都要保持这一特性。因此TCP在每次发包的时候都会计算往返时间（RTT，Round Trip Time）及其偏差（RTT波动的值、方差。也叫抖动）。将往返时间和偏差相加，超时重传的时间间隔就是比这个总和再稍微大一点的值。
握手/挥手机制：

![yo](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151210284.png)

用人话解释一下：（握手是连接挥手是停止连接）

在第一次握手之前服务器需要先打开监听接口（通过图虫的socket/bind/listen函数）来接收请求（被动打开）

第一次握手：客户端通过connect函数主动打开端口（主动打开）；之后客户发送一个SYN字符请求服务器与其建立连接。此时服务器还没有收到SYN字符。

第二次挥手：服务器确认客户的SYN，同时向客户端发出一个SYN和一个ACK，说明客户发送的SYN是有效的。

第三次握手：客户收到SYN和ACK。连接建立，客户服务器进入established状态，可以发送消息了。

第一次挥手：某个应用进程调用close（这里是服务端和客户端都可以），主动关闭的一段发送一个FIN字节，意思是告诉另外一边数据传输已经结束。

第二次挥手：另一端接收到FIN，被动关闭。

第三次握手：一段时间后，接收到FIN的一段调用close关闭它的套接口，这会导致它也发一个FIN。

第四次挥手：接收到FIN的原发送端确认之后关闭连接，整个连接过程结束。

参考文档：https://blog.csdn.net/weixin_45523353/article/details/124305056

#### MAC地址

MAC地址也叫物理地址、硬件地址，由网络设备制造商生产时烧录在网卡(Network lnterface Card)的[EPROM](https://baike.baidu.com/item/EPROM/1690813?fromModule=lemma_inlink)(一种闪存芯片，通常可以通过程序擦写)。[IP地址](https://baike.baidu.com/item/IP地址/150859?fromModule=lemma_inlink)与MAC地址在计算机里都是以[二进制](https://baike.baidu.com/item/二进制/361457?fromModule=lemma_inlink)表示的，IP地址是32位的，而MAC地址则是48位的。与IP地址不同，每一个网络设备都具有不可更改的唯一MAC地址。MAC地址用于在数据链路层中唯一表示一个网卡（注意，不是主机，因为一台主机可以拥有多个网卡，也就拥有了多个MAC地址）。

交换机取代了集线器的最重要的原因就在于，交换机可以向其所在碰撞域内的两台主机间点对点发送报文，而集线器只能将某一台主机发送的报文广播给碰撞与内的每台主机，由接受主机判断是否需要此报文。交换机能做到这一点的原因就是它可以识别连接在网络上的每个节点的MAC地址，并将它们存放起来。再发送中转信息时就可以通过MAC地址缓存向对应的节点发送消息。

(to be continued,,,)

#### IPv6

IPv6使用十六进制表示法，分隔成 8 个 16 位段，每 16 位段的值在 0000 ~ FFFF 的十六进制数之间，每个 16 位段之间用 “ : ” 分开.

特殊规则：

每组十六进制数中开头的 0 可以省略。这里需要注意，开头的 0 才能省略，末尾的 0 是不能省略的，因为这样会引起歧义，无法确定省略的 0 是在数字前还是数字后。

IPv6 只能用斜线法表示网段地址，即在 IPv6 地址后面加上一个斜线 “ / ” ，后面加上一个十进制的数字，来表示前面多少位是网络位。

#### NAT和桥接

在VMware虚拟机中网络选取NAT模式时相当于将物理机变成局域网网关，每个虚拟机都在局域网之内，对外不可见。

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151210285.png)

NAT详解：https://blog.csdn.net/yingshuanglu2564/article/details/111830114

桥接模式相当于将物理机的网络信息克隆一份到虚拟机，并对IP地址等信息做相应修改，使其与物理机达到实际上的并列关系。

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151210286.png)

#### Hping3常见指令

常用参数：

-p 端口号

-S 发送TCP的SYN包

-A 发送TCP的ACK包

-a 伪造源IP

--flood 尽可能快的发送，慎用（手动@喵师傅）

打个比方：

```
hping3 -c 100000 -d 120 -S -w 64 -p 80 --flood --rand-source 192.168.3.13
```

-c n：发送数据包总数

-d n：每个数据包大小

-S：只以SYN方式发送数据包

-w n：TCP窗口大小

-p n：目的端口

--rand source：伪装成随机IP地址

（SYN包在TCP握手机制里已经提到，为同步报文段，只是为了请求建立连接，而不带有任何可解析的信息）

一些其他的hping3使用方法：

https://blog.csdn.net/hexf9632/article/details/103698141

https://www.cnblogs.com/Higgerw/p/16469371.html

#### ARP协议

详解：https://zhuanlan.zhihu.com/p/28771785

简单来说就是通过广播IP地址，让正确的IP地址返回其MAC地址使二者间产生通信链路。在这一过程中，发送者会将接受者的MAC地址放入自己的ARP缓存表中，这样下一次发送报文就不需要再广播了。

之所以需要IP地址和MAC地址的对应关系，是因为数据在通过网络结构从上往下封装的时候需要MAC接受者的MAC地址才能成功封包。

ARP缓存表：（每次机器重启后会将该表重置）

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151210287.png)

*大部分广播包的特征：二层封装时目的MAC是全f（ffff.ffff.ffff）或三层封装时目的IP是全1（255.255.255.255）。

*.免费ARP：主机向局域网内广播自己的IP的行为被称为免费ARP。

可以通过此行为确定另一个主机是否设置了相同的IP地址；

同样的，当某台主机的网卡更新后（即MAC地址改变），其它主机当返回ARP缓存表中的MAC时会发现MAC地址不匹配，此时就需要使用ARP请求中的IP地址向发送者再发起一次ARP请求，通过这次请求返回的MAC地址覆写之前的缓存；

免费ARP也可以用于ARP欺骗。主机可以构造虚假的免费ARP应答，将ARP的源MAC地址设为错误的MAC地址，并把这个虚假的免费ARP应答发送到网络中，那么所有接收到这个免费ARP应答的主机都会更新本地ARP表项中相应IP地址对应的MAC地址。更新成功后，这些主机的数据报文就会被转发到错误的MAC地址，从而实现了ARP欺骗的攻击。

*.代理ARP

当ARP请求跨越了当前的局域网范围时，就需要代理ARP。由于两台主机处于不同的局域网内，此时IP地址不再起作用，这时需要路由器使用自己的MAC地址来取代发送者的MAC地址进行应答。对于接收机，发送者的IP地址其实是不可见的。

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151210288.png)

*.ARP欺骗

核心原理：

1.ARP是一种无连接协议（和UDP协议类似）

2.收到AEP请求/响应的主机无法确认MAC/IP/MAC-IP对应关系的真假。

种类：

1.伪造网关（仅针对两台主机不在同一局域网的情况）

攻击者B伪造ARP报文（senderIP地址是网关的，senderMAC地址不是网关的），发送给网段内的主机A，那么主机A就会把网关的ip地址和伪造的mac地址缓存到arp缓存表内，导致主机A无法把要发给网关的消息送达网关，致使主机A无法正常访问外网

2.欺骗网关

攻击者B伪造ARP报文（senderIP地址是主机A的，senderMAC地址不是主机A的），发送给网关，网关就把主机A的ip地址和伪造的mac地址缓存到网关arp缓存表内，导致网关无法给主机A发送消息，致使主机A无法正常访问外网

3.单一欺骗攻击

攻击者B伪造ARP报文（senderIP地址是主机C的，senderMAC地址不是主机C的），发送给主机A，主机A就把主机C的ip地址和伪造的mac地址缓存到主机A的arp缓存表内，导致主机A无法给主机C发送消息

4.FLOOD攻击

攻击者伪造大量不同ARP报文在同网段内进行广播，导致网关ARP表项被占满，合法用户的ARP表项无法正常被读取，导致合法用户无法正常访问外网（也可以泛洪攻击其他主机）

#### 代理

正向代理：

比如翻墙就是正向代理的一种应用。客户端发送给目标服务器的内容会通过代理服务器中继，一般原因是不通过代理服务器无法无法直接访问目标服务器。

反向代理;

比如负载均衡就是反向代理的一种应用。代理服务器收集多个客户端的访问请求之后判断哪个服务器负载较小，就将请求发给这个服务器。
![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151210289.png)

#### 虚拟主机

通过把服务器分区的方式让服务器可以通过相应不同的需求调用不同的网站，分出的每个区就是一个虚拟主机。

##### 常见的虚拟主机划分：

1.基于IP

通过使用一台含有多张网卡的服务器，（一张网卡可以提供一个IP地址）让每个虚拟主机都对应一个IP

2.基于域名

一台电脑上只有一个IP，这个IP之下存放很多网站，每个网站所对应的虚拟主机名都不一样。但这么做需要DNS域名解析服务的支持。

3.基于端口

在同一个端口下，不同的端口号分配给不同的虚拟主机。打个比方，PHPStudy中如果要同时使用DVWA和sqli-labs就可以通过分配不同的端口号来实现，此时这两个网站就相当于PHPStudy服务器下的两台虚拟主机。
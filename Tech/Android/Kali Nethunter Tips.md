### 前言

现在我们已经拥有了一台可调用安卓硬件的完整的Kali Nethunter攻击机。那让我们开始干坏事吧！（不是）

### 1.ADB模拟电脑终端

ADB指令大全详见：https://quickref.cn/docs/adb.html

补充资料：https://cloud.tencent.com/developer/article/1621182



与传统adb不同，以攻击为目的的adb桥接最好不要用到实体介质，故最好使用tcp连接adb的方式。

首先需要激活靶机adb调试中的tcp模式。对于旧版本的一加手机，在开发者选项中有开关可一键激活：

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151147934.png)

在ADB over network被激活之后，下方会有设备在局域网中的IP地址和adb服务端口号。默认为5555，即安卓默认监听端口。

但绝大多数手机在低于安卓11的版本是没有开放的无线调试接口的，可以通过软件【ADB WIFI】实现（需要root权限）。

在kali终端中输入adb connect IP:端口号（一般默认为5555），第一次输入后会弹窗是否授予调试权限，点击确认后终端重复输入一次即可链接无限调试。adb devices指令若出现靶机ip地址则已经连接成功。![IMG_20230128_151503](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151147954.jpg)渗透时一般无法得知靶机的ip地址，可以通过软件【pingtools pro】进行ip地址扫描和端口扫描。

![IMG_20230128_151802](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151147012.jpg)![IMG_20230128_144912](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151148602.jpg)

进入adb shell后即可提权对靶机系统进行操作：

![1674890671671](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151148640.jpg)

ls：显示根目录。

pm list packages -f 显示所有安装包包名及其绝对地址。

su:授予超级用户权限（前提是已经获取root）

事实上adb shell可以用来做非常方便的自动化工作，不仅可以修改应用，文件；还可以模拟文本输入，点击，甚至是实体按键。

![IMG_20230128_155809](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151148869.jpg)

自动化文本输入。（需要注意的是原生adb也有推送文本的功能，但不支持UTF-8，即中文输入）

### 2.Nethunter Kex

与VNC server是一个东西，即在本地创建一个虚拟网络控制台，在该控制台内加载kali linux桌面，实现kali linux GUI的随身携带。
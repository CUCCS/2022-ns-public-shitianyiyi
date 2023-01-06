# 第三次实验报告

## 实验环境
>VirtualBox 虚拟机

>Attacker：Kali Rolling 2022

>Gateway：Debian Buster

>Victim：Kali
## 实验工具
    tinyproxy

    tinyproxy简介

    apache ：web服务器

    curl:cURL是一个利用URL语法在命令行下工作的文件传输工具


## 实验目的

1.在Kali Linux中安装tinyproxy

2.然后用主机设置浏览器代理指向tinyproxy建立的HTTP正向代理

3.在Kali中用wireshark抓包，分析抓包过程

4.理解HTTP正向代理 HTTPS 流量的特点。

## Tinyproxy

![2](img\tiny.png)

>Tinyproxy很容易配置和修改。

>较小的内存占用意味着它在操作系统上占用很少的空间。它的内存占用几乎是2MB。

>匿名模式允许授权允许和不允许的单个HTTP头。

>通过阻止未经授权的用户进行访问控制。

>过滤是指用户通过创建黑白名单来阻断或允许某个域。

>通过控制从HTTPS/HTTP服务器传入和传出的数据实现隐私特性。

## 实验步骤
1.在Ubuntu系统中输入以下命令更新系统到最新：
```
sudo  apt-get  update
sudo  apt-get  upgrade -y

```
2.更新完成后，执行以下命令安装Tinyproxy
```
sudo apt-get -y install tinyproxy

```
3.完成Tinyproxy的安装后。要启动和检查Tinyproxy的状态，输入以下命令：
```
sudo systemctl tinyproxy start
sudo systemctl tinyproxy status
```
4.自定义配件
Tinyproxy配置文件位于以下路径：
```
etc/tinyproxy/tinyproxy.conf
```
使用vim文本编辑器编辑它：
```
sudo vim /etc/tinyproxy/tinyproxy.conf

```
5.如果要允许第三方设备使用本代理服务，在配置文件中找到以下这行：
```
Allow 127.0.0.1
```
把127.0.0.1修改为客户端的IP地址或者一个IP范围，比如192.168.1.0/24

6.接下来找到Listen 192.168.0.1，修改为本服务器连接外网的网卡IP地址，使用ip addr查看本机网卡的IP地址。这步操作主要是对外开放代理服务，不然第三方设备无法使用该服务器的代理服务。

7. 配置过滤器

可以通过使用tinyproxy来添加流量过滤器,找到Filter "/etc/tinyproxy/filter" 这行内容，取消对这一行的注释，可以把过滤器配置文件路径指定为一个域名。

在后面的行修改成以下这样:
![2](img\tinychange.png)

8.保存退出文件。现在假设您把过滤器放在本地，路径为etc/tinyproxy/filter，接下来就要编辑过滤器了。
```
sudo vim etc/tinyproxy/filter
```
9.逐行添加域名作为黑名单。

10.为tinyproxy服务配置防火墙
![2](img\tinyfire.png)
执行以下2条命令开放该端口
```
firewall-cmd --zone=public --add-port=8888/tcp
firewall-cmd --zone=public --add-port=8888/tcp --permanent

```
在未开启apache前， 靶机对攻击者的HTTP请求没有响应 ，此时由于web服务器还未开启

11.开启apache服务，命令是:startsystemctl start apache2
cd到/etc/rc.d/init.d/目录，并列出该目录下的所有文件，看看是否有httpd
![2](img\apache.png)
使用httpd -v查看已经安装的httpd的版本
![2](img\httpd.png)
使用rpm -qa | grep httpd查看是否已经安装了httpd
![2](img\rpm.png)
使用ps -ef | grep httpd查看httpd的进程
![2](img\ps-ef.png)
使用service httpd status查看httpd的运行状态
![2](img\service.png)
13.开启apache以后靶机可以通过浏览器访问攻击者

攻击者仍无法访问靶机

至此，攻击者还是无法访问靶机，接下来使用正向代理，tinyproxy
14.开启tinyproxy服务
攻击者配置代理，网关设为代理，端口设为默认值8888

配置完成后，攻击者在浏览器对靶机进行访问，同时在靶机上抓包
```
命令:tcpdump -i eth0 -n -s 65535 -w attacker.pcap //抓包长度限制为65535，并把结果保存下来
```
15.攻击者访问靶机的apache，同时让靶机进行抓包，访问两个地址分别为:172.16.111.133和172.16.111.133/nationalday，分别出现apache界面和404

查看抓包结果,由于本次实验只关心http协议，所以过滤后剩下以下信息

选取其中404的包追踪http流，发现我们能看到代理，但是看不到具体的信息（ip地址等等信息）
## 总结
1.本次实验通过设置代理服务器的方式改变了原来网络的连通性，攻击者在未设置代理之前，无法访问靶机，而在经过tinyproxy的设置后，可以进行访问。

2.代理服务器可以看到主机访问的网址。


## 参考链接：
1.http://www.freebuf.com/news/45929.html
2.https://www.bamsoftware.com/sec/goagent-advisory.html
3.https://www.cnblogs.com/gaosf/p/10154786.html
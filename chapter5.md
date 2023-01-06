# 实验五 网络扫描实验：使用nmap/scapy进行主机和网络扫描实验

![2](img\网络扫描.png)
# Nmap工具使用
## 实验目的和实验工具

1.通过Nmap对局域⽹主机进⾏扫描，了解常用的Nmap指令

2.nmap(网络映射器)–一款开源代码的网络探测和安全审核工具

## 实验步骤
1.查看⼀下自⼰的IP地址，为后续探测做准备
2.扫描内⽹存活的主机,选择目标主机
```
nmap -sP -A 4 +目标主机 
```
通过扫描自己的PC，熟悉nmap图形界面Zenmap的基本使用。打开Zenmap，点击“扫描”—>“新建窗口”，在目标框中输入IP地址172.26.10.102，不修改配置直接点击扫描，等待返回扫描结果。点击状态界面的“扫描”选项卡，可以看到扫描进度，扫描完成后，可以看到被扫描对象的各种状态，包括端口、拓扑图、主机操作系统类型等信息。

扫描结果如下，可以看出999个端口都是关闭的
![2](img\主机扫描.png)
3.扫描该目标主机常见服务,并同时进行抓包，nmap可以扫描到目标主机常见端口服务
```
nmap +目标主机
```
4.扫描目标主机的操作系统 ，查看目标主机各个服务详细版本信息 ，奇怪的是，这里没有扫描到该有的信息，我们打开目标主机的apache服务后，再次进行扫描，发现得到了80端口的服务信息，而且得到的信息显示debian
```
nmap -sV -O +目标主机ip
#-O : Enable OS detection
#-sV : Probe open ports to determine service/version info
```
5.对本机所处网段进行扫描，分析本网段环境：根据图7的目标栏，在扫描目标中输入：172.22.252.0/24，将配置改为：Intense scan, all TCP ports，然后运行扫描任务，扫描任务停止后即可查看返回结果。在左侧“主机”中会列举出被扫描网段中所有存活的主机以及操作系统类型，点击相关主机即可查看主机的详细信息。接着，选择“扫描”菜单中“保存扫描”选项可以保存扫描结果，保存类型选用默认的XML格式。可用IE浏览器打开并查看该XML文件。
![2](img\网段扫描.png)
6.使用 --script-args选项的脚本扫描
一般普通用户在向一些Web网站发送HTTP请求时，网站会提取HTTP请求中的user-agent来判断该请求是否合法。事实上，在nmap使用http-methods脚本扫描时，会发送带有nmap特征的请求报文。输入命令：nmap --script=http-methods 172.26.10.2时，并使用wireshark抓取该命令的扫描请求报文，并追踪HTTP流。
![2](img\wireshark.png)
可以看到在user-agent中有nmap的特征“nmap scripting engine”，这也验证了在nmap使用http-methods脚本扫描时，会发送带有nmap特征的请求报文。

因此，在使用某个脚本时，若该脚本支持传入参数，可以利用--script-args选项来修改数据包。在上面的http-methods脚本扫描案例中，若目标站点根据nmap特征来拒绝nmap的HTTP请求；此时，若攻击者还想让网站通过判断nmap的HTTP请求是合法请求，以绕过网站的对nmap请求包的检测，就可以利用--script-args修改nmap的HTTP请求包中的user-agent，命令为：
```
nmap --script=http-methods --script-args http.useragent="Mozilla 42" 172.26.10.2；
```
再使用wireshark抓包，可发现此时nmap的HTTP请求包中含有的useragent与命令中传入的http.useragent一样。

## 总结
1.尽量在root用户权限下使用Nmap ，否则容易实现不了预期的结果。 这个是因为操作系统限制了「构造畸形包」这一行为，换句话说只有root用户有这个权限。所谓畸形包就是协议之外产生的包。操作系统知道你好好的凭空发RA这种肯定有问题的。那么如果是root用户那么操作系统认为你知道自己在做什么，否则操作系统拒绝往外发RA这种包

>我们使用非管理员权限使用nmap进行扫描，当然在此之前，我们要在kali中新建一个非root用户

>使用该用户(test)进行nmap扫描，我们发现TCP connect可以正常扫描，但是其他受到了权限的限制，查询资料发现确实如此，TCP connect scan不需要管理员权限

2.Nmap运⾏通常会得到被扫描主机端⼝的列表


## 参考链接
1.https://blog.csdn.net/qq_28656907/article/details/52193777
2.https://c4pr1c3.github.io/cuc-ns/chap0x05/main.html
3.https://www.stationx.net/nmap-cheat-sheet/
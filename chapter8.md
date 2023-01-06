# H8 防火墙实验：iptables配置

## 实验目的
1.熟悉iptables 防火墙使用原理

2.学习使用 iptables 进行一些网络安全配置

## iptables概述
iptables是与内核集成的IP信息包过滤系统。它是一种工具，也称为用户空间（userspace）

## 实验步骤
新建3个虚拟机，分别作为内网、网关（防火墙）、外网，如下:
![2](img\防火墙.png)
1.启动防火墙/ 关闭防火墙 (加sudo)
```
Ufw enable/ ufw disable
```
![2](img\open.png)
2.查看本机关于iptables 的配置
```
iptables -L
```
![2](img\iptables.png)
3.清除预设表filter 中的所有规则
```
iptables -F
```
![2](img\F.png)
4.保存现有规则
使用命令 /sbin/iptables-save 这样可以将现有的设置写入，这样重启后也不会消除
![2](img\sbin.png)
5.使用iptables filter表完成包过滤的一些操作,将INPUT、FORWARD和OUTPUT链默认策略均设置为DROP，记录使用的命令， 使用iptables -L 返回规则表，截图保存。 ping 一下其他主机观察结果。
![2](img\input.png)
![2](img\ping.png)
6.配置ssh 规则，只允许某一个IP地址的主机，使用ssh服务登录你的主机。
```
vim/etc/hosts.allow
vim/etc/hosts.deny
```
![2](img\vimallow.png)

![2](img\vimdeny.png)
7.配置ICMP规则，防止ping攻击

![2](img\ICMP.png)
8.端口映射，将源地址转换，将内网的地址隐藏，将内网的地址全映射成1.1.1.0-1.1.1.255之间的地址，并使用ping观察效果。

![2](img\映射1.png)

![2](img\映射2.png)
## 参考链接
1.https://www.codenong.com/cs106307401/
2.http://www.zsythink.net/archives/1199
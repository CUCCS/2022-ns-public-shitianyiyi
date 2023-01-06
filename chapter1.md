# 2022-ns-public-shitianyiyi
2022-ns-public-shitianyiyi created by GitHub Classroom
# 基于 VirtualBox 的网络攻防基础环境搭建

## 实验要求

1.靶机可以直接访问攻击者主机

2.攻击者主机无法直接访问靶机

3.网关可以直接访问攻击者主机和靶机

4.靶机的所有对外上下行流量必须经过网关

5.所有节点均可以访问互联网

## 实验步骤

1.虚拟机的多重加载

    > 管理->虚拟介质管理->虚拟硬盘
    > 选中导入的vdi文件后，类型->多重加载 多重加载

配置一块安装了kali的 .vdi 硬盘多重加载，并在virtualbox中新建三台虚拟机，分别设置为Victim、Gateway、Attacker
![0](img\多重加载.png)

2.分别给三台虚拟机添加网卡
首先给三台虚拟机添加网卡，网卡类型如下：
![1](img\网卡类型.png)
对三台虚拟机网卡的配置如下：
![2](img\eth0.png)
Victim
![2](img\Victim.png)
Gateway
![2](img\gateway.png)
Attacker
![2](img\attacker.png)
3.开启 Gateway ipv4转发功能，添加Gateway防火墙NAT规则，设置网关转发局域网192.168.1.0/24中的数据包。
   
默认情况下，linux的三层包转发功能是关闭的，所以网关如果收到目的地址不是本机网卡ip的时候，会直接将数据包丢弃。如果要让**Gateway**实现转发，需要改变 Gateway 的一个系统参数以打开 ipv4 转发功能。

使用以下三条指令都可以打开

      #方法1
      echo 1 > /proc/sys/net/ipv4/ip_forward

      #方法2
      sysctl net.ipv4.ip_forward=1

      #方法3
      #编辑 vim /etc/sysctl.conf  
      #添加一条语句 net.ipv4.ip_forward =1
       sysctl -p
再添加网关防火墙NAT规则
```
iptables -t nat -A POSTROUTING　-s 192.168.1.0/24 -o eth1 -j  MASQUERADE
```
配置对应的网卡开启和关闭防火墙规则，保存现有的iptables 防火墙NAT规则。
```
cat << EOF >  /etc/network/if-pre-up.d/firewall
#!/bin/sh
/usr/sbin/iptables-restore  <  /etc/iptables.rules
exit 0
EOF
chmod +x  /etc/network/if-pre-up.d/firewall


cat << EOF >  /etc/network/if-post-down.d/firewall
#!/bin/sh
/usr/sbin/iptables-save  -c  >  /etc/iptables.rules
exit 0
EOF
chmod +x  /etc/network/if-post-down.d/firewall


```
添加完成后，查看路由规则已经被配置好
![3](img\luyou.png)
4.拓扑关系
![3](img\拓扑.png)
## 连通性验证
1.靶机可以直接访问攻击者主机
![3](img\L1.png)
2.攻击者主机无法直接访问靶机
![3](img\L2.png)
网关可以直接访问攻击者主机和靶机
3.网关到靶机
![3](img\L3.png)
4.网关到攻击者
![3](img\L4.png)

## 参考链接：
1.https://blog.51cto.com/wwdhks/1154032
2.https://ipset.netfilter.org/iptables.man.html

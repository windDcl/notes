## ==使用桥接模式==
桥接模式指的是虚拟机和宿主机同一个级别。虚拟机和宿主机都使用路由器作为网关，虚拟机会在网段里被分配真实的ip地址。分配之后，机器可以设置静态ip。
1. 虚拟机软件设置桥接
![[Pasted image 20230708115834.png]]
2. 各机器设置桥接
![[Pasted image 20230708120038.png]]
3. 设置虚拟机网卡，设置静态ip
```
vi /etc/sysconfig/network-scripts/ifcfg-ens160 
有的网卡可能是ifcfg-ens30，根据情况而定
加入一下内容，X需要自行替换

TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
NAME=ens160
UUID=8fc8964f-1177-4a9b-89db-793b3b20e38b
DEVICE=ens160
ONBOOT=yes

# 这是虚拟机的ip地址
IPADDR=192.168.10.100
# 路由器网关地址。可以在设置--网络--wifi--路由器地址查看
GATEWAY=192.168.10.1
NETMASK=255.255.255.0
# 必须配置
DNS1=114.114.114.114
DNS2=8.8.8.8

```

## 常用的命令
```
yum install net-tools

ifconfig

systemctl start network

systemctl stop firewalld

# 查看端口占用
lsof -i:10000
# 或者
netstat -nltp|grep 10000

# 免密
ssh-keygen 生成秘钥
# 免密连接linux01
ssh-copy-id root@linux01  

# 安装rpm包
rpm -ivh xxx.rpm 
# 查看rpm包是否安装
rpm -qa|grep -i mysql
# 删除yum包
yum remove xxx
```


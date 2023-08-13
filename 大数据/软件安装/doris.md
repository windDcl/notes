	环境：
	doris-1.1.4版本安装成功，2.0beta和1.2.4.1版本各种原因安装失败。
	centos7 arm架构
	jdk 1.8
	
## 系统设置
需要对linux的系统提前做一些设置
关于硬盘扩容参考：[[linux#重新分区]]
```
1.设置系统打开文件句柄数
vi /etc/security/limits.conf

# 文件末尾写入下面的内容
* soft nofile 65535
* hard nofile 65535
* soft nproc 65535
* hard nproc 65535
如果不修改启动be会报错

2.时钟同步
多个FE之间必须保持时钟同步
yum install ntpdate -y

ntpdate ntp.sjtu.edu.cn

# 将当前时间写入bios，这样才能永久生效不变，不然reboot后还会恢复到原来的时间clock -w

3.关闭swap分区
原因是交换分区的作用是如果内存不够，那么使用硬盘的一小部分分区来充当内存。这样对doris查询效率有影响。
# 查看 Linux 当前 Swap 分区
free -m

# 关闭 Swap 分区swapoff -a
free -m

4.硬盘扩容
由于doris占用的磁盘空间和内存都比较大。如果是虚拟机的话，最好扩充下磁盘。否则会启动失败。第一次使用我是共20G磁盘，然后FE启动失败。目前的配置是虚拟机共分配64G，其中43G是一个分区只存放doris，启动FE成功
关于硬盘扩容参考：[[linux]]
```

## doris安装说明
* doris的FE磁盘用来存储元数据，包括日志和镜像需要占用几百MB~几个GB
* BE用来存储用户数据，还需要保留40%空间用来后台数据合并和保存中间数据
* 一台机器可部署多个BE，但只能部署一台FE。但是如果要多个副本，那么需要多个BE
* BE的数量决定了查询的延迟
* FE的Follower数量必须是奇数，且必须有一个Follower。3个Foller可以实现读写高可用，1个Follower+1个observer实现读高可用
* Broker是访问外部数据（如hdfs）的进程，在每台机器都需要部署一个
* 经验安装：实时->3Follower+1~3个observer；离线->1Followr+1~3个observer

## 安装FE

1. 官网下载源码包，官网地址：https://doris.apache.org
2. 解压
3. 修改配置
```shell
vi /newdata/doris/fe/conf/fe.conf

#配置文件中指定元数据路径： 注意这个文件以后会很大，所以最后把他放在单独一个磁盘下而且容量要大。
meta_dir = /newdata/data/dorisdata/doris-meta

#修改绑定 ip（每台机器修改成自己的 ip）注意格式最后一个ip位要写0
priority_networks = 192.168.3.0/24


# 然后启动
/newdata/doris/fe/bin/start_fe.sh 非后台启动，可看到启动日志
/newdata/doris/fe/bin/start_fe.sh --daemon(这个是后台启动）
# jps查看是否有下面的进程
6391 PaloFe
6542 Jps
```


## 安装BE

1. 修改配置
```
vi be.conf

#配置文件中指定数据存放路径：
storage_root_path = /newdata/data/dorisdata/bedata

#修改绑定 ip（每台机器修改成自己的 ip）
priority_networks = 192.168.3.0/24


注意这里的数据存放路径可以设置多个
storage_root_path=/home/disk1/doris.HDD,50;/home/disk2/doris.SSD,10;/home/disk。
可以指定磁盘的类型和限制doris使用的最大容量(单位G)，也可以指定多个磁盘分区需要用;隔开
```
2. 启动
```
be/bin/start_be.sh
```

3. be启动报错：
```
Please set vm.max_map_count to be 2000000 under root using 'sysctl -w vm.max_map_count=200000
```
执行这个命令即可
```
sysctl -w vm.max_map_count=2000000
```
参考 https://blog.csdn.net/weixin_43114209/article/details/131395344

4. 查看是否成功
```
netstat -nltp|grep be
tcp        0      0 0.0.0.0:8040            0.0.0.0:*               LISTEN      7133/doris_be       

tcp        0      0 0.0.0.0:8060            0.0.0.0:*               LISTEN      7133/doris_be       

tcp6       0      0 :::9060                 :::*                    LISTEN      7133/doris_be       

tcp6       0      0 :::9050                 :::*                    LISTEN      7133/doris
```
## mysql建立fe和be的关系
1. 首先要有mysql的客户端。
参考 [[mysql#仅安装客户端]]
2. mysql-cli连接fe
```
mysql -uroot -p -P9030 -hlinux02

-- 设置登录密码
> SET PASSWORD FOR 'root' = PASSWORD('123456');
> -- 查看fe的状态
> SHOW PROC '/frontends'\G;
> -- 添加BE节点
> ALTER SYSTEM ADD BACKEND "linux02:9050";
> -- 查看 BE 状态 ALive=true表示存活
> SHOW PROC '/backends';

```
这里只是用mysql的客户端去连接doris。

## 添加broker角色

## FE扩容

## BE扩容


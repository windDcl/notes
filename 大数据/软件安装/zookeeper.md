**下载**
官网下载即可。我这里下载的是最新稳定版3.7.1
```
wget https://dlcdn.apache.org/zookeeper/zookeeper-3.7.1/apache-zookeeper-3.7.1-bin.tar.gz
```

**解压安装**
```
tar -zxvf apache-zookeeper-3.7.1-bin.tar.gz -C /opt

mv /opt/apache-zookeeper-3.7.1-bin /opt/zookeeper
```

**配置zk**
```
mkdir /opt/zookeeper/zkData

vi zkData/myid
myid写入一个机器号1.集群内其他机器的机器号都不同。另外两台linux02和linux03分别写2、3

mv /opt/zookeeper/conf/zoo_sample.cfg zoo.cfg
# 修改zoo.cfg文件
vi zoo.cfg
	# 修改dataDir目录位置
	dataDir=/opt/module/zookeeper-3.5.7/zkData
	# 增加下面配置
	server.1=linux01:2888:3888
	server.2=linux02:2888:3888
	server.3=linux03:2888:3888

这的server.1后的1是myid里的值。后面的2888是机器间通信的端口，3888是备用通信端口
```

将配置好的zk分发到linux02和linux03

**启动zk**
```
# 各个机器执行命令
bin/zkServer.sh start

# 查看zk的状态
bin/zkServer.sh status
```
**一键启动脚本**
```shell
#!/bin/bash

for i in 1 2 3

do
echo "ssh linux0${i}..."
ssh linux0${i} "source /etc/profile;/opt/zookeeper/bin/zkServer.sh $1"

done
```

## 配置kerberos认证
https://developer.aliyun.com/article/25626

## zookeeper选举相关api
https://blog.csdn.net/younger_china/article/details/53063426

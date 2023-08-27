## 安装
下载jar包解压即可
附下载链接。这里我用的是1.9版本。经测试最新版的1.11的官网监听端口的用例无效。
```
https://archive.apache.org/dist/flume/1.9.0/
```

## flume基本使用
编写一个source类型是netcat。channel为内存类型，sink使用输出到日志logger类型。

在flume-agent下创建一个目录job，编写配置文件
```shell
mkdir job
vim job/flume-netcat-logger.conf
```

flume-netcat-logger.conf
```shell
# Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 44444

# Describe the sink
a1.sinks.k1.type = logger

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 3
a1.channels.c1.transactionCapacity = 2

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```
说明：
- a1是该agent的名字
- flume有3个组件，source、channel、sink
- 每个组件都有type
- source和chanel有链接
- sink也和channel有链接

**启动**
```shell
# 前台启动，输出到日志
bin/flume-ng -c conf -n a1 -f job/flume-netcat-logger.conf -Dflume.root.logger=INFO,console

# 后台启动
nohup bin/flume-ng agent -n a1 -c conf -f job/flume-netcat-logger.conf 1>/dev/null 2>&1 &
```

## source类型
>exec source，监听系统命令
>taildir source，监听文件夹下的每个文件行数，可记录偏移量
>avro source，监听某个端口的序列化成avro的数据。
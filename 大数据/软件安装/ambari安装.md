入门参考： https://zhuanlan.zhihu.com/p/101940942

## ambari简介
>Ambari_已支持大多数Hadoop组件，包括HDFS、MapReduce、Hive、Pig、 Hbase、Zookeeper、Sqoop和Hcatalog等，并支持他们的管理。是5个顶级hadoop管理工具之一。
## ambari功能
- 提供任意数量的hadoop集群的安装，并且是指导式安装。
- 可以web-ui统一修改集群的配置文件
- 可以统一启停集群，重新配置
- 可以监控集群（节点状态，磁盘空间）

## ambari架构

ambari-server、ambari-agent、ambari-metrics

**用户和集群交互过程：**
用户访问web-ui。web向ambari-server发起post或get请求，server接受到后通过心跳机制向agent发送指令，最后agent执行py或者shell脚本完成。

**web展示的数据类型**
一种是metrics不断地采集节点的状态实时展现。
还有一种由ambari-server缓存在内存，持久化到数据库的数据，这类数据比如主机列表、服务列表、服务的配置信息等。这些只有当调用restapi才会保存。

## 安装

参考： https://juejin.cn/post/7079074822688505892



datasophon另一款国产的软件

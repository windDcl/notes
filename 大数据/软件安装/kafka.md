```
 官网下载
 # 解压
tar -zxvf kafka_2.12-3.5.0.tgz -C /opt/

vim kafka/config/server.properties

# 修改
broker.id=1
zookeeper.connect=linux01:2181,linux02:2181,linux03:2181
log.dirs=/opt/data/kafkalogs

```

**一键启停**
```
#!/bin/bash

for i in 1 2 3

do

echo "ssh linux0${i}..."

ssh linux0${i} "source /etc/profile;/opt/kafka/bin/kafka-server-$1.sh -daemon /opt/kafka/config/server.properties"

done
```
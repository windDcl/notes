## 1.java
参考 https://www.jianshu.com/p/7825cfed60ae

镜像源地址：https://repo.huaweicloud.com/java/jdk/

```
```bash
# cd /usr/
# mkdir java
# cd java

```cpp
# wget https://repo.huaweicloud.com/java/jdk/8u181-b13/jdk-8u181-linux-arm64-vfp-hflt.tar.gz


tar -zxvf jdk-8u181-linux-arm64-vfp-hflt.tar.gz 


```bash
#java
export JAVA_HOME=/opt/jdk1.8.0_181
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib
```


## 2.hadoop
版本：3.2.4
前提：
```
各个机器之间和自己之间免密
关闭防火墙
已安装了java，设置了java_home
```

**1. hdfs-site**
```xml
<configuration>

    <property>

        <name>dfs.namenode.http-address</name>

        <value>linux01:9870</value>

    </property>

        <!-- 2nn web端访问地址-->

    <property>

        <name>dfs.namenode.secondary.http-address</name>

        <value>linux02:9868</value>

    </property>

</configuration>
```

**2. core-site.xml**
```xml
  

<configuration>

 <!-- 指定NameNode的地址 -->

    <property>

        <name>fs.defaultFS</name>

        <value>hdfs://linux01:8020</value>

    </property>

  

    <!-- 指定hadoop数据的存储目录 -->

    <property>

        <name>hadoop.tmp.dir</name>

        <value>/opt/hdpdata/data</value>

    </property>

  

    <!-- 配置HDFS网页登录使用的静态用户为dcl -->

    <property>

        <name>hadoop.http.staticuser.user</name>

        <value>dcl</value>

    </property>

</configuration>
```

**3. hadoop-env.sh**
```xml
export JAVA_HOME=/usr/local/java/jdk
```

**4. workers**
```
linux01
linux02
linux03
```
**5. yarn-site.xml**
```xml
<configuration>
<!-- Site specific YARN configuration properties -->

<!-- 指定MR走shuffle -->

    <property>

        <name>yarn.nodemanager.aux-services</name>

        <value>mapreduce_shuffle</value>

    </property>

    <!-- 指定ResourceManager的地址-->

    <property>

        <name>yarn.resourcemanager.hostname</name>

        <value>linux01</value>

    </property>


    <!-- 环境变量的继承 -->

    <property>

        <name>yarn.nodemanager.env-whitelist</name>

        <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>

    </property>

</configuration>
```

**6.mapred-site.xml**
```xml
<configuration>

<!-- 指定MapReduce程序运行在Yarn上 -->

    <property>

        <name>mapreduce.framework.name</name>

        <value>yarn</value>

    </property>

</configuration>
```

**7. 设置hadoop用户为root**
在sbin/start-dfs.sh  /  stop-dfs.sh / start-yarn.sh / stop-yarn.sh添加下面这几行，否则启动失败
```
HDFS_DATANODE_USER=root
HADOOP_SECURE_DN_USER=hdfs
HDFS_NAMENODE_USER=root
HDFS_SECONDARYNAMENODE_USER=root
YARN_RESOURCEMANAGER_USER=root
YARN_NODEMANAGER_USER=root
```
**8.  在namenode的机器上初始化集群**
```
hdfs namenode -format
```
**9. 启动集群**
```
start-dfs.sh
```

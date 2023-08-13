## 环境：
	centos 7 arm架构  
	mysql 8.0.28版本
	hadoop :  3.2.4
	hive 3.1.1
	经测试，这个版本的hive下lib的guava版本和hadoop common包下的guava版本不一致。需要替换成hadoop下的jar包


## 下载hive
官网下载： http://archive.apache.org/dist/hive/
```
解压
配置环境变量
```

## 配置mysql元数据库

### 1.新建Hive元数据库
我这里数据库名写的hive
```
mysql -uroot -p123456
create database hive;
exit;
```
### 2.mysql设置允许远程登录（必须）
	mysql安装后，默认没有开启远程登录，只能使用localhost或者127.0.0.1。这个必须设置一下
```
mysql -u root -p123456

use mysql;

select host, user from user;

update user set host = ‘%’ where user = ‘root’;
此时，root用户的权限已经允许非localhost登录

# 刷新设置，即可远程登录
FLUSH PRIVILEGES;
```
### 3.MySQL的JDBC驱动拷贝到Hive的lib目录下

	安装包可以在官网拿到 https://downloads.mysql.com/archives/c-j/ 
	选择对应的mysql版本
	由于官网下载后的是rpm包。需要安装一下。
```
安装： rpm -ivh mysql-connector-java-8.0.28-1.el7.noarch.rpm

得到jar包：/usr/share/java/mysql-connector-java.jar

cp /usr/share/java/mysql-connector-java.jar $HIVE_HOME/lib
```
### 4.配置元数据、hiveserver2、metastore
	几个注意点：
	1. 如果是6以下版本的mysql。需要设置javax.jdo.option.ConnectionDriverName选项需要设置com.mysql.jdbc.Driver
	2. jdbc连接必须有这个参数allowPublicKeyRetrieval=true
	3. &amp;的作用是xml不能写&，需要转义
	4. hiveserver2作用是为用户提供jdbc访问hive方式。但是集群内访问hadoop使用哪个用户这就需要用到hadoop的模拟用户了。默认是开启的hive.server2.enable.doAs=true。所以就必须在每个节点设置hadoop的代理用户

hive-site.xml
```xml
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
    <!-- jdbc连接的URL -->
    <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:mysql://linux01:3306/hive?useSSL=false&amp;allowPublicKeyRetrieval=true</value>
    </property>
    
    <!-- jdbc连接的Driver-->

    <property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>com.mysql.cj.jdbc.Driver</value>
    </property>

<!-- jdbc连接的username-->

    <property>

        <name>javax.jdo.option.ConnectionUserName</name>

        <value>root</value>
    </property>

    <!-- jdbc连接的password-->

    <property>
        <name>javax.jdo.option.ConnectionPassword</name>
        <value>123456</value>
    </property>
    <!-- Hive默认在HDFS的工作目录-->
    <property>
        <name>hive.metastore.warehouse.dir</name>
        <value>/user/hive/warehouse</value>
    </property>

<!-- 到这里后面的和元数据管理无关-->
<!-- 指定hiveserver2连接的host -->
<property>
<name>hive.server2.thrift.bind.host</name>
<value>linux01</value>
</property>
  
<!-- 指定hiveserver2连接的端口号 -->
<property>
<name>hive.server2.thrift.port</name>
<value>10000</value>
</property>
  
<!-- 指定metastore服务的地址 默认就是9083-->
<property>
<name>hive.metastore.uris</name>
<value>thrift://linux01:9083</value>
</property>

</configuration>
```

设置hadoop代理用户core-site.xml（和元数据管理无关，和hiveserver2有关）
```xml
vi $HADOOP_HOME/etc/hadoop/core-site.xml

添加以下配置

<!--配置所有节点的root用户都可作为代理用户-->

<property>

    <name>hadoop.proxyuser.root.hosts</name>

    <value>*</value>

</property>

<!--配置root用户能够代理的用户组为任意组-->

<property>

    <name>hadoop.proxyuser.root.groups</name>

    <value>*</value>

</property>

<!--配置root用户能够代理的用户为任意用户-->

<property>

    <name>hadoop.proxyuser.root.users</name>

    <value>*</value>

</property>


最后将这个文件分发到各个集群
```


### 5.初始化元数据库
```
schematool -dbType mysql -initSchema -verbose
```
### 6.启动
```
nohup hive --service metastore 2>&1 &
nohup hive --service hiveserver2 2>&1 &

远程连接，查看是否成功
beeline -u jdbc:hive2://linux01:10000 -n root
```

### 7.解决jar包冲突
可能会报错：
```
hadoop@fzqs-Laptop:/usr/local/hive/lib$ hive -version
Exception in thread "main" java.lang.NoSuchMethodError: com.google.common.base.Preconditions.checkArgument(ZLjava/lang/String;Ljava/lang/Object;)V
	at org.apache.hadoop.conf.Configuration.set(Configuration.java:1357)
	...
	at org.apache.hadoop.util.RunJar.main(RunJar.java:236)
```
这是因为 hive 内依赖的 guava.jar 和 hadoop 内的版本不一致造成的。
查看 hadoop 安装目录下的 guava.jar 版本：
```
ls $HADOOP_HOME/share/hadoop/common/lib/guava*
```
输出如下：
```
$HADOOP_HOME/share/hadoop/common/lib/guava-27.0-jre.jar
```

接着查看 Hive 下的 guava.jar 版本：
```
ls $HIVE_HOME/lib/guava*
```
输出如下：
```
$HIVE_HOME/lib/guava-19.0.jar
```
可以看到 Hadoop 内的 guava.jar 版本是要高于 Hive 内带的的，所以使用 Hadoop 的替代 Hive 的：
```
rm $HIVE_HOME/lib/guava-19.0.jar
cp $HADOOP_HOME/share/hadoop/common/lib/guava-27.0-jre.jar $HIVE_HOME/lib/

```
问题解决。

原因是这个节点的磁盘空间不足。yarn会判断yarn.nodemanager.local-dirs的目录是否还有空间。他的默认值是 ${hadoop.tmp.dir}/nm-local-dir。（来源参考hadoop官网：
https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-common/yarn-default.xml ）

所以我们可以重新设置下这个目录的值。这里我重新新增了一个本地磁盘分区。参考[[linux#重新分区]]

vim yarn-site.xml
```xml
<property>

     <name>yarn.nodemanager.local-dirs</name>

     <value>/newarea/yarn/local</value>

</property>

  

<property>

     <name>yarn.nodemanager.log-dirs</name>

     <value>/newarea/yarn/log</value>

</property>
```
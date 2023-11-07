**目前hadoop的架构是
ambari集成hadoop、spark、yarn、hive。用户权限控制使用kerberos、ldap、sssd和ranger。其中ranger的作用是用来控制用户是否有使用yarn、hdfs和hive的权限。
ldap用来添加用户。sssd负责同步用户到所有的节点上。
kerberos用来给集群的所有服务做安全认证。
只有通过了kerberos认证的用户才能够访问这些服务。**




当前的场景：
日志数据所有的  116500EPS。
集群共有384G内存，64台机器。这些机器是虚拟机。需要给他们划分kafka集群和es集群。怎么规划比较合理？
另外
84台供hadoop集群
6台供PG和Redis
![[Pasted image 20230810192912.png]]


待分析······
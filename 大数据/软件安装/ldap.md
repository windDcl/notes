
ldap是一种轻量级的访问目录的。可以称为数据库。

它可以作为kerberos的数据库。

可以对他添加、删除条目。

ldap结合sssd可以将ldap的用户同步到所有的hadoop集群上。可以保证每个hadoop集群都有这些用户。


目前hadoop的架构是
ambari集成hadoop、spark、yarn、hive。用户权限控制使用kerberos、ldap、sssd和ranger。其中ranger的作用是用来控制用户是否有使用yarn、hdfs和hive的权限。
ldap用来添加用户。sssd负责同步用户到所有的节点上。
kerberos用来给集群的所有服务做安全认证。
只有通过了kerberos认证的用户才能够访问这些服务。

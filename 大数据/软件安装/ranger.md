使用ranger同步ldap用户

参考： https://blog.csdn.net/Happy_Sunshine_Boy/article/details/104848430

1. Ranger同步用户是单向同步，从 Unix/LDAP/AD/FILE 中，通过Ranger-UserSync插件，同步用户和用户组到Ranger-admin（数据库）中。

2. 配置文件中ranger.usersync.cookie.enabled 默认为true，同步的外部用户在ranger中删除后，不会重复导入，如果ranger.usersync.cookie.enabled 设置为false，同步的外部用户删除后，**还可以再次同步。**

3. Ranger-UserSync同步用户不是实时的，根据同步源不同默认同步时间也不同，如果用户从Unix中同步用户和用户组信息，默认时间间隔==1分钟==；如果用户从LDAP中同步用户和用户组信息，默认时间间隔是==1小时==；

4. 不支持删除用户同步，不支持在用户源（Unix已验证）中删除用户和用户组操作，同步到Ranger；也不支持在Ranger中删除用户和用户组操作同步到用户源（Unix已验证）中。（linux中删除用户后，ranger已同步的用户不会被删除；同样ranger删除linux用户，linux用户不会真正删除）

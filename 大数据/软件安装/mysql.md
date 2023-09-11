
## 完全卸载旧的mysql
参考： https://www.jianshu.com/p/ef58fb333cd6
```
查找mysql的安装包
rpm -qa |grep -i mysql

# 卸载安装包
yum remove mysql-community-common-5.7.20-1.el7.x86_64
yum remove mysql-community-client-5.7.20-1.el7.x86_64
yum remove mysql57-community-release-el7-11.noarch
yum remove mysql-community-libs-5.7.20-1.el7.x86_64
yum removemysql-community-server-5.7.20-1.el7.x86_64

# 查找mysql相关目录
find / -name mysql
rm -rf 相关目录

# 删除其他文件
rm -rf /etc/my.cnf
rm -rf /var/log/mysqld.log
````

## mysql官网下载
选择合适的版本，下载第一个
https://downloads.mysql.com/archives/community/
![[Pasted image 20230710173503.png]]

```
wget https://downloads.mysql.com/archives/get/p/23/file/mysql-8.0.28-1.el7.aarch64.rpm-bundle.tar
```
## 卸载mariadb
```
先查询  rpm -qa |grep mariadb

如果有就卸载： 
rpm -e --nodeps mariadb-libs
```

## 安装mysql
```
tar -xf mysql-8.0.28-1.el7.aarch64.rpm-bundle.tar

rpm -ivh mysql-community-common-8.0.28-1.el7.aarch64.rpm

rpm -ivh mysql-community-client-plugins-8.0.28-1.el7.aarch64.rpm 

rpm -ivh mysql-community-libs-8.0.28-1.el7.aarch64.rpm 

rpm -ivh mysql-community-client-8.0.28-1.el7.aarch64.rpm 

rpm -ivh mysql-community-icu-data-files-8.0.28-1.el7.aarch64.rpm 

rpm -ivh mysql-community-server-8.0.28-1.el7.aarch64.rpm
```
## 初始化启动mysql
```
mysqld --initialize --console

chown -R mysql:mysql /var/lib/mysql/

systemctl start mysqld

# 查看初始密码
cat /var/log/mysqld.log |grep root

# 登录修改密码
mysql -uroot -pArqnZ/GjV2eW
alter user 'root'@'localhost' identified by '123456';

```

## 设置远程登录
默认是不能远程登录，只能使用localhost或者127.0.0.1。这个必须设置一下
```
mysql -u root -p123456

use mysql;

select host, user from user;

update user set host = ‘%’ where user = ‘root’;
此时，root用户的权限已经允许非localhost登录

# 刷新设置，即可远程登录
FLUSH PRIVILEGES;

# 远程连接
mysql -h linux01 -u root -p123456 -P 3306
```

## 仅安装客户端
```
rpm -e --nodeps mariadb-libs

rpm -ivh mysql-community-common-8.0.28-1.el7.aarch64.rpm

rpm -ivh mysql-community-client-plugins-8.0.28-1.el7.aarch64.rpm 

rpm -ivh mysql-community-libs-8.0.28-1.el7.aarch64.rpm 

rpm -ivh mysql-community-client-8.0.28-1.el7.aarch64.rpm 
```

## ubuntu安装mysql8

参考 
https://learnku.com/articles/56934
```
apt install mysql-server

```

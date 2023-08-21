
4.1.3初始化
```
cat << EOF | ldapmodify -Y EXTERNAL -H ldapi:///
dn: cn=config
changetype: modify
add: olcDisallows
olcDisallows: bind_anon

dn: cn=config
changetype: modify
add: olcRequires
olcRequires: authc

dn: olcDatabase={-1}frontend,cn=config
changetype: modify
add: olcRequires
olcRequires: authc
EOF



ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/core.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif
————————————————
版权声明：本文为CSDN博主「　Laurence」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/bluishglc/article/details/125168912
```

禁止匿名访问
```
cat << EOF | ldapmodify -Y EXTERNAL -H ldapi:///
dn: cn=config
changetype: modify
add: olcDisallows
olcDisallows: bind_anon

dn: cn=config
changetype: modify
add: olcRequires
olcRequires: authc

dn: olcDatabase={-1}frontend,cn=config
changetype: modify
add: olcRequires
olcRequires: authc
EOF
```

创建两个OU
```
cat << EOF | ldapadd -D "cn=admin,dc=example,dc=com" -w Admin1234!
dn: dc=example,dc=com
objectClass: dcObject
objectClass: organization
dc: example
o: example

dn: ou=users,dc=example,dc=com
objectclass: top
objectclass: organizationalUnit
ou: users
description: OU for user accounts

dn: ou=services,dc=example,dc=com
objectclass: top
objectclass: organizationalUnit
ou: services
description: OU for service accounts
EOF

```




```
cp -f /var/kerberos/krb5kdc/kdc.conf /var/kerberos/krb5kdc/kdc.conf.$(date +%s)
# 找到database_name这一行，将其注释掉;
# 并在下一行插入：database_module = openldap_ldapconf
sed -i 's/\(^\s*\)database_name\(.*\)/\1#database_name\2\n\1database_module = openldap_ldapconf/g' /var/kerberos/krb5kdc/kdc.conf
# 在文件尾部追加[dbmodules]部分
tee -a /var/kerberos/krb5kdc/kdc.conf <<EOF

[dbmodules]
    openldap_ldapconf = {
        db_library = kldap
        ldap_servers = ldap://linux01
        ldap_kerberos_container_dn = cn=kerberos,dc=example,dc=com
        ldap_kdc_dn = cn=admin,dc=example,dc=com
        ldap_kadmind_dn = cn=admin,dc=example,dc=com
        ldap_service_password_file = /etc/openldap-admin.keyfile
        ldap_conns_per_server = 5
 }
EOF

```


```




```

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
# 1. 安装OpenLDAP和导入数据
```shell
sudo apt-get update
sudo apt-get install slapd ldap-utils
```

- 安装OpenLDAP后，可以使用sudo dpkg-reconfigure slapd命令对OpenLDAP重新配置，比如设置BaseDN，用户名和密码等信息。配置的时候，需要填写的，根据实际情况填写，需要选择的，选择默认选项即可。

```shell
sudo dpkg-reconfigure slapd
```

# 2. 导入数据
- 添加组节点: 创建文件group.ldif,内容如下

```shell
dn: ou=people,dc=nowlan,dc=com
objectClass: organizationalUnit
ou: people

dn: ou=group,dc=nowlan,dc=com
objectClass: organizationalUnit
ou: group
```

- 导入组数据到OpenLDAP

```shell
ldapadd -x -D cn=admin,dc=nowlan,dc=com -W -f group.ldif
```

- 添加用户节点: 创建文件user.ldif，内容如下

```shell
dn: uid=ldapuser1,ou=people,dc=nowlan,dc=com
objectClass: top
objectClass: account
objectClass: posixAccount
objectClass: shadowAccount
cn: ldapuser1
uid: ldapuser1
uidNumber: 16859
gidNumber: 100
homeDirectory: /home/ldapuser1
loginShell: /bin/bash
gecos: user1
userPassword: {crypt}x
shadowLastChange: 0
shadowMax: 0
shadowWarning: 0

dn: uid=ldapuser2,ou=people,dc=nowlan,dc=com
objectClass: top
objectClass: account
objectClass: posixAccount
objectClass: shadowAccount
cn: ldapuser2
uid: ldapuser2
uidNumber: 16860
gidNumber: 100
homeDirectory: /home/ldapuser2
loginShell: /bin/bash
gecos: user1
userPassword: {crypt}x
shadowLastChange: 0
shadowMax: 0
shadowWarning: 0

dn: uid=ldapuser3,ou=people,dc=nowlan,dc=com
objectClass: top
objectClass: account
objectClass: posixAccount
objectClass: shadowAccount
cn: ldapuser3
uid: ldapuser3
uidNumber: 16861
gidNumber: 100
homeDirectory: /home/ldapuser3
loginShell: /bin/bash
gecos: user1
userPassword: {crypt}x
shadowLastChange: 0
shadowMax: 0
shadowWarning: 0
```
> 如果需要添加更多用户，则复制上面的内容。比如添加ldapuser4,则可以复制添加ldapuser3那一段内容，然后将ldapuser3替换为ldapuser4，并修改uidNumber的值。

- 导入用户数据到OpenLDAP

```shell
ldapadd -x -W -D "cn=admin,dc=nowlan,dc=com" -f user.ldif
```

- 给用户节点的用户ldapuser1,ldapuser2,ldapuser3设置密码123456

```shell
ldappasswd -x -D "cn=admin,dc=nowlan,dc=com" -w Hello "uid=ldapuser1,ou=people,dc=nowlan,dc=com" -s 123456
ldappasswd -x -D "cn=admin,dc=nowlan,dc=com" -w Hello "uid=ldapuser2,ou=people,dc=nowlan,dc=com" -s 123456
ldappasswd -x -D "cn=admin,dc=nowlan,dc=com" -w Hello "uid=ldapuser3,ou=people,dc=nowlan,dc=com" -s 123456
```

> 如果只需要几条数据，则可以使用上面导入数据的方式，简单方便。如果需要导入大量数据，可以使用工具migrationtools。

# 3. 使用ApacheDirectoryStudio连接OpenLDAP查看数据
- 打开Apache Directory Studio，在菜单 LDAP 选择 New Connection
- Connection name 配置为 nowlan.com；Hostname配置为OpenLDAP的服务器地址，Port默认是389，然后点击 Check Network Parameter 查看配置是否正确。如果配置正确，则点击 Next。
- Bind DN or user 输入 cn=admin,dc=nowlan,dc=com,Bind password输入Hello。这里的配置都是根据执行sudo dpkg-reconfigure slapd时设置的。然后点击 Check Authentication，如果successful，则点击 Next。
- 下个页面没有需要配置的内容，直接点击 Next
- 在最后一个页面点击 Finish。
- 在ou=people,dc=nowlan,dc=com节点中可以查看到添加的用户
- 单击一个用户节点，页面右侧就会显示用户的属性信息
- 在属性userPassword上双击，弹出一个页面，在Verify Password处输入密码，然后点击 Verify可以查看用户密码是否正确。


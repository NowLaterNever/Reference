# 1. 安装mysql相关软件
- 因为要将认证信息存在数据库Mysql中，所以必须安装如下三个软件：

```cpp
sudo apt-get install mysql-server
sudo apt-get install mysql-client
sudo apt-get install libmysqlclient-dev
```

# 2. 安装freeradius的依赖包

```cpp
sudo apt-get install libtalloc-dev
sudo apt-get install libssl-dev
sudo apt-get install openssl
```

# 3. 安装freeradius
首先从官网下载安装文件freeradius-server-3.0.15.tar.gz，然后三步曲安装freeradius

```cpp
tar zxvf freeradius-server-3.0.15.tar.gz  
cd freeradius-server-3.0.15  
./configure 
make
sudo make install
```

# 4. 配置freeradius
```cpp
cd /usr/local/etc/raddb 
vim radiusd.conf
找到allow_vulnerable_openssl = no,修改成allow_vulnerable_openssl = yes
```

> 普通用户是无权进入这个目录的，请切换到root用户或者sudo chmod 755 -R/usr/local/etc/raddb 。因为freeradius毕竟存的是用户认证信息，普通用户无权进入很正常。

# 5. freeradius说明
> 调试的命令为：sudo radiusd -X 这个命令启动radiusd服务器 
测试命令是 sudo radtest Username Password ServerIP Port Secret 
测试的时候，开两个终端，一个终端是服务器，另一个是客户机 
如果出现Access Accept的提示，测试成功！

# 6. 测试用户认证信息在文件中的情况
freeradius默认不是用数据库的，用户认证信息保存在users文件中。vim users  找到如下位置，将注释去掉即可
```cpp
 steve  Cleartext-Password := "testing"
       Service-Type = Framed-User,
       Framed-Protocol = PPP,
       Framed-IP-Address = 172.16.3.33,
       Framed-IP-Netmask = 255.255.255.0,
       Framed-Routing = Broadcast-Listen,
       Framed-Filter-Id = "std.ppp",
       Framed-MTU = 1500,
       Framed-Compression = Van-Jacobsen-TCP-IP
```
>这里将注释去掉相当于在users增加了一条认证信息。用户名为steve 密码为testing ，其他信息属于附带信息。当然你也可以自己在users里添加其他用户信息。比如：dog  Cleartext-Password := "cat"

- 开启认证服务器

```cpp
sudo radiusd -X   修改users后，必须重启才能生效
```

- 测试认证信息

```cpp
echo "User-Name=steve,User-Password=testing" | radclient 127.0.0.1:1812 auth testing123 -x     这里是测试，出现Access Accept表示认证通过
```

# 7. 测试认证信息保存在mysql的情况
创建数据库，输入命令mysql -u root -p要求输入密码时，直接回车即可。
```cpp
mysql>create database radius;
mysql>exit;
```

- 导入数据表(即使手动创建，也应该是名字为radcheck这样的表，只有这样的表名才能被radius使用)

```cpp
mysql -u root radius </usr/local/etc/raddb/mods-config/sql/main/mysql/schema.sql -p
```

- 插入一条用于测试认证的数据记录

```cpp
insert into radcheck(username,attribute,value,op) values('test','Cleartext-Password','test123',':=');
```

- 创建软链接，表示支持sql(从sql中获取username/passwd,然后进行认证)

```cpp
cd /usr/local/etc/raddb/mods-enabled/
ln -s ../mods-available/sql
```

- 修改 FreeRADIUS中的mysql 配置文件

```cpp
vim /usr/local/etc/raddb/mods-available/sql
1. 找到driver = “rlm_sql_null”这一行，修改为driver = “rlm_sql_mysql”。
2. 找到如下位置，取消注释，
    server = "localhost"
    port = 3306
    login = "root"
    password = "Hello"
```
> radiusd启动的时候需要使用上面的信息连接到mysql。server是localhost，端口是3306，使用的用户名密码是root/Hello。也可以新创建一个用户比如radius，设置密码，然后赋予相应的权限。

- 测试认证信息

```cpp
sudo radiusd -X  修改配置后必须重启才能生效
echo "User-Name=test,User-Password=test123" | radclient 127.0.0.1:1812 auth testing123 -x     这里是测试，出现Access Accept表示认证通过
```

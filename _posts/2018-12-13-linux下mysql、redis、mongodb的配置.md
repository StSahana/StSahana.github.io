# linux下mysql、redis、mongodb的配置

## redis

```shell
tar -zxvf redis-3.2.5.tar.gz
# 移动到/usr/local下方便管理
mv redis-3.2.5 /usr/local/
cd /usr/local/redis-3.2.5/
# 编译前确保程序中安装了gcc
# redis默认使用jemalloc，在安装了jemalloc的情况下可以直接运行make命令，没安装的情况下
make MALLOC=libc
# 如果要远程访问redis，必须要修改redis.conf
>>>>># bind 127.0.0.1 //注释掉该行,也可以指定一个ip，仅此ip可连接redis
>>>>>protected-mode no
# 启动程序
# ./redis-server: 默认启动方式，使用默认配置，可以指定配置文件
./src/redis-server redis.conf
# 详细配置说明见http://www.runoob.com/redis/redis-conf.html
# 本机关闭redis命令
./src/redis-cli -h 127.0.0.1 -p 6379 shutdown
```

## mongodb

```shell
# 解压移动
tar -zxvf mongodb-linux-x86_64-3.2.10.tgz
mv mongodb-linux-x86_64-3.2.10/ /usr/local/mongodb
# 创建数据存文件夹及log文件
cd /usr/local/mongodb/
mkdir data
mkdir log
cd log
touch mongo.log
# 相比于在命令行设置参数，在配置文档中设置参数更便于后期的维护
cd /usr/local/mongodb/bin/
touch mongo.conf
vi mongo.conf
```

mongo.conf

```properties
logpath=/usr/local/mongodb/log/mongo.log
logappend=true
dbpath=/usr/local/mongodb/data
```

启动命令

```shell
./mongod --config mongo.conf --fork
```

## mysql

```shell
# 解压移动
tar -zxvf mysql-5.7.16-linux-glibc2.5-x86_64.tar.gz
mv mysql-5.7.16-linux-glibc2.5-x86_64/ /usr/local/mysql
cd /usr/local/mysql
# 创建msyql用户和组
groupadd mysql
useradd -g mysql mysql
passwd mysql #输入新密码
# 执行初始化,basedir为安装位置，datadir为数据存放位置，初始化完成后会为root用户生成随机密码，密码一定要保存好，下面修改密码时要用到
./bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data --explicit_defaults_for_timestamp
# 修改配置文档,找到basedir和datadir一行，进行修改
vi ./support-files/mysql.server
# 修改完成后，启动mysql
./support-files/mysql.server start
# 若显示SUCCESS就说明启动成功了，下面对root用户进行密码修改,使用root用户登录到mysql数据库
# 关闭数据库
./support-files/mysql.server stop
# 安全模式登陆
./bin/mysqld_safe --user=mysql --skip-grant-tables --skip-networking &
# 执行完上一条记录后，需要另打开一个命令行进行输入
./bin/mysql -u root -p mysql
# 输入密码时，密码就是初始化时系统生成的随机密码，修改user表,同时修改host字段使所有主机可登陆
>>update user set authentication_string=PASSWORD("newPassword"),host='%'  where user="root" ；
>>flush privileges; 
>>exit
# 重启
./support-files/mysql.server restart
```
Good Luck！

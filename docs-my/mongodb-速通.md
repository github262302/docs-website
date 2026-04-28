# Mongodb Ubuntu 24 速通

## install

```bash
# 装密钥 安装用
wget -qO - https://www.mongodb.org/static/pgp/server-8.0.asc | gpg -o /etc/apt/keyrings/mongodb-server-8.0.gpg --dearmor

# 写安装源 不然不知道去哪里找安装包
# write to /etc/apt/sources.list.d/mongodb.list
deb [signed-by=/etc/apt/keyrings/mongodb-server-8.0.gpg] https://mirrors.tuna.tsinghua.edu.cn/mongodb/apt/ubuntu noble/mongodb-org/8.0 multiverse



apt-get update
apt-get install -y mongodb-org

```


## createUser default
> 链接数据库需要的账户
```js
// root 代表超級管理员权限 admin代表给admin数据库加的超级管理员
db.createUser({
    
    //用户名
    user:'admin',
    //密码    
    pwd:'123456',
    roles:[{ 'role':'root','db':'admin'}] })

```

>example link

mongodb://admin:123456@0.0.0.0:56666/anyDatabase?authSource=admin



## config

```yml

# mongod.conf

# for documentation of all options, see:
#   http://docs.mongodb.org/manual/reference/configuration-options/

# Where and how to store data.
storage:
  dbPath: /var/lib/mongodb
#  engine:
#  wiredTiger:

# where to write logging data.
systemLog:
  destination: file
  logAppend: true
  path: /var/log/mongodb/mongod.log

# network interfaces
net:
  #changed
  port: 56666
  #changed
  bindIp: 0.0.0.0


# how the process runs
processManagement:
  timeZoneInfo: /usr/share/zoneinfo

security:
  authorization: enabled
#operationProfiling:

#replication:

#sharding:

## Enterprise-Only Options:

#auditLog:

```

## command

```bash

systemctl restart mongod
systemctl enable mongod
systemctl disable mongod
systemctl start mongod


```


## info

> mongodb数据库角色
1、数据库用户角色： read、readWrite
2、数据库管理角色：dbAdmin、dbOwner、userAdmin
3、集群管理角色：clusterAdmin、clusterManager、clusterMonitor、hostManager
4、备份恢复角色：backup、restore
5、所有数据库角色：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase
6、超级用户角色：root
7、内部角色：__system
8、内建的角色

> 角色说明：
read：允许用户读取指定数据库
readWrite：允许用户读写指定数据库
dbAdmin：允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访问system.profile
userAdmin：允许用户向system.users集合写入，可以找指定数据库里创建、删除和管理用户
clusterAdmin：只在admin数据库中可用，赋予用户所有分片和复制集相关函数的管理权限。
readAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读权限
readWriteAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读写权限
userAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的userAdmin权限
dbAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的dbAdmin权限。
root：只在admin数据库中可用。超级账号，超级权限
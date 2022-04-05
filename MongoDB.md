# MongoDB🌿

MongoDB 是由C++语言编写的，是一个基于分布式文件存储的开源数据库系统。



## 参考文档

👉  [MongoDB 官方文档](https://docs.mongodb.com/v4.4/)



## 安装

1. 安装mongodb

   ```shell
   # 安装 mongodb，安装后会自动启动
   sudo apt install mongodb
   
   # 查看进程是否已经启动 
   pgrep -l mongo  
   ```


2. 创建用户

   mongodb 安装完成会自动运行，默认是没有密码的，先进入mongodb中创建一个管理员用户。

   ```shell
   use admin
   db.createUser({user:'root',pwd:'123456',roles:[{role:"userAdminAnyDatabase",db:"admin"}]})
   ```

   ```
   db.createUser({user:'root',pwd:'123456',roles:[{role:"root",db:"admin"}]})
   ```

   

3. 修改配置文件

   ```sh
   vim /etc/mongodb.conf
   
   bind_ip = 0.0.0.0    						 # 监听所有IP
   port = 27017                       			 # 实例运行在27017端口（默认）
   dbpath = /usr/local/mongodb/db      	     # 数据文件夹存放地址（db要预先创建）
   logpath = /usr/local/mongodb/logs/27017.log  # 日志文件地址
   logappend = false                   		 # 启动时添加还是重写日志文件
   fork = true                         		 # 是否后台运行
   auth = true                         		 # 开启校验用户
   # bind_ip_all = true                 		 # 任何机器可以连接
   ```

   

4. 关闭mongodb

   修改了配置之后需要重新运行mongodb
   
   ```sh
   # way1 强制关闭
   $ ps -e|grep mongo   # 查看mongodb的pid
   $ kill -9 pid
   # way2 
   use admin;
   - db.runCommand("shutdown");
   - db.shutdownServer();
   ```
   
   

5. 开启mongodb

   如果是系统非正常关闭，启动可能会报错，由于 mongodb 自动被锁上了，这是需要进入mongodb数据库文件所在的目录 `/var/lib/mongodb` ,删除目录中的 `mongodb.lock` 文件,然后再进行上述操作

   ```sh
   $ mongod --config /etc/mongodb.conf
   ```



## 文件位置

可执行文件：`/usr/bin`

数据库文件地址：`/var/lib/mongodb`

数据库 log 日志文件的位置：`/var/log/mongodb/mongodb.log`



## 优缺点分析

https://blog.csdn.net/yanpenglei/article/details/79261875





# shell命令

## mongod

mongod 命令用于启动

```sh
mongod --dbpath /var/lib/mongodb/ --logpath /var/log/mongodb/mongodb.log --logappend &

    --dbpath：指定 mongo 的数据库文件在哪个文件夹
    --logpath：指定 mongo 的日志文件，一定要指定到具体的文件名
    --logappend：表示日志的写入是采用附加的方式，默认的是覆盖之前的文件
```



## mongo

mongo 命令用于连接数据库服务，可以通过端口进行访问（27017）



# 相关概念



## 数据库基本概念

| 关系数据库         | MongoDB                                   |
| ------------------ | ----------------------------------------- |
| 数据库  database   | 数据库  database                          |
| 表格 table         | 集合 collection                           |
| 行 row             | 文档  document                            |
| 列 column          | 字段 field                                |
| 索引 index         | 索引 index                                |
| 表联合 table joins | 嵌入文档                                  |
| 主键 primary key   | 主键 primary key 。MongoDB 默认主键为 _id |

 

## 默认数据库

一个 MongoDB 中可以建立多个数据库，这些数据库是相互独立的，有自己的集合和权限。不同的数据库使用不同的文件存储。

MongoDB 默认有 3 个数据库：

- admin： 从权限的角度来看，这是"root" 数据库。将一个用户添加到这个数据库，这个用户会自动继承所有数据库的权限。一些特定的服务器端命令也只能在这个数据库中运行，比如列出所有的数据库或者关闭服务器。

- local：这个数据库永远不会被复制，里面的数据都是本地的，不会复制到其他 MongoDB 服务器上，可以用来存储限于本地单台服务器的任意集合。

- config：当 MongoDB 用于分片设置时，config 数据库在内部使用，用于保存分片的相关信息。

  





# 字段🌿 

## ObjectId

MongoDB 默认使用 _id 字段作为主键，类型为 ObjectId，ObjectId的生成有一定的规则。

ObjectId 使用 12 字节的存储空间，是一个由 24 个 16 进制数字组成的字符串（每个字节可以存储两个16进制数字）

```
571094e2976aeb1df982ad4e

57  10  94  e2  |  97  6a  eb  |  1d  f9  |  82  ad  4e
标准纪元的时间戳     主机唯一标识符   进程标识符   自增加计数器
```



- 1 - 4 的字节是从标准纪元开始的时间戳，单位为秒。
  - 时间戳在前，这意味着 ObjectId 大致会按照插入的顺序排列。
  - 隐含了文档的创建时间

- 5 - 7 的字节是所在主机的唯一标识符
  - 通常是机器主机名的散列值
  - 确保不同主机生成不同的 ObjectId，不产生冲突。
- 8 - 9 的字节来自产生 ObjectId 的进程的进程标识符（PID）
  - 前 9 个字节保证了同一秒钟不同机器不同进程产生的 ObjectId 是唯一的。

- 10 - 12 个字节是一个自动增加的计数器
  - 确保相同进程同一秒产生的 ObjectId 也是不一样的。



# 用户与权限🌿

## 权限列表

### 数据库用户角色

- `read`： 只读数据权限
- `readWrite`：读写数据权限



### 数据库管理角色

- `dbAdmin`： 允许在当前 db 中执行管理操作的权限
- `userADmin`： 允许在当前 db 中创建、修改权限与用户
- `dbOwner`： 允许在当前 db 中执行任意操作，拥有  `readWrite` 、 `dbAdmin` 、 `userAdmin` 的权限



### 集群管理角色

- `clusterAdmin`： 管理机器的最高权限
- `clusterManager`： 管理和监控集群的权限
- `clusterMonitor`： 监控集群的权限
- `hostManager`： 管理 Server



### 运维角色

- `backup`
- `restore`



### 跨库角色

- `readAnyDatabase`： 在所有数据库上都有读取数据的权限
- `readWriteAnyDatabase`： 在所有数据库上都有读写数据的权限
- `userAdminAnyDatabase`： 在所有数据库上都有管理 user 的权限
- `dbAdminAnyDatabase`： 管理所有数据库的权限



### 超级权限

yyds：

- `root`： 超级用户，拥有以上所有权限

间接获得超级权限：

- `dbOwner`：当范围限定为 admin 时
- `userAdmin`：当范围限定为 admin 时
- `userAdminAnyDatabase`





# Mongo Shell🌿

## 参考文档

👉  [官方文档](https://docs.mongodb.com/manual/reference/method/)



## 集合操作



##  数据库操作

```shell
show dbs 				 # 显示所有db信息
db.dropDatabase()   	 # 删除db
db.stats()       		 # 显示当前db状态
db.collection.stats()    # 显示集合状态
db.version()     		 # 当前db版本
show collections 		 # 显示当前db中所有集合
db.collection.drop()  	 # 删除集合
db.getName() 			 # 获取当前db名
db.getCollectionNames()  # 获取当前db中所有集合
```







## 用户管理操作

```shell
# 创建用户
db.createUser(
 {
   user: "root",
   pwd: "123456",
   roles: [ { role: "root", db: "admin"} ]
   }
)
   
# 用户认证
db.autu("root","123456") 

# 查询用户
db.system.users.find().pretty()

# 删除用户
db.dropUser("username");

# 赋予用户权限
db.grantRolesToUser( "<username>", [ <roles> ], { <writeConcern> } )

db.grantRolesToUser(
   "superkuang",
   [ { role: "dbOwner", db: "admin" } ]
)

# 移除用户权限
db.revokeRolesFromUser( "<username>", [ <roles> ], { <writeConcern> } )

db.revokeRolesFromUser(
   "superkuang",
   [ { role: "userAdminAnyDatabase", db: "admin" } ]
)
```





## 权限管理操作

```sh

```


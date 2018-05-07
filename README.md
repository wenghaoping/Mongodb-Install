# MongDB在windows和Linux上的安装与使用

**主要是这个坑最大,所以先写,怕我忘记了啊**

##  一、mongodb在Linux上的安装说明##  ##

1.下载安装包

```
wget http://fastdl.mongodb.org/linux/mongodb-linux-x86_64-amazon-3.4.9.tgz
```


或者[http://www.mongodb.org/downloads ](http://note.youdao.com/)

#### 1. 下载完成后解压缩压缩包

```
tar zxf mongodb-linux-x86_64-amazon-3.4.9.tgz
```


### 2. 安装准备
将mongodb移动到/usr/local/mongdb文件夹

```
mv mongodb-linux-x86_64-amazon-3.4.9.tgz /usr/local/mongodb
```


创建数据库文件夹与日志文件以及配置


```
用来放数据
mkdir /usr/local/mongodb/data  
```
```
用来放日志,日志会很庞大的
mkdir /usr/local/mongodb/logs
cd logs
touch mongo.log
```
```
用来放配置
mkdir /usr/local/mongodb/etc  
cd etc
touch mongo.conf

配置文件如下

#数据库数据存放目录
dbpath=/usr/local/mongodb/data
#数据库日志存放目录
logpath=c/usr/local/mongodb/logs/mongo.log 
#以追加的方式记录日志
logappend = true
#端口号 默认为27017
port=27017 
#开启用户认证
#auth=true
#关闭http接口，默认关闭http端口访问
nohttpinterface=true
#启用日志文件，默认启用
journal=true 
#这个选项可以过滤掉一些无用的日志信息，若需要调试使用请设置为false
quiet=true 
```

保存退出



上面有个神坑,就是==开启用户认证==,    
这个东西第一次被坑的直接就我没有权限登陆了,
第一次先不要用配置文件登陆,先手动登陆,设置账号密码之后,再用配置登陆,切记,切记啊    
先启动非授权的模式


### 3. 启动Mongodb,并且设置用户名密码

1.  先进入bin文件夹
```
cd /usr/local/mongodb/bin
使用配置文件启动数据库 (记住,这里是没有使用用户认证启动的)

./mongod --config /usr/local/mongodb/etc/mongo.conf --fork

这时候新建一个窗口,数据库已经启动了
```

2. 进入数据库

```
输入 ./mongo   进入数据库
[root@wenghaoping bin]# ./mongo
MongoDB shell version v3.4.9
connecting to: mongodb://127.0.0.1:27017
MongoDB server version: 3.4.9
> 

已成功进入数据库,
```

3.设置用户名密码


```
//显示所有
> show dbs   
admin     0.000GB
blog      0.001GB
local     0.000GB
shuoshuo  0.000GB

//进入admin db
> use admin
switched to db admin


//设置用户名和密码
>db.createUser({user:"admin",pwd:"123456",roles:["root"]});

//返回这个即代表,设置用户名成功
Sucessfully added user: {"user" : "admin", "roles" : ["root"]}

//用户认证
>db.auth("admin" , "123456")

返回1,则代表认证成功

```

4.给所需要的数据库,设置账号密码,以test数据库为例


```
> use test
switched to db test


//dbOwner为角色 db为可以使用的数据库名称
> db.createUser({user:"test",pwd:"123456",roles:[{role : "dbOwner",db : "test"}]});
//成功
Successfully added user: {
        "user" : "test",
        "roles" : [
                {
                        "role" : "dbOwner",
                        "db" : "test"
                }
        ]
}
```

5. 重新启动数据库,以认证方式启动


```
把配置文件中的auth=true前面的#去处,重新保存,然后再一次以配置启动数据库,方法参考上面
```


启动后,在用可视化软件去连接数据库,已经需要指定数据库和密码了,输入即可连接


### 4. 设置开机自启动
先关闭数据库,

将mongodb启动项目追加入rc.local保证mongodb在服务器开机时启动 

```
vi /etc/rc.local
```

使用vi编辑器打开配置文件，并在其中加入下面一行代码

```
/usr/local/mongodb/bin/mongod -dbpath=/usr/local/mongodb/data --fork --port 27017 --logpath=/usr/local/mongodb/log/mongo.log --logappend --auth
```

或者直接输入一下语句,用法与上面一样
```
echo "/usr/local/mongodb/bin/mongod --dbpath=/usr/local/mongodb/data –logpath=/usr/local/mongodb/logs/mongo.log –logappend --auth –port=27017" >> /etc/rc.local
```


### 5. 再一次使用配置启动mongodb


```
/usr/local/mongodb/bin/mongod --config /usr/local/mongodb/mongodb/etc/mongo.conf --fork
```

### 6. 大功告成
你会发现,现在你关闭shell数据库也依然启动这了

这一段主要的问题就是,先在非认证情况下建立账号密码,然后在在认证情况下,进入数据库,需要输入账号密码





## 二 windows下的mongodb的安装

windows的就简单很多啦

### 1.下载安装包

[https://www.mongodb.com/download-center?jmp=nav#community](http://note.youdao.com/)

自己先下载,然后就是傻瓜式的安装,一直下一步下一步,就好了

别忘了设置环境变量


### 2.与Linux一样,创建数据,日志,以及配置文件目录,最好在c盘下创建,


```
C:\MongoDB\data
C:\MongoDB\logs\mongo.log
C:\MongoDB\etc\mongo.conf
```

配置文件的内容和Linux的相同,只需要把对应的路径改一下



```
#数据库数据存放目录
dbpath=c:\MongoDB\data
#数据库日志存放目录
logpath=c:\MongoDB\logs\mongo.log 
#以追加的方式记录日志
logappend = true
#端口号 默认为27017
port=27017 
#开启用户认证
#auth=true
#关闭http接口，默认关闭http端口访问
nohttpinterface=true
#启用日志文件，默认启用
journal=true 
#这个选项可以过滤掉一些无用的日志信息，若需要调试使用请设置为false
quiet=true
```

然后直接使用配置文件启动数据库

```
mongod --config C:\MongoDB\etc\mongo.conf
```

再启动一个窗口

输入
```
monggo
```

就进入数据库啦,其他与Linux一毛一样,

简单吧

简单吧

但是我搞了好久好久啊,唉,





















### 配置MongoDB #

- 下载MongoDB之后，在C盘创建一个MongoDB文件夹，创建data（存放数据）、logs（存放日志）、etc（存放配置文件）文件夹。

- 进入MongoDB下载的路径的bin文件夹：一般为C:\Program Files\MongoDB\Server\3.4\bin

传参方式配置：

- 输入	 mongod --dbpath C:\MongoDB\data

- 此时最后出现 waiting for connections on port 27017 代表成功啦~~

- 在浏览器输入http://localhost:27017/可以看到显示信息为：It looks like you are trying to access MongoDB over HTTP on the native driver port.

- 正式成功！配置成功~

以上是为传参方式配置。也可直接在etc文件夹下创建mongo.conf文件进行配置。直接运行该文件即可。

- 打开mongo.conf文件，修改如下：

		#数据库路径
		dbpath=c:\MongoDB\data\
		#日志输出文件路径
		logpath=c:\MongoDB\logs\mongo.log
		#错误日志采用追加模式，配置这个选项后mongodb的日志会追加到现有的日志文件，而不是从新创建一个新文件
		logappend=true
		#启用日志文件，默认启用
		journal=true
		#这个选项可以过滤掉一些无用的日志信息，若需要调试使用请设置为false
		quiet=true
		#端口号 默认为27017
		port=27017

- 因为27017是客户端访问数据库的，但是如果想网页访问则要添加设置。该设置是启用http的服务来给网页进行监听。 

		--httpinterface

- 然后在C:\Program Files\MongoDB\Server\3.4\bin路径下输入：

		mongod --config C:\MongoDB\etc\mongo.conf --httpinterface

在http://localhost:28017可以查看到。

- 也可直接在配置上加上：

		#http服务
		httpinterface=true


第三种配置方法：安装到服务上~~

- 在运行输入 service 选择本地服务。
- mongod --config C:\MongoDB\etc\mongo.conf --install --serviceName "MongoDB"
- 可在本地服务看见MongoDB
- 在左上角启动~
- 在bin目录输入mongo 
- 输入 show dbs  即可看到运行了~~~


### MongoVUE #

	#创建数据库
	use HappyMall 

	#增加数据：

	> db.goods.insert({id:101,"name":"iphone6","salePrice":5280})

	#删除数据：
	db.goods.remove({"name":"iphone6"})

### MongDB创建用户  #

	1. 创建管理员
	2. 授权认证
	3. 给使用的数据库添加用户

在非授权：

	#不授权
	mongod --config c:\MongoDB\etc\mongo.conf
	
	#创建admin数据库
	use admin

	#为admin数据库创建帐号
	db.createUser({user:"admin",pwd:"admin",roles:["root"]})

	#先关了服务
	mongod --config c:\MongoDB\etc\mongo.conf --auth

	#查看admin
	use admin

	show collections

	#出错~~~

	#认证
	db.auth("admin","admin")

	#查看admin
	use admin

	show collections

	#成功~~~~

	#切换test数据库：
	use test1

	#创建用户
	db.createUser({user:"root",pwd:"123456",roles:[{role:"dbOwner",db:"test1"}]})

	#先关了服务
	mongod --config c:\MongoDB\etc\mongo.conf
	#此时不能访问collection


	#查看test1
	use test1

	show collections

	#出错~~~

	#认证
	db.auth("root","123456")

	#查看test1
	use test1

	show collections

	#成功~~~~

	#admin可以查看所有的数据库，而test1是绑定了test1，只可以查看test1
	

	我改了

	> use admin 
	switched to db admin 
	> var s= db.system.version.findOne({"_id" : "authSchema"}) 
	> s.currentVersion = 3 
	3 
	> db.system.version.save(s) 
	WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 }) 
	
	这个时候验证方式就修改好了，然后再创建的用户的验证方式就变成3了
	
	>use admin
	>db.createUser({user:"admin",pwd:"admin",roles:[{role:"root",db:"admin"}]}) 
	 
	最后，以auth启动服务，就能用旧驱动验证了
	D:
	cd D:\MongoDB\Server\3.2\bin
	mongod --dbpath D:\mongodb\data -auth




> ###MongoDB 基本语法

#####查看数据库
	show dbs
	
#####创建数据库
	use demo

#####创建集合（创建表）
	方法一：创建集合user
	db.createCollection("user")

	方法二：创建集合users
	db.users.insert({id:123})
 
#####查看表
	> show collections
	system.indexes
	user
	users

#####删除数据库demo
	db.dropDatabase()
	
	
#####删除demo的user表
	db.user.drop()

#####增加数据：
	db.user.insert({userId:101,userName:'jack',userAge:12,class:{name:'classa',num:10}})

#####查看全部数据：
	 db.user.find()
	 #object显示
	 db.user.find().pretty()

#####查看第一条数据：
	 db.user.findOne()

#####更新数据：（$set:{}）
	 db.user.update({userName:'jack'},{$set:{userAge:30}})
#####更新子数据：（class.name）
	 db.user.update({userName:'jack'},{$set:{"class.name":"test"}})
#####查询数据：
	 db.user.find({userName:"jack"})
	 db.user.find({"class.name":"test"})
#####条件查询数据：
	 #大于$gt
	 db.user.find({userAge:{$gt:20}})
	 #小于$lt
     db.user.find({userAge:{$lt:20}})
	 #等于$eq
	 db.user.find({userAge:{$eq:28}})
     #大于或等于
	 db.user.find({userAge:{$gte:28}})
	 #小于或等于
	 db.user.find({userAge:{$lte:28}})
#####删除数据：
	 db.user.remove({userName:"jack"})

> ###数据库的插入 #

- 可直接在命令行插入。
- 可在mongo客户端import文件
- 可在命令行：
	mongoimport -d db_demo -c users --file c:\......
	 

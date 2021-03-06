# mongo-sync-elastic 使用说明
## 注：bin目录下的：mongo-sync-elastic是linux的可执行文件， mongo-sync-elastic.exe是windows的可执行文件

## 1.快速开始
* linux: bin/mongo-sync-elastic -f config.json
* windows：bin/mongo-sync-elastic.exe -f config.json

## 2.配置文件 config.json 内容如下
```
{
  "mongodb": "mydb",
  "mongocoll": "mycoll",
  "mongodburl": "mongodb://wpsroot:wpspwd@localhost:27017",
  "esurl": "http://127.0.0.1:9200",
  "tspath": "./"
}
```
### 参数说明:
* mongodb: 数据库名字
* mongocoll: 集合名字
* mongodburl: 连接数据库的url
* esurl: 连接es的url
* tspath: 非必须参数。用于服务意外停止做数据恢复的，或断电续传使用 (默认是程序执行所在的路径下oplogts文件夹保存同步状态)

## 3.tspath参数的作用
* 当已经完成全量同步的时候，程序会在tspath路径下创建 oplogts/mydb_mycoll_latestoplog.log 文件，纪录下时间节点，意味着在该时间节点之前的数据都已完成同步，但当全量同步失败不会创建该文件
* 每隔1个小时就会更新 mydb_mycoll_latestoplog.log 文件里面的时间节点
* 当服务意外停止时，并且不愿意再进行一次全量同步，只需同步服务停止之后还没同步的数据，则服务再次启动时tspath不能改变，让数据从tspath中恢复
* 当服务意外停止时，并希望从0开始重新同步一次，则可以吧tspath下面删除对应的log文件 或 重新选择一个tspath即可


### 备注:
* 1.启动服务的用户需要拥有参数tspath路径下文件的创建查看删除权限
* 2.参数mongodburl中的mongodb用户需要拥有admin库下的oplog.rs查询权限
* 3.在es中创建的索引名字是 mongodb+'.'+mongocoll，即: mydb.mycoll

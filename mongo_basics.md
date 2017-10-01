---
title: Mongo Basic
date: 2017-09-03 9:10:34
tags: MongoDB
categories: 前端开发
---
# MongoDB
## 基本配置
下载 MongoDB, 解压后并启动:
```code
$ bin/mongod
```
MongoDB 默认存储数据目录为 /data/db/ (或者 c:\data\db), 当然你也可以修改成不同目录, 只需要指定 –dbpath 参数:
```code
$ bin/mongod --dbpath /path/to/my/data/dir
```
启动 MongoDB JavaScript 工具来操作数据库:
```code
$ bin/mongo
```
添加mongo-hacker，更方便的使用mongo
```code
npm install -g mongo-hacker
```
<!-- more -->

## 基本概念
- NoSQL意思是“Not Only SQL”
- NoSQL databases store data
in a multitude of formats and are generally less bound to schema controls
- Mongo does not have a schema, it makes development really fast.
Additionally, changing andevolving projects is easy without a schema to migrate.
- Mongo的查询语句非常简单。实际上，它更像一种编程语言。
- The core of Mongo's data model is documents.
Documents are unique records in Mongo, and they are a lot like JSON objects.

## documents and collection
在mongo里，documents是一份个人记录。collection是一张表格，每份documents像是表格上的一行。

因为documents是 individual record，所以严格意义上，他不是关系型数据库上的一行，documents不一定含有所有的列(field)。在关系型数据库中，你必须要先定义好模式（schema），每一列都有特定的类型，而Mongo完全不需要管哪些缺少的值。每个documents的每一个字段都是独立的。

## [Why ascending or descending indexes matter in Mongo](http://stackoverflow.com/questions/10329104/why-does-direction-of-index-matter-in-mongodb)
重点在于使用复合键（compound key）的时候，查询方向和key的排序不一致，导致需要跳转索引，是速度变慢。

> The db.colleciton.createIndex method documentation: https://docs.mongodb.org/v3.0/reference/method/db.collection.createIndex/#db.collection.createIndex


## Querying
```
 db.posts.find({$or:[{title: "I love the holidays"},{title: "How to workout"}]},{title: true});
```
```
Object.keys(db.collection.find());
```
> Mongo’s query operators - https://docs.mongodb.org/v3.0/reference/operator/query/

- Map reduce pattern to find all keys in a collection: http://stackoverflow.com/questions/2298870/mongodb-get-names-of-all-keys-in-collection

- Pagination formula: limit = number of records on each page, skip = number of records on each page * page number - 1

	So, with 5 results per page:
	```
	page 1: limit = 5, skip = 0
	page 2: limit = 5, skip = 5
	page 3: limit = 5, skip = 10
	etc...
	```

## Updating Data
```mongo
db.collection.update({author: ObjectId("56334t43532csd")},{$set: {tags: ['javascript', 'front end'],title: "Update Title"}})
```

> mongo-hacker: https://github.com/TylerBrock/mongo-hacker
>
> Mongo’s update modifiers: https://docs.mongodb.org/v3.0/reference/operator/update/

## Language Drivers and Sharding
MongoDB Drivers: https://docs.mongodb.org/ecosystem/drivers/
Sharding in Mongo: https://docs.mongodb.org/manual/sharding/

---
title: 'NodeBB 3: mongodb 与 redis'
date: 2016-10-27 21:29:18
tags:
    - Nodejs
    - NodeBB
    - V2MM
    - 数据库
---

前面说过，[NodeBB] 支持 mongodb 或 redis 作为存储数据库。这种使用 NoSQL 数据库的做法我很欣赏，有时候写插件要给数据库添加某个字段的时候非常方便，直接加就行。比如给 Topic 添加一个 externalLink 属性。

其实 [NodeBB] 刚开始 0.x 版的时候还只支持 Redis, 所以它的数据存储格式就这几种： hashes, sets, sortedsets and lists。后来经过某个版本的 Merge 后，依靠社区的力量终于支持 Mongodb 了， 有牛人用 Mongodb 来实现了  hashes, sets, sortedsets and list 这几种特殊的格式。 以至于现在 [NodeBB] 在数据库层的接口完全是一致的, Views 和 Controller 调用存储的时候完全不用管是 Redis 还是 Mongodb.

这是如何实现的呢？

查看源代码可以看到，在 `src/databases` 下有 mongo 和 redis 对于 hash, list, sets 和 sorted set 的各自实现，上层调用时使用它们统一抽象的接口名。比如 `setObject`，它们在 mongo 和 redis 下的实现是这样的：

```javascript

// from redis/hash.js
module.setObject = function(key, data, callback) {
        callback = callback || function() {};
        redisClient.hmset(key, data, function(err) {
            callback(err);
        });
    };

// from mongo/hash.js
module.setObject = function(key, data, callback) {
        callback = callback || helpers.noop;
        if (!key) {
            return callback();
        }

        db.collection('objects').update({_key: key}, {$set: data}, {upsert: true, w: 1}, function(err) {
            callback(err);
        });
    };
```

上层调用数据库的时候无需区分使用哪个 db：

```javascript
var db = require('./database');

db.setObject('user:' + uid, userData);
```

[NodeBB] 其实就是这样简单粗暴地检测调用哪个数据库的：

```
var nconf = require('nconf'),
    databaseType = nconf.get('database'),
    winston = require('winston');

if(!databaseType) {
    winston.info('Database type not set! Run node app --setup');
    process.exit();
}

var db = require('./database/' + databaseType);

module.exports = db;
```

这样做对于 mongodb 的后果就是整个数据库看上去只有一张表叫 objects（除了服务器 session 和 search 索引用到了其他表），并通过 _key 来区分不同数据分类。
比如，这样查询某个用户表的信息：

```
> db.objects.find({_key:"user:100"}).pretty()
{
        "_id" : ObjectId("555b9afa65190fe2123df8de"),
        "_key" : "user:100",
        "username" : "KwVLmsBU",
        "userslug" : "kwvlmsbu",
        "email" : "KwVLmsBU@nodebb.org",
        "joindate" : 1432066810034,
        "picture" : "https://secure.gravatar.com/avatar/8e43da62c77de95de0a91b306fb9d658?size=128&default=identicon&rating=pg",
        "gravatarpicture" : "https://secure.gravatar.com/avatar/8e43da62c77de95de0a91b306fb9d658?size=128&default=identicon&rating=pg",
        "fullname" : "",
        "location" : "",
        "birthday" : "",
        "website" : "",
        "signature" : "",
        "uploadedpicture" : "",
        "profileviews" : 0,
        "reputation" : 0,
        "postcount" : 0,
        "topiccount" : 0,
        "lastposttime" : 0,
        "banned" : 0,
        "status" : "online",
        "uid" : 100
}
```
这是官网的例子，详请见 [NodeBB database structure](https://github.com/NodeBB/NodeBB/wiki/Database-Structure).

[NodeBB] 将很多需要统计或排序的字段存储在 sorted set 中，一般使用 value 表示排序的字段名，score 表示统计数字。
比如 `users:postcount` 就是一个 sorted set, 其中存储了每个用户对应的 post count, 其中 value 表示 uid, score 表示 post count. 如果你熟悉 redis 的话，应该知道这是什么意思。

再举个例子，我在做  [nodebb-plugin-sso-github2] 的时候，用到的数据库有 hash 类型的 `githubid:uid` 和 sorted set 类型的 `email:uid`， 各自保存了 github 和 user ，email 和 user 之间的对应关系。

此前我在创建 category 的时候，没注意将 category 的 slug 都设置成了中文，与 category name 同名。在 mongo 下可以这样更改（以我的数据库为例）：

    db.objects.update({_key: "category:10"}, {$set: {slug: "10/technique"}})


如你看到此文，还有更多的疑问或不同的见解， 欢迎来到 [v2mm] 上讨论。
[v2mm] 是我创建的一个自由职业者论坛，欢迎加入 [v2mm],  winter is coming.



[v2mm]: http://v2mm.tech
[github]: https://github.com/NodeBB/NodeBB
[NodeBB]: http://nodebb.org
[demo]: http://v2mm.tech
[文档]: https://docs.nodebb.org/en/latest/
[nodebb-plugin-sso-github2]: https://github.com/revir/nodebb-plugin-sso-github2

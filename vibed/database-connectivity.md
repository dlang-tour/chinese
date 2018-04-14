# Database connectivity 数据库连接

Vibe.d makes it easy to access databases
in your backend services. **MongoDB** and **Redis**
support come directly with vibe.d, other database
adaptors can be found on [code.dlang.org](https://code.dlang.org).
Vibe.d可以使你的后端服务轻松访问数据库。 **MongoDB**和**Redis**支持直接使用vibe.d，其他数据库适配器可以在[code.dlang.org](https://code.dlang.org)上找到。

### MongoDB

Access to MongoDB is modelled around the class
[`MongoClient`](http://vibed.org/api/vibe.db.mongo.client/MongoClient).
The implementation doesn't have external dependencies
and is implemented using vibe.d's asynchronous
sockets - no need to block if the connection
has some latency.
访问MongoDB在类[`MongoClient`](http://vibed.org/api/vibe.db.mongo.client/MongoClient)中进行了建模。
该实现没有外部依赖，并使用vibe.d的异步套接字实现 -存在延迟将不会阻塞连接。

    auto client = connectMongoDB("127.0.0.1");
    auto users = client.getCollection("users");
    users.insert(Bson("peter"));

### Redis

Redis support is implemented with vibe.d sockets as well
and doesn't have external dependencies. Central
to the implementation is the
[`RedisDatabase`](http://vibed.org/api/vibe.db.redis.redis/RedisDatabase)
class which allows to run commands against a Redis
server. There are also convenience wrappers available, like
[`RedisList`](http://vibed.org/api/vibe.db.redis.types/RedisList)
which allows to transparently access a list
stored in Redis.
Redis支持也是通过vibe.d套接字来实现的，而且没有外部依赖。
实现的核心是[`RedisDatabase`](http://vibed.org/api/vibe.db.redis.redis/RedisDatabase)类，它允许针对Redis服务器运行命令。
还有一些便利的封装可以使用，例如[`RedisList`]（http://vibed.org/api/vibe.db.redis.types/RedisList），它允许透明地访问存储在Redis中的列表。

### MySQL

MySQL support without external dependencies
to the official MySQL library can be
added using the
[mysql-native](http://code.dlang.org/packages/mysql-native)
project. It supports vibe.d non-blocking sockets
too.
没有外部依赖的官方MySQL库的MySQL支持可以使用[mysql-native](http://code.dlang.org/packages/mysql-native)项目添加。 它也支持vibe.d的非阻塞套接字。

### Postgresql

A full-featured Postgresql client is implemented
with the external module [dpq2](http://code.dlang.org/packages/dpq2)
that is based on the official *libpq* library.
It uses vibe.d's event system to implement
asynchronous behaviour.
一个全功能的Postgresql客户端可以通过基于官方*libpq*库的外部模块[dpq2](http://code.dlang.org/packages/dpq2)实现。
它使用vibe.d的事件系统来实现异步行为。

Another alternative for Postgresql is
[ddb](http://code.dlang.org/packages/ddb)
which implements a Postgresql client with
vibe.d sockets and no external dependencies.
对Postgresql的另一种选择是[ddb](http://code.dlang.org/packages/ddb)，它使用vibe.d套接字实现了一个Postgresql客户端，并且没有外部依赖。



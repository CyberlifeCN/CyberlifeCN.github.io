---
title: "CouchBase 安装篇"
date: 2018-07-26T10:19:55+08:00
url: "2018/07/26/couchbase-install"
categories: ["deploy"]
tags: ["linux","数据库"]
banner: "imgs/couchbase.jpg"
---

CouchBase，是MemBase与couchDB这两个NoSQL数据库的合并的产物，是一个分布式的面向文档的NoSQL数据库管理系统,即拥有CouchDB的简单和可靠以及Memcached的高性能。可以简单的理解为：

$$ CouchBase = Memcached + Mysql + MongoDB $$

<!--more-->
## 下载 CouchBase
进入[官方网站](https://www.couchbase.com)，找到下载链接。里面可以选择不同的版本 社区版 / 商业版，选择社区版，选择Mac的社区版。

## 安装 CouchBase
将下载的文件解压得到app文件，拖入到应用程序即可。点击应用里的Couchbase直接运行。
如果之前安装了 CouchBase Server，请执行
```
rm -rf ~/Library/Application\ Support/Couchbase
rm -rf ~/Library/Application\ Support/Membase
```

## 访问 CouchBase UI
我们搭建的是本地环境没有集群，所以访问http://localhost:8091/即可看到我们的CouchBase信息。
系统默认用户名/密码
```
Username: Administrator
Password: password.
```

## 创建 Bucket
在启动时会提示进入配置页面，自带的测试数据库可以选择性安装，以方便开始我们的demo.
总共有三个自带`buckets`
- beer-sample
- gamesim-sample
- travel-sample

## Couchbase和传统RMDB的对比
| Couchbase Server                                   | Relational databases |
|:--------------------------------------------------:| --------------------:|
| Buckets                                            | Databases            |
| Buckets or Items (with type designator attribute)  | Tables               |
| Items (key-value or document)	                     | Rows                 |
| Index	                                             | Index                |

## Query
这是我们最经常用到的功能，可以在此处输入类 SQL 语句(N1ql)进行数据的增删改查操作。
如果在安装设置的时候已经配置安装了自带数据库 beer-sample,则可以在 CouchBase UI 的 Query 页面执行以下指令
```
SELECT brewery_id, name FROM `beer-sample`
     WHERE brewery_id IS NOT MISSING AND type="beer" LIMIT 5;
```

## Java 调用Couchbase
首先在 Eclipse 里创建一个Maven项目。 在项目的根目录下的pom.xml里增加jar包依赖
```
<dependencies>
  <dependency>
    <groupId>com.couchbase.client</groupId>
    <artifactId>java-client</artifactId>
    <version>2.4.6</version>
  </dependency>
</dependencies>
```

## 创建 Example.java
在查询指定的buckets之前一定要保证有相应的PrimaryIndex，否则会提示查询错误。
```
import com.couchbase.client.java.*;
import com.couchbase.client.java.document.*;
import com.couchbase.client.java.document.json.*;
import com.couchbase.client.java.query.*;

public class Example {

    public static void main(String... args) throws Exception {
        // Initialize the Connection
        Cluster cluster = CouchbaseCluster.create("localhost");

        // For more than one server -->
        // Connects to a cluster on 10.0.0.1 and tries 10.0.0.2
        //Cluster cluster = CouchbaseCluster.create("10.0.0.1", "10.0.0.2");

        Bucket bucket = cluster.openBucket("default");

        // Create a JSON Document
        JsonObject arthur = JsonObject.create()
            .put("name", "Arthur")
            .put("email", "kingarthur@couchbase.com")
            .put("interests", JsonArray.from("Holy Grail", "African Swallows"));

        // Store the Document
        bucket.upsert(JsonDocument.create("u:king_arthur", arthur));

        // Load the Document and print it
        // Prints Content and Metadata of the stored Document
        System.out.println(bucket.get("u:king_arthur"));

        // Create a N1QL Primary Index (but ignore if it exists)
        bucket.bucketManager().createN1qlPrimaryIndex(true, false);

        // Perform a N1QL Query
        N1qlQueryResult result = bucket.query(
            N1qlQuery.parameterized("SELECT name FROM default WHERE $1 IN interests",
            JsonArray.from("African Swallows"))
        );

        // Print each found Row
        for (N1qlQueryRow row : result) {
            // Prints {"name":"Arthur"}
            System.out.println(row);
        }

      // Just close a single bucket
      bucket.close();

      // Disconnect and close all buckets
      cluster.disconnect();
    }
}
```

## Python 安装依赖包
```
pip install git+git://github.com/couchbase/couchbase-python-client
```
Installation on Mac OS X
```
brew install libcouchbase
```
Debian and Ubuntu
```
sudo apt-get install libcouchbase-dev libcouchbase2-bin build-essential
```
RHEL and CentOS
```
sudo yum install libcouchbase-devel libcouchbase2-bin gcc gcc-c++
```

## 创建 example.py
```
from couchbase.cluster import Cluster, PasswordAuthenticator
cluster = Cluster('couchbase://localhost')
# cluster.authenticate(ClassicAuthenticator(buckets={'bucket-name': 'password'}))
cluster.authenticate(PasswordAuthenticator('username', 'password'))
bucket = cluster.open_bucket('bucket-name')
bucket.upsert("key", "value")
res = bucket.get("key")
res.value
```

### 参考
* [官方网站](https://www.couchbase.com)
* [基于Java的Couchbase使用入门](https://www.jianshu.com/p/cbc2d351c02f)
* [impala-connect-couchbase](https://github.com/Songhuitang/impala-connect-couchbase)
* [Couchbase Python Client](https://github.com/couchbase/couchbase-python-client)
* [Start Using the SDK](https://developer.couchbase.com/documentation/server/4.5/sdk/c/start-using-sdk.html)
* [Installing on Mac OS X](https://developer.couchbase.com/documentation/server/current/install/macos-install.html)
* [Couchbase 中的分布式储存](http://blog.jiguang.cn/couchbase_distributed_storage/)
* [ntroduction to Couchbase Server](http://docs.couchbase.com/couchbase-manual-2.2/#map-functions)
<!--more-->

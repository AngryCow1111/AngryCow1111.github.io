---
layout: post
title:  es透明实现reindex
no-post-nav: true
category: elasticsearch
tags: [elasticsearch]
excerpt: elasticsearch nosql reindex
---
# es透明实现reindex
1. 创建一个错误的index
```
PUT mytest_index1/mytest/1
{
  "title":"2019-09-01"
}
```
2. 查询mytest_index1这个索引
```
GET mytest_index1/mytest/_mapping
# 结果如下:
{
  "mytest_index1": {
    "mappings": {
      "mytest": {
        "properties": {
          "title": {
            "type": "date"
          }
        }
      }
    }
  }
}
```
此时，我们发现title的类型为date，而我们想要的是text；
我们可以删除mytest_index1，然后再重建。但是，假如我在生产环境下，已经有java应用正在使用mytest_index1了，那我这个操作会导致java应用停机，降低系统的可用性。<font color='red'>不推荐。</font>
2. 解决方法
- 想先将就的index也就是mytest_index1指定一个别名，保证它的正常使用比如将它指定为user_index。
```
PUT mytest_index1/_alias/user_index
```
我们验证一下它是否仍然可用,确认它依然可用。
```
GET user_index/_search
# 查询结果
{
  "took": 0,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": 1,
    "max_score": 1,
    "hits": [
      {
        "_index": "mytest_index1",
        "_type": "mytest",
        "_id": "1",
        "_score": 1,
        "_source": {
          "title": "2019-09-01"
        }
      }
    ]
  }
}
```
3. 创建一个新的index
```
PUT /mytest_index2/
{
  "mappings": {
    "mytest2": {
      "properties": {
        "title": {
          "type": "text"
        }
      }
    }
  }
}
```
查询创建的type的mapping
```
GET /mytest_index2/mytest2/_mapping

# 查询结果
{
  "mytest_index2": {
    "mappings": {
      "mytest2": {
        "properties": {
          "title": {
            "type": "text"
          }
        }
      }
    }
  }
}
```
## 利用scroll来滚动查询

可用利用多线程来使用scroll进行并发查询原index的数据。正常应该使用:
```
GET /user_index/mytest/_search?scroll=1m
{
  "query":{
      "match_all":{}
  }
}

```
测试因为数据较少，所以指定了每次返回的size。

```
GET /user_index/mytest/_search?scroll=1m
{
  "size": 1
}

# 查询结果
{
  "_scroll_id": "DnF1ZXJ5VGhlbkZldGNoBQAAAAAAAAtsFjl1R2o0VW1aUjRtTDF3UUNPWnpfU0EAAAAAAAALbRY5dUdqNFVtWlI0bUwxd1FDT1p6X1NBAAAAAAAAC24WOXVHajRVbVpSNG1MMXdRQ09ael9TQQAAAAAAAAtvFjl1R2o0VW1aUjRtTDF3UUNPWnpfU0EAAAAAAAALcBY5dUdqNFVtWlI0bUwxd1FDT1p6X1NB",
  "took": 0,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": 1,
    "max_score": 1,
    "hits": [
      {
        "_index": "mytest_index1",
        "_type": "mytest",
        "_id": "1",
        "_score": 1,
        "_source": {
          "title": "2019-09-01"
        }
      }
    ]
  }
}
```
第二次scroll查询
```
POST _search/scroll
{
  "scroll":"1m",
  "scroll_id":"DnF1ZXJ5VGhlbkZldGNoBQAAAAAAAAyiFjl1R2o0VW1aUjRtTDF3UUNPWnpfU0EAAAAAAAAMoxY5dUdqNFVtWlI0bUwxd1FDT1p6X1NBAAAAAAAADKQWOXVHajRVbVpSNG1MMXdRQ09ael9TQQAAAAAAAAylFjl1R2o0VW1aUjRtTDF3UUNPWnpfU0EAAAAAAAAMphY5dUdqNFVtWlI0bUwxd1FDT1p6X1NB"
}
```
4. 使用bulkapi将scroll查询出来的数据，存入新的index。
```
POST /_bulk
{"index": {"_index": "mytest_index2","_type":"mytest2","_id": "2"}}
{"title": "2019-09-02"}
```
5. 将别名指向新的index
```
POST _aliases
{
  "actions":[
    {"remove":{"index":"mytest_index1","alias":"user_index"}},
    {"add":{"index":"mytest_index2","alias": "user_index"}}
    ]
}
```
根据别名进行查询，确认是否已经成功
```
GET /user_index/mytest2/_search
# 查询结果
{
  "took": 0,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": 1,
    "max_score": 1,
    "hits": [
      {
        "_index": "mytest_index2",
        "_type": "mytest2",
        "_id": "2",
        "_score": 1,
        "_source": {
          "title": "2019-09-02"
        }
      }
    ]
  }
}
```
       
**到此es的reindex结束。** 

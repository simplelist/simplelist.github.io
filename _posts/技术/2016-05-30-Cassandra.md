---
layout: post
title: Cassandra 保存报错
category: 技术
tags: cassandra
keywords: cassandra
description: cassandra
---
# Expected 4 or 0 byte int



```
Caused by: com.datastax.driver.core.exceptions.InvalidQueryException: Expected 4 or 0 byte int (36)
	at com.datastax.driver.core.Responses$Error.asException(Responses.java:102)
```
原因是创建 cassandra 表的时候将某个字段设置为了 int 类型的,应该是 text 类型
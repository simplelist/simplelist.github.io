---
layout: post
title: groupBy
category: 技术
tags: sql hql cassandra
keywords: sql hql cassandra
description: sql hql cassandra
---

##  需求:一次查询出某个实体的各种状态的数量,在主页展示一个报表
- 方案一:每次发送一条SQL查询一个状态
- 方案一:只执行一条SQL根据status进行分组,select status, count(id) from tableName group by status

方法二如下

```

public Map<Integer, Long> getCountByOrgId(Integer orgId) {
		StringBuffer hqlString = new StringBuffer(" select status, count(id) from tableName where  charityOrgId.id= " +orgId+" group by status");
		List list =getSession().createQuery(hqlString.toString()).list();
		Map<Integer, Long> result=new HashMap<Integer, Long>();
		if (list!=null&&!list.isEmpty()) {
			for(Iterator iterator = list.iterator();iterator.hasNext();){  
	            Object[] objects = (Object[]) iterator.next();  
	            result.put((Integer)objects[0], (Long)objects[1]);
	        }  
		}
		return result;
	}
	
```

---
layout: post
title: 读别人代码,笔记
category: 技术
tags: hutool 
keywords: hutool java 笔记
description: hutool java 笔记
---

#读工具集Hutool代码,笔记

##CollectionUtil

###将集合转换为字符串

> 这里很巧妙用了一个boolean变量来判断是非是第一个字符从而决定是否需要添加分隔符

```

public static <T> String join(Iterable<T> collection, String conjunction) {
		StringBuilder sb = new StringBuilder();
		boolean isFirst = true;
		for (T item : collection) {
			if (isFirst) {
				isFirst = false;
			} else {
				sb.append(conjunction);
			}
			sb.append(item);
		}
		return sb.toString();
	}
	
```

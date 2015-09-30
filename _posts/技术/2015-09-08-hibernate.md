---
layout: post
title: hibernate关系映射xml
category: 技术
tags: hibernate xml
keywords: hibernate
description: hibernate关系映射xml
---
#hibernate关系映射xml

###一对一

一的一方
java

```
private CharityCampReport reportId;
```

xml

```
<one-to-one name="reportId" class="com.yxtar.server.dto.CharityCampReport" cascade="all" property-ref="charityCampId"></one-to-one>
```
对应的另一个一的一方
java

```
private CharityCamp charityCampId;
```

xml

```
<many-to-one name="charityCampId" class="com.yxtar.server.dto.CharityCamp" unique="true">
	<column name="charityCampId" />
</many-to-one>
```

***
###一对多
一的一方 java

```
private Advertiser advertiserId;
```
xml

```
<many-to-one name="advertiserId" class="com.yxtar.server.dto.Advertiser" lazy="true" fetch="select">
	<column name="advertiserId" />
</many-to-one>
```
多的一方 java

```
private Set<MoneyInRecord> moneyInRecords;
```
xml

```
<set name="moneyInRecords" lazy="false" cascade="all" order-by="id desc">
	<key>
		<column name="advertiserId" />
	</key>
	<one-to-many class="com.yxtar.server.dto.MoneyInRecord"/>
</set>
```

***
###多对多
多的一方 java

```
private Set<CharityFund> charityFunds;
```
xml

```
<set name="charityFunds" table="camp_fund_relation">
	<key column="camp_id"/>
	<many-to-many class="com.yxtar.server.dto.CharityFund" column="fund_id"/>
</set>
```
多的一方 java

```
private Set<CharityCamp> charityCamps;
```
xml

```
<set name="charityCamps" table="camp_fund_relation">
	<key column="fund_id"/>
	<many-to-many class="com.yxtar.server.dto.CharityCamp" column="camp_id"/>
</set>
```
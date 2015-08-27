---
layout: post
title: springMVC前置方法
category: 技术
tags: spring springMVC
keywords: springMVC,controller,方法执行之前
description: springMVC中controller方法执行之前
---
#SpringMVC中controller中方法执行之前加入前置方法

###遇到的问题的思考
>写后台接口时发现;每个method中**开始**时都有一些重复的代码,写起来麻烦,太多不美观

于是,想把这些重复代码抽取出来,单独作为一个方法,每次执行method之前调用改方法
>把重复代码抽取成为一个初始化方法,然后在每个method中调用
每次手动调用也不太好,然后搜索了一下强大的SpringMVC果然提供了解决方法

```
@ModelAttribute
public void befroeAtMethod(HttpServletRequest request){
	//TODO init.....
}
```
当然,这个前置方法可以放在父类里,不用再每个controller中写,
父类写了一些接口公用的属性及方法



```
public class Action {
	protected Map<String, Object> resultMap = new HashMap<String, Object>();
	protected Map<String, Object> acceptMap = new HashMap<String, Object>();
	protected HttpServletRequest request;
	protected ValidationUtil validationUtil = ValidationUtil.getInstance();

	@ModelAttribute
	private void beforeAtMethod(HttpServletRequest request) {
		initialAction(request);
		try {
			setStreamToMap();
		} catch (IOException e) {
			e.printStackTrace();
			faile(ErrorType.ParasIsNull.getName());
		}
	}
	
	
	protected void initialAction(HttpServletRequest request) {
		resultMap.clear();
		acceptMap.clear();
		/*
		 * set the HttpServletRequest from subClass;
		 */
		this.request = request;
	}

	protected void faile(String name) {
		resultMap.put("success", false);
		resultMap.put("reason", name);
	}

	protected void success(Object object) {
		resultMap.put("success", true);
		resultMap.put("result", object);
	}

	/**
	 * transform the HttpServletRequest.inputStream to (Map<String, Object> acceptMap)
	 * 
	 * @throws IOException
	 */
	protected void setStreamToMap() throws IOException {
		if (request != null) {
			acceptMap = ActionUtils.getInstance().toMap(request.getInputStream());
		}
	}

}
```
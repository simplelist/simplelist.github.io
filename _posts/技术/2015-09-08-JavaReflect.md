---
layout: post
title: java反射
category: 技术
tags: java反射
keywords: java反射
description: java反射
---
# java反射

```
package com.yxtar.server.dto;

import java.io.Serializable;
import java.lang.reflect.Field;
import java.lang.reflect.Method;
import java.util.ArrayList;
import java.util.List;

import com.yxtar.app.base.model.map.BaseMap;

public abstract class BaseDto implements Serializable, StrategyMap {

	/**
	 * process the complex type by subClass 声明abstract方法由子类自己去处理复杂类型
	 * 
	 * @param map
	 *            被复制的BaseMap类
	 */
	public abstract void copyFromMapProcess(BaseMap map);

	/**
	 * process the base type by subClass 处理基本类型和字符串
	 * 
	 * @param map
	 *            被复制的BaseMap类
	 */
	public BaseDto copyFromMap(BaseMap map) {
		try {
			if (null != map) {
				Class<?> mapClass = Class.forName(map.getClass().getName());
				Field[] mapFields = mapClass.getDeclaredFields();

				// 将map里的字段放到list中
				Class<?> dtoClass = Class.forName(this.getClass().getName());
				List<String> fieldList = new ArrayList<String>();
				Field[] dtoFields = dtoClass.getDeclaredFields();
				for (Field field : dtoFields) {
					fieldList.add(field.getName());
				}
				for (Field field : mapFields) {
					if (null != getter(map, field)) {
						// 有值才处理
						if (fieldList.contains(field.getName())) {
							// dto中必须也有对应的字段,才处理
							if (field.getType().toString().contains("java.lang.String") || isWrapClass(field.getType()) || field.getType().isPrimitive()
									|| field.getType().toString().contains("java.util.Date")) {
								// 处理基本类型,包装类和String类型的字段
								setter(this, field, getter(map, field), field.getType());
							}
						}
					}
				}
				// 子类处理
				copyFromMapProcess(map);
			}
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		}
		return this;
	}

	/**
	 * 将dto类型转换为对应的Map类型 声明abstract方法由子类去完成一些非基本类型的处理
	 * 
	 * @param object
	 *            生成的新的Map对象
	 */
	public abstract void generateMapProcess(Object object);

	/**
	 * 将dto类型转换为对应的Map类型 处理基本类型
	 */
	@Override
	public Object generateMap() {
		Object map = null;
		try {

			/**
			 * get the dto class & fields
			 */
			Class<?> dtoClass = Class.forName(this.getClass().getName());
			Field[] dtoFields = dtoClass.getDeclaredFields();
			/**
			 * map class generator by dtoClass 创建一个对应的map对象
			 */
			String mapName = "com.yxtar.app.base.model.map." + dtoClass.getSimpleName() + "Map";
			Class<?> mapClass = Class.forName(mapName);
			map = mapClass.newInstance();
			// 将map里的字段放到list中
			List<String> fieldList = new ArrayList<String>();
			Field[] mapFields = mapClass.getDeclaredFields();
			for (Field field : mapFields) {
				fieldList.add(field.getName());
			}
			for (Field field : dtoFields) {
				if (null != getter(this, field)) {
					// 有值才处理
					if (fieldList.contains(field.getName())) {
						// map中必须也有对应的字段,才处理
						if (field.getType().toString().contains("java.lang.String") || isWrapClass(field.getType()) || field.getType().isPrimitive()
								|| field.getType().toString().contains("java.util.Date")) {
							// 处理基本类型,包装类和String类型的字段
							setter(map, field, getter(this, field), field.getType());
						} else if (field.getType().toString().contains("java.util.UUID")) {
							// 处理UUID类型
							setter(map, field, getter(this, field).toString(), mapClass.getDeclaredField(field.getName()).getType());
						}
					}
				}
			}
			// 子类自己去做一些非基本类型的处理
			generateMapProcess(map);
		} catch (Exception e) {
			e.printStackTrace();
		}
		return map;
	}

	/**
	 * 获取Field的值 getXxx(); 将Field名字的首字母大写
	 * 
	 * @param obj
	 *            操作的对象
	 * @param att
	 *            操作的属性
	 * */
	private static Object getter(Object obj, Field att) {
		Object a = null;
		try {
			Method method = obj.getClass().getMethod("get" + toUpperCaseFirstOne(att.getName()));
			a = method.invoke(obj);
		} catch (Exception e) {
			e.printStackTrace();
		}
		return a;
	}

	/**
	 * 设置Field的值 setXxx(); 将Field名字的首字母大写
	 * 
	 * @param obj
	 *            操作的对象
	 * @param att
	 *            操作的属性
	 * @param value
	 *            设置的值
	 * @param type
	 *            参数的属性
	 * */
	private void setter(Object obj, Field att, Object value, Class<?> type) {
		try {
			Method method = obj.getClass().getMethod("set" + toUpperCaseFirstOne(att.getName()), type);
			method.invoke(obj, value);
		} catch (Exception e) {
			e.printStackTrace();
		}
	}

	/**
	 * 首字母转大写
	 * 
	 * @param s
	 * @return
	 */
	private static String toUpperCaseFirstOne(String s) {
		if (Character.isUpperCase(s.charAt(0))) {
			return s;
		} else {
			return (new StringBuilder()).append(Character.toUpperCase(s.charAt(0))).append(s.substring(1)).toString();
		}
	}

	/**
	 * 判断类是否为基本类型
	 * 
	 * @param clz
	 * @return
	 */
	private boolean isWrapClass(Class clz) {
		try {
			return ((Class) clz.getField("TYPE").get(null)).isPrimitive();
		} catch (Exception e) {
			return false;
		}
	}

	
}
```

### Admin.java

```
package com.yxtar.server.dto;

import com.yxtar.app.base.model.map.BaseMap;

public class Admin extends BaseDto {
	@Override
	public void copyFromMapProcess(BaseMap map) {
		// TODO Auto-generated method stub
		
	}

	private Integer id;
	private String fname;
	private String lname;
	private String email;
	private String cpersonMobile;
	private String loginId;
	private String password;
	private Integer authority;
	private Integer status;

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public String getFname() {
		return fname;
	}

	public void setFname(String fname) {
		this.fname = fname;
	}

	public String getLname() {
		return lname;
	}

	public void setLname(String lname) {
		this.lname = lname;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}

	public String getCpersonMobile() {
		return cpersonMobile;
	}

	public void setCpersonMobile(String cpersonMobile) {
		this.cpersonMobile = cpersonMobile;
	}

	public String getLoginId() {
		return loginId;
	}

	public void setLoginId(String loginId) {
		this.loginId = loginId;
	}

	public String getPassword() {
		return password;
	}

	public void setPassword(String password) {
		this.password = password;
	}

	public Integer getAuthority() {
		return authority;
	}

	public void setAuthority(Integer authority) {
		this.authority = authority;
	}

	public Integer getStatus() {
		return status;
	}

	public void setStatus(Integer status) {
		this.status = status;
	}

	@Override
	public void generateMapProcess(Object object) {
		// TODO Auto-generated method stub

	}

}
```
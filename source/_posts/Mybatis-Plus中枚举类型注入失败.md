---
title: Mybatis-Plus中枚举类型注入失败
date: 2021-4-15 12:14:46
tags: Java
categories:
  - Java
---

# 问题描述
项目持久层框架是`Mybatis-plus`（以下简称mybatis），数据库是`Mysql`  
最近在将项目中的一些字段改为枚举类型，但是后来发现枚举变量一直注入不进去  
通过对mybatis的 <b>[defaultEnumTypeHandler配置（默认枚举处理类）](https://baomidou.com/config/#defaultEnumTypeHandler)</b> 的`org.apache.ibatis.type.EnumOrdinalTypeHandler#valueOf`方法进行DEBUG，发现value参数的值是**布尔类型**
![./Mybatis-Plus中枚举类型注入失败/2021-04-15-00-23-53.png](2021-04-15-00-23-53.png)
这时第一时间想到可能是因为数据库使用**tinyint**类型导致的

# 解决方案
解决方法就很简单了，只需要将数据库字段的tinyint类型改为int就好了。  
![./Mybatis-Plus中枚举类型注入失败/2021-04-15-00-34-43.png](2021-04-15-00-34-43.png)
可以看到value的值变成了1，而不是上面的true了，问题至此解决  
但是你说，诶，我任性，我就要设置为tinyint怎么办呢，那我们可以把tinyint的长度设置为4，这样问题也能得到解决
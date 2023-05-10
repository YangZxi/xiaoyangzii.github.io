---
title: 关于Jackson注解@JsonIgnore用在getter和setter方法上失效的解决办法
date: 2020-10-25 12:14:10
tags: 
  - Java
categories:
  - Java
---

这两天在写项目的时候，有一个需求时用户信息的资料查询，只允许从JSON字符串序列化成Java对象，而Java对象转为JSON字符串应该时应该忽略。

经了解然后使用了`@JsonIgnore`注解
``` java
@TableName(value = "user")
public class User extends BaseEntity implements Serializable {

    @TableId(type = IdType.AUTO)
    private Integer id;
    private String username;
    private String password;

    @JsonIgnore
    public String getPassword() {
        return password;
    }

}
```
想象总是美好的，但是现实总是残酷的
理论上来说，我在getter方法上加了注解以后在序列化成JSON的时候应该不会取出password属性。
然鹅，他却静静躺在了我的Postman响应结果中
然后我点进了这个注解的源代码

> * NOTE! As Jackson 2.6, there is a new and improved way to define
> * `read-only` and `write-only` properties, using
> * {@link JsonProperty#access()} annotation: this is recommended over
> * use of separate <code>JsonIgnore</code> and {@link JsonProperty}
> * annotations.

大概意思就是说从2.6的版本以后，这个注解用在getter和setter方法将不生效了，改用`@JsonProperty`注解

查看下`@JsonProperty`参数
![image.png](https://qiniu.xiaosm.cn/blog/image_1593500533716.png-blogshuiyin)
有一个access属性，查看源码
``` java
/**
 * Optional property that may be used to change the way visibility of
 * accessors (getter, field-as-getter) and mutators (constructor parameter,
 * setter, field-as-setter) is determined, either so that otherwise
 * non-visible accessors (like private getters) may be used; or that
 * otherwise visible accessors are ignored.
 *<p>
 * Default value os {@link Access#AUTO} which means that access is determined
 * solely based on visibility and other annotations.
 *
 * @since 2.6
 */
```
这个属性可以设置是否允许读写（getter/setter）
经过修改以后正确的代码如下
``` java
@JsonProperty(access = JsonProperty.Access.WRITE_ONLY)
private String password;
```
其中`WRITE_ONLY`表示只允许写，读的操作是忽略的
所以这样就达到了我们想要的后端给前端的数据不带密码


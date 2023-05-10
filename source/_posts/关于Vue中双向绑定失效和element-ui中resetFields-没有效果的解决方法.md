---
title: 关于Vue中双向绑定失效和element-ui中resetFields()没有效果的解决方法
date: 2020-10-25 12:15:17
tags: 
  - Vue
categories:
  - Vue
---

## 双向绑定失效
起因是我的表单是写在Dialog中的，然后有些数据是需要从后台获取然后再重新赋值上去，因为我有一个字段是数组，且需要从后台获取，所以就出现了双向绑定没有失效了

查阅了下文档，发现在[**深入响应式原理**](https://cn.vuejs.org/v2/guide/reactivity.html)一文中
> ### 检测变化的注意事项
> 由于 JavaScript 的限制，Vue 不能检测数组和对象的变化。尽管如此我们还是有一些办法来回避这些限制并保证它们的响应性。

大概就是说，我对`响应式 Property(Data方法)`中的对象或数组进行的修改不会触发更新。
官方推荐使用`this.$set(需要修改对象, 键名, 值)`的形式进行修改

如下
``` javascript
data() {
  modelForm: {
    id: null,
    username: null,
    nickname: null,
    email: null,
    gender: null,
    age: null,
    roleIds: []
  },
},
method: {
  modifyArray() {
    this.$set(this.modelForm, "roleIds", ['1', '2']);
  }
}
```
这样就能大概能解决双向绑定失效的问题，至于为什么说大概，别问我，因为我菜。

## resetFields()失效
网上有很多解决方法，我就只说说我的解决方法**遍历赋值**
对，没错，就是遍历赋值
通过上面提到的`this.$set()`方法对每个属性进行重新赋值
``` Javascript
const clearObj = function(source, vm) {
  Object.keys(source).forEach(key => {
    if (Array.isArray(source[key])) {
      source[key].splice(0);
    } else {
      vm.$set(source, key, null);
    }
  }
}
```
`vm`是指向调用者的this上下文
这里对数组进行特殊处理，具体可以参阅[**深入响应式原理**](https://cn.vuejs.org/v2/guide/reactivity.html)

欢迎批评和指正


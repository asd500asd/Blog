---
title: console.log打印对象的坑
date: 2018-11-17 16:20:00
tags:
- 防坑指南
categories:
- 防坑指南
---

> 浏览器调试中使用console.log是很常见的，但是在打印对象的时候是有坑的，这个需要记住以下。


```
var a = {a: 1}
console.log(a)
// 这时控制台输出 ▶ {a: 1}  * 注意这时还没有展开这个对象
a.a = 2
// 这时我们把上面的对象a展开，就会发现展示下面结果
// ▼{a: 1}
//    a: 2
//   ▶__proto__: Object
```

这应该是控制在输出log的时候，其实是只保存了一个a的引用，当鼠标要展开这个对象时，再去调用getter方法去目标对象中取值，为了验证这个想法，我利用Object.defineProperty方法去定义一个对象：



```
// 方案1
var a = {}
Object.defineProperty(a, 'a', {
  writable: false,
  value: 1
})
console.log(a)
a.a = 2
// 此时把上面输出的a对象展开，得到{a: 1}

// 方案2
var b = {a: 1}
Object.defineProperty(b, 'a', {
	get: function () { return 2},
	set: function(newValue){
    bValue = newValue;
  }
})
console.log(b)
b.a = 3
// 此时把上面输出的a对象展开，得到{a: 2}

```
从上面两个代码我们就发现，确实当console打印出来时，他只保留了一个对象的引用，当真正展开去查看对象时才去调用getter方法取值。那是每次展开对象时都会去取值嘛？


```
var a = {a: 1}
console.log(a) // 点击展开对象，再收起
a.a = 2
// 再展开上面的对象发现还是1
```

所以我们发现只有第一次展开对象的时候会去getter他的值，后面收起就真的只是收起来了，再次展开也只是展开对象不会再次去取值。

所以，为什么会出现最开始调试时的那个问题。是因为请求回来之后操作改变了console引用对象的值，导致我们在查看对象时已经不是最初的返回结果了。那其实这样也不是我们理想当中状态，我们console就是为了输出当时的状态，而不是后面更改过的状态。

### 解决
知道了问题的原因，解决方案其实就是在打印的时候去拷贝一个对象，或者是在返回后不要修改console的对象。我的方法是用了lodash中的拷贝方法，console一个拷贝出来的对象。


```
const _response = _.cloneDeep(response)
console.log(_response)
```

还有一种比较简单的深复制方法：
```
var newObject = JSON.parse(JSON.stringify(oldObject));
```
当然这个方法有一些局限性，比如：如果被拷贝的对象中有function，则拷贝之后的对象就会丢失这个function，如果被拷贝的对象中有正则表达式，则拷贝之后的对象正则表达式会变成Object。


```
let a = {
  name: 'liyanfeng',
  sayName: function() {alert(this.name)},
  numberReg: /\d+/
  }
let b = JSON.parse(JSON.stringify(a))
// sayName丢失，numberReg变为了对象
console.dir(b) // {name: 'liyanfeng', numberReg: {}}

```

### console的作用
前端调试中离不开console命令，主要是用来记录打印日志便于前端调试，其实console还有一些其他的妙用，比如计数（count）、计时（time）、断言（assert）等等功能，具体可以看以下的文章来学习：
- [Chrome 控制台console的用法](https://segmentfault.com/a/1190000002511877)

- [你真的了解 CONSOLE 吗](http://hao.jser.com/archive/4202/)

### 延伸
- [ 你所不知道的JSON.stringify](https://blog.fundebug.com/2017/08/17/what-you-didnt-know%20about-json-stringify/)

- [深入剖析 JavaScript 的深复制](http://jerryzou.com/posts/dive-into-deep-clone-in-javascript/)
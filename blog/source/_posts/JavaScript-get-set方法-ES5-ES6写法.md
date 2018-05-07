---
title: JavaScript get set方法 ES5/ES6写法
date: 2018-05-07 10:38:50
tags:
- 前端
categories:
- 前端
---

网上鲜有get和set的方法的实例，在这边再mark一下。

get和set我个人理解本身只是一个语法糖，它定义的属性相当于“存储器属性”

为内部属性提供了一个方便习惯的读/写方式

### ES5写法

```
function Number(num) {
  this._num = num           //这里的_num和get/set方法num()不能重名
}

//get/set方法使用同一个命名，增加可读性
Number.prototype = {
  get num() {
    return this._num;
  },

  set num(num) {
        this._num = num;
  }
}

var test = new Number(8);
console.log(test.num);
test.num = 88;
console.log(test.num);
```

输出：

>8

>88


在这里，定义了Number类的一个值_num，再原型中注入set和get方法，此时就可以使用test.num取值和赋值，更加直观。


---
### ES6写法

再看看用ES6中class的实现：

```
class Num {
  constructor(num) {
        this._num = num;
  }

  get num() {
    return this._num;
  }

  set num(num) {
    this._num = num;
  }

}

var test = new Num(9);
console.log(test.num);
test.num = 99;
console.log(test.num);
```

输出：

>9

>99
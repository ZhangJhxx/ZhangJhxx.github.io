---
title: 实现模板字符串&replace复习
date: 2022-02-27 20:13:15
tags: 
- 模板字符串实现
- replace
categories: 
- 面试
---

javaScript在线测试正则网站：https://regexr.com/

mdn replace方法详解：https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace

代码实现：

**函数function(str, obj)	`"{name} is {age}"`&`{name:"tom",age:"20"}`->"tom is 20"**

```js
function template(str, obj) {
  const reg = /{(\w+)}/g;
  return str.replace(reg, (match, key) => {
    //key代表正则表达式中的括号里匹配到的第一个字符串
    console.log(match,key);
    /*
    * {name} name
    * {key} key
    */
    return obj[key];
  });
}
const res = template("{name} is {age}", { name: "jac", age: 20 });
console.log(res);// jac is 20
```


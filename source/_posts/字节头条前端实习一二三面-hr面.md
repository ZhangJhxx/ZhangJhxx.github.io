---
title: 2022字节头条前端实习一二三面+hr面
date: 2022-03-02 16:56:03
tags: 
- 字节
- 前端面试
categories: 
- 面试
---
> 挂了hr面，当时还是觉得比较可惜
### 一面 1月28日

1. 问css: less,sass（可能还问了stylus） 平时有没有在用，熟悉mixin吗

   > 由于平时写的css比较简单，只用到了less很简单的功能。

​		复盘：以sass为例，知识点@mixin与@include , 嵌套，定义变量$, 继承变量@extend。

2.  移动端适配&postcss

   px-to-rem 最为常用

   https://www.jianshu.com/p/8f9aab666c4a

3. rem与em有什么区别

​		rem是基于html元素的字体大小来决定，而em则根据使用它的元素的大小决定

4. 浏览器渲染以及强制缓存协商缓存

   https://www.bilibili.com/video/BV1x54y1B7RE?spm_id_from=333.999.0.0

   https://www.jianshu.com/p/9c95db596df5

5. 实现deepClone

   ```js
   let obj = {
     a: 1,
     b: [12, 34, 56],
     c: function () {
       console.log(123);
     },
     d: {
       e: 23,
       f: 34,
     },
     set f(value) {
       this.a = value;
     },
     get f() {
       return this.a;
     },
   };
   ```

   1. 丐版

      ```js
      return JSON.parse(JSON.stringify(target))
      ```

      - RegExp、Error对象，则序列化的结果将只得到空对象 RegExp、Error => {}；
      - 时间对象(date)，则序列化结果：时间对象=>字符串的形式
      -  function,undefined 丢失
      - NaN、Infinity和-Infinity，则序列化的结果会变成null
      - 对象是由构造函数生成的，则序列化的结果会丢弃对象的 constructor

   2. 简易实现

      ```js
      function clone1(source){
        if(typeof source === "object") {
          const target = Array.isArray(source) ? [] : {};
          for(let key in source) {
            target[key] = clone1(source[key]);
          }
          return target;
        }else{
          return source;
        }
      }
      const target1 = clone1(obj);
      console.log(target1);//结果无法克隆get set
      ```

   3. 完整实现      

      ```js
      let deepClone1 = (source,target ={})=>{
        if(typeof source === "object" && source !== null){ // 判断source不为基本数据类型
          const names = Object.getOwnPropertyNames(source);
          for(let i=0;i<names.length;i++){
            let description = Object.getOwnPropertyDescriptor(source, names[i]);
            if(typeof description.value === "object" && description.value !== null){
              let val;
              if(Array.isArray(description.value)){
                val = [];
              }else{
                val = {};
              }
              Object.defineProperty(target,names[i],{
                configurable: description.configurable,
                enumerable:description.enumerable,
                value:val,
                writable: description.writable,
              })
              deepClone1(description.value,val);
            }else{
              Object.defineProperty(target,names[i],description);
            }
          }
          return target;
        }else{
          return source;
        }
      }
      ```

   4. 循环引用的解决方法

      weakMap

6. 版本对比

  类似 https://leetcode-cn.com/problems/compare-version-numbers/

7. react 中setState多次操作，一共会render几次

8. promise setInterval setTimeout 对比

9. 浏览器事件环 EventLoop
    当微任务正在执行时，有一个同步任务进入，此时会执行哪个任务？
    当时回答微任务挂起，执行同步任务。×
    应该是不会终止微任务的执行的。
    https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/

### 二面 2月11日
1. UDP和HTTP协议是如何连接的

   是个坑，HTTP下层的传输层协议为TCP

2. 问项目

3. 封装get

   大概是这样的

   ```js
   const o = {
     a:{
       b:{
         c:1
       }
     }
   };
   function get(obj,selectStr){
     let splitStr = selectStr.split(".");
     let target = obj;
     for(let i = 0;i<splitStr.length;i++){
       target = target[splitStr[i]];
     }
     return target;
   }
   ```

4. 手写throttle/debounce

   ```js
   function throttle(callback,delay){
     let timer;
     return function(){
       const context = this;
       const args = [...arguments];
       if(timer){
         return;
       }
       timer = setTimeout(()=>{
         callback.apply(context,args);
         timer = null;
       },delay);
     }
   }
   ```

   ```js
   function debounce(callback,delay,immediate){//immediate标记是否立即被调用
     let timer;
     return function(){
       const context = this;
       const args = [...arguments];
       clearTimeout(timer);
       if(immediate){
         let startNow = !timer;
         timer = setTimeout(()=>{
           timer = null;
         },delay);
         if(startNow) callback.apply(context,args);
       }else{
         timer = setTimeout(()=>{
           callback.apply(context,args);
         },delay)
       }
     }
   }
   ```

5. 考虑如何实现用户勾选“记住我”的功能

### 三面 2月24日

1. 行内元素与块级元素对比，列举常用的行内元素

   - `b`,`big`,`i`,`small`

   - `abbr`,`code`,`code`,`em`,`kbd`,`strong`

   - `a`,`br`,`img`,`span`,`sub`,`sup`

   - `button`,`input`,`label`,`textarea`,`select`

- 内容

  - 一般情况下，行内元素只能包含数据和其他行内元素。

  - 而块级元素可以包含行内元素和其他块级元素。这种结构上的包含继承区别可以使块级元素创建比行内元素更”大型“的结构。

- 格式
  - 默认情况下，行内元素不会以新行开始，而块级元素会新起一行。

2. 与webpack相似的打包工具

   <img src="https://me-file-list.oss-cn-beijing.aliyuncs.com/img/8ed8d520-b1a4-11eb-85f6-6fac77c0c9b3.png" alt="img" style="zoom:67%;" /> 

   - rollup 更适用于JS库的打包（react、vue、three.js)

     - 代码效率更简洁、效率更高

     - 默认支持 Tree-shaking

       -----

     - 不支持HMR

     - 不支持CommonJs ES新特性

   - parcel

     - 开箱即用，0配置

     - 支持以HTML作为入口

     - 支持HMR

       -----
     
     - 根目录不主张放js文件，配置不方便

   - ==unpack== 基于 ESM 的 HMR

     - Snowpack 

       - Snowpack利用[JavaScript](https://cloud.tencent.com/product/sms?from=10680)的本机模块系统（称为ESM）来避免不必要的工作并保持流畅的开发体验
       - ![img](https://me-file-list.oss-cn-beijing.aliyuncs.com/img/79197830-b1a3-11eb-85f6-6fac77c0c9b3.png)

     - vite

       > `vite`会直接启动开发服务器，不需要进行打包操作，也就意味着不需要分析模块的依赖、不需要编译，因此启动速度非常快

       - 使用`es module`导入代码, 极快的启动速度(ESM)

       - 即时的热模块替换(HMR)

       - 真正的按需编译

         ![1](https://me-file-list.oss-cn-beijing.aliyuncs.com/img/1.png)

3. 最近用到的模块

   可以按不同种类分

   https://www.jianshu.com/p/6836a42ff6ae

   classnames, axios, @testing-library/react, react-create-app,webpack,lodash


4. 问项目相关

5. react JSX是什么，源码相关

6. 开源协议

    ![开源协议_01](https://me-file-list.oss-cn-beijing.aliyuncs.com/img/%E5%BC%80%E6%BA%90%E5%8D%8F%E8%AE%AE_01.jpg)

    ![开源协议](https://me-file-list.oss-cn-beijing.aliyuncs.com/img/%E5%BC%80%E6%BA%90%E5%8D%8F%E8%AE%AE.png)

7. package.json 相关
    script中的命令 默认情况下 会去node_modules/bin下查找，故直接执行右边有可能出错。
    eg.
    "serve":"webpack serve --mode=development"
    没有全局安装webpack，执行右边命令出错，但是执行npm run serve正常


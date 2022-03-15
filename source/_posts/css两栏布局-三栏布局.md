---
title: css两栏布局&三栏布局
date: 2022-02-28 09:55:37
tags: 
- BFC-两栏布局
- 圣杯布局
- 双飞翼布局
categories: 
- css
---

### BFC应用：实现两栏布局

```html
<div class="container">
  <div class="left"></div>
  <div class="right"></div>
</div>
```

```css
.container{
}
.left{
  background-color: red;
  width: 300px;
  height: 300px;
  float: left;
}
.right{
  background-color: green;
  height: 400px;
  /* overflow: hidden; */
}
```

此时红色块覆盖在绿色块上，float脱离了文档流
[![butmqK.md.png](https://s4.ax1x.com/2022/02/28/butmqK.md.png)](https://imgtu.com/i/butmqK)
为div.right开启BFC

> BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。
计算BFC的高度时，浮动元素也参与计算。**也就是说BFC的区域不会与float box重叠**。
```css
.right{
	overflow:hidden;
}
```
绿色块此时不与红色块重叠
[![buw0vd.md.png](https://s4.ax1x.com/2022/02/28/buw0vd.md.png)](https://imgtu.com/i/buw0vd)
### 三栏布局
圣杯布局
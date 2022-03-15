---
title: react 合成事件
date: 2022-02-26 16:50:13
tags: 
- react
- 源码学习
categories: 
- react源码学习记录
---
### 合成事件
React有自己的一套事件机制,它重新封装了绝大部分的原生事件。

由于fiber机制的特点，生成一个fiber节点时，它对应的dom节点有可能还未挂载，onClick这样的事件处理函数作为fiber节点的prop，也就不能直接被绑定到真实的DOM节点上。

为此，React提供了一种“顶层注册，事件收集，统一触发”的事件机制。

这套机制规避了无法将事件直接绑定到DOM节点上的问题，并且能够很好地利用fiber树的层级关系来生成事件执行路径，进而模拟事件捕获和冒泡，另外还带来两个非常重要的特性：

- 对事件进行归类，可以在事件产生的任务上包含不同的优先级
- 提供合成事件对象，抹平浏览器的兼容性差异

REACT 16下

>合成事件采用了事件池，这样做可以大大节省内存，而不会频繁的创建和销毁事件对象。React并不是将click事件绑在该div的真实DOM上，而是在document处监听所有支持的事件，当事件发生并冒泡至document处时，React将事件内容封装并交由真正的处理函数运行。

REACT 17下
> React 不再将事件添加在document 上，而是添加到渲染 React 树的根 DOM 容器中。并且除去了事件池

[![bZUQhR.md.png](https://s4.ax1x.com/2022/02/26/bZUQhR.md.png)](https://imgtu.com/i/bZUQhR)

```react
const App = () =>{
  const parentRef = useRef(null);
  const childRef = useRef(null);
  useEffect(()=>{
    //绑定原生事件
    parentRef.current.addEventListener("click",()=>{
      console.log("原生----父元素捕获");
    },true)
    parentRef.current.addEventListener("click",()=>{
      console.log("原生----父元素冒泡");
    })
    childRef.current.addEventListener("click",()=>{
      console.log("原生----子元素捕获");
    },true)
    childRef.current.addEventListener("click",()=>{
      console.log("原生----子元素冒泡");
    })
    document.addEventListener("click",()=>{
      console.log("原生----document捕获");
    },true)
    document.addEventListener("click",()=>{
      console.log("原生----document冒泡");
    })
  },[]);
  function parentBubble (){
    console.log("react----父元素冒泡");
  }
  function childBubble (){
    console.log("react----子元素冒泡");
  }
  function parentCapture (){
    console.log("react----父元素捕获");
  }
  function childCapture (){
    console.log("react----子元素捕获");
  }
  return (
    //绑定react事件
    <div ref={parentRef} onClick={parentBubble} onClickCapture={parentCapture}>
      <div ref={childRef} onClick={childBubble} onClickCapture={childCapture}>
        <button>事件捕获顺序</button>
      </div>
    </div>
  )
}
ReactDOM.render(
  <App />
  ,document.getElementById('root')
);
```

- 在react16下的输出结果
```
原生----document捕获
原生----父元素捕获
原生----子元素捕获
原生----子元素冒泡
原生----父元素冒泡
react----父元素捕获
react----子元素捕获
react----子元素冒泡
react----父元素冒泡
原生----document冒泡
```

- 在react17下得到输出：

```
原生----document捕获
react----父元素捕获
react----子元素捕获
原生----父元素捕获
原生----子元素捕获
原生----子元素冒泡
原生----父元素冒泡
react----子元素冒泡
react----父元素冒泡
原生----document冒泡
```

#### 模拟react16下合成事件的实现

```html
<div class="parent">
  <div class="child">
    <button>事件捕获顺序</button>
  </div>
</div>
```

```js
const parent = document.getElementsByClassName("parent")[0];
const child = document.getElementsByClassName("child")[0];
function dispatchEvent(e) {
  const path = []; //收集所有当前Node的上级目录
  let current = e.target;
  while (current) {
    path.push(current);
    current = current.parentNode;
  }
  for (let i = path.length - 1; i >= 0; i--) {
    if (path[i].onClickCapture) {
      path[i].onClickCapture();
    }
  }
  for (let i = 0; i < path.length; i++) {
    if (path[i].onClick) {
      path[i].onClick();
    }
  }
}
//react的合成事件注册要在document注册之前
document.addEventListener("click", dispatchEvent);
parent.addEventListener("click",() => {console.log("原生----父元素捕获")},true);
parent.addEventListener("click", () => {console.log("原生----父元素冒泡")});
child.addEventListener("click",() => {console.log("原生----子元素捕获")},true);
child.addEventListener("click", () => {console.log("原生----子元素冒泡")});
document.addEventListener("click",() => {console.log("原生----document捕获")},true);
document.addEventListener("click", () => {console.log("原生----document冒泡")});
//react合成事件
parent.onClickCapture = () => {console.log("react----父元素捕获")};
parent.onClick = () => {console.log("react----父元素冒泡")};
child.onClickCapture = () => {console.log("react----子元素捕获")};
child.onClick = () => {console.log("react----子元素冒泡")};
```

#### 模拟react17下合成事件的实现

```html
<div id="root">
  <div class="parent">
    <div class="child">
      <button>事件捕获顺序</button>
    </div>
  </div>
</div>
```

```js
const root = document.getElementById("root");
function dispatchEvent(e,captureFlag) {//添加标志位
  const path = []; //收集所有当前Node的上级目录
  let current = e.target;
  while (current) {
    path.push(current);
    current = current.parentNode;
  }
  if(captureFlag){
    for (let i = path.length - 1; i >= 0; i--) {
      if (path[i].onClickCapture) {
        path[i].onClickCapture();
      }
    }
  }
  if(!captureFlag){
    for (let i = 0; i < path.length; i++) {
      if (path[i].onClick) {
        path[i].onClick();
      }
    }
  }
}
//将绑定至document的事件绑定至根节点
root.addEventListener("click", (e)=>{dispatchEvent(e)},true);
root.addEventListener("click", (e)=>{dispatchEvent(e)},false);
```


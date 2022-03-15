---
title: react设计理念&fiber为什么出现
date: 2022-03-15 16:50:13
tags: 
- react
categories: 
- react源码学习记录
---

### 异步可中断

> react15的协调过程同步（stack reconciler)，又因为js执行为单线程，无法及时相应高优先级的任务，比如用户输入，页面会卡顿，也就是CPU的限制

#### CPU瓶颈问题----解决方案

将比较耗时的任务进行分割，使其可中断，在其他高优先级任务执行结束之后，再从之前中断的部分开始异步执行剩下的计算。

- 实现：任务分割、异步执行、让出执行权

- 方案：

  1. Fiber【一种数据结构既能对应真实DOM，又能作为分隔的单元】

  ![f49c9f904ccf9c9da2022df52dac985da2028488](https://me-file-list.oss-cn-beijing.aliyuncs.com/img/f49c9f904ccf9c9da2022df52dac985da2028488.png)

  ```js
  let firstFiber;
  let nextFiber = firstFiber;
  let shouldYield = false;
  function performUnitOfWork(nextFiber){
    //...
    return nextFiber.next
  }
  function workLoop(deadline){
    while(nextFiber && !shouldYield){
      nextFiber = performUnitOfWork(nextFiber);
      shouldYield = deadline.timeRemaining < 1;
    }
    requestIdleCallBack(workLoop);
  }
  requestIdleCallBack(workLoop);
  ```

  

  2.  Scheduler: `requestIdleCallback`存在浏览器兼容性和触发不稳定的问题，react用实现了一套时间片运行的机制，在react中这部分叫做scheduler
  3. Lane：有了异步调度，我们还需要细粒度的管理各个任务的优先级，让优先级高的任务优先执行，各个Fiber工作单元能比较优先级，相同优先级的任务可以一起更新。

  

#### 副作用分离----代数效应

> 获取数据、文件操作等，在不同的设备性能和网络状况都不一样，react需要处理这些副作用，让我们在编码时最佳时间、运行应用时表现一致。这需要react有分离副作用的能力。

perform ---- 暂停函数的执行

handle ---- 获取函数的执行权

resume ---- 交出函数执行权

```js
function getPrice(id){
  const price = perform id;
  return price;
}
function getTotalPrice(id1, id2){
  const p1 = getPrice(id1);
  const p2 = getPrice(id2);
  return p1+p2;
}
try{
  getTotalPrice("001","002");
}handle(productId){
  fetch(`xxx.com?id=${productId}`).then(res=>{
    resume with res.price;
  })
}
```

↑ perform handle resume 将副作用分离到了getPrice和getTotalPrice之外


将getPrice替换成usePrice，getTotalPrice替换为TotalPrice组件。这就有了hooks分离副作用的能力
```react
function usePrice(id){
  useEffect((id)=>{
    fetch(`xxx.com?id=${productId}`).then(res=>{
      return res.price;
    })
  },[]);
}

function TotalPrice({id1,id2}){
  const p1 = usePrice(id1);
  const p2 = usePrice(id2);
  return <TotalPrice props={...}>
}
```


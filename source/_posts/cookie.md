---
title: cookie
date: 2022-03-03 19:57:19
tags: cookie
categories: 
- 计算机网络
---

> 为了解决Http无状态的问题【客户端与服务端会话状态的问题】，出现了cookie。

cookie的大小一般不超过4kb，它由一个名称（Name）、一个值（Value）和其它几个用于控制 Cookie 有效期、安全性、使用范围的可选属性组成

![image-20220316201203229](https://me-file-list.oss-cn-beijing.aliyuncs.com/img/image-20220316201203229.png)

- Name/Value

  用JavaScript操作Cookie注意对Value进行编码处理。

  http://www.miaoqiyuan.cn/p/escape-encodeuri-encodeuricomponent

- Expires

  设置cookie过期时间

  ```js
  Set-Cookie: id=asdfgB; Expires=Wed, 21 Oct 2022 07:15:00 GMT;
  ```

  Expires属性缺省时，表示是会话性Cookie，Expire的值为Session，表示的就是会话性Cookie。当为会话性 Cookie 的时候，值保存在客户端==内存==中，并在用户关闭浏览器时失效。有些浏览器提供了会话恢复功能，会话期Cookie也会被保留下来。

  持久性Cookie保存在用户的==硬盘==中，直至过期或清除Cookie。

  过期的时间仅与客户端相关，而不是服务端

- Max-Age

  ```js
    Set-Cookie: id=asdfgB; Max-Age=80809;
  ```

​		max-age:为负数时为会话形cookie，为正数为持久形cookie，为0时会立即删除这个cookie

​		max-age的优先级高于expires

- Domain

  Domain指定了Cookie可送达的主机名。如果没有指定，默认为当前文档访问地址中的主机部分，但不包括子域名。

  将Domain设置为`.google.com`那么`a.google.com`和`b.google.com`都可以使用cookie

- Path

  Path指定了一个URL路径，设置path为/test 则/test/doc 下的资源会携带Cookie首部，但/stu 不会携带这个首部

​		Domain和Path标识共同定义了Cookie的作用域，即cookie应该发送给那些URL

- Secure

  仅HTTPS协议传输

- HTTPOnly

  防止客户端脚本通过document.cookie等方式访问Cookie，有助于避免XSS攻击

- SameSite

  > 在跨站请求时不会被发送，从而阻止跨站请求伪造攻击（CSRF）

  - Strict 仅一方请求携带Cookie，即浏览器只发送相同站点请求的Cookie，即当前网页URL与请求目标URL完全一致。
  - Lax允许部分第三方请求携带Cookie
  - None
  
	same-site（同站）/cross-site 与 同源策略中的same-origin/cross-origin不同
	
	same-site的判定较为宽松，cookie的同站判断==不考虑端口和协议==。只要两个URL的eTlLD+1相同即可。eTLD表示顶级域名`.com`，`.github.io`等，eTLD+1表示有效顶级域名+二级域名
	
	www.baidu.com 与 www.a.baidu.com是同站

​		a.github.io和b.github.io是跨站

![2133762988-d56cfa55b282c615](https://me-file-list.oss-cn-beijing.aliyuncs.com/img/2133762988-d56cfa55b282c615.png)

​		参考： https://segmentfault.com/a/1190000022055666

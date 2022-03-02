---
title: typora+picGo+aliyun—oss配置图床
date: 2022-03-02 08:52:01
tags: typora, picGo, oss
---

> 实现使用picGo+oss实现typora上传图片自动保存在阿里云

### 1. 阿里云oss 

   <img src="https://me-file-list.oss-cn-beijing.aliyuncs.com/img/2022-03-02_091844.png" alt="2022-03-02_091844" style="zoom:50%;" /> 
	
	- 创建bucket
	
	  ![2022-03-02_092052](https://me-file-list.oss-cn-beijing.aliyuncs.com/img/2022-03-02_092052.png)
	  bucket名称将在后续配置中用到
	
	  ![2022-03-02_092501](https://me-file-list.oss-cn-beijing.aliyuncs.com/img/2022-03-02_092501.png)
	  个人使用可选择1年40G 9￥

### 2. 下载并配置PicGo 

   [picGo github地址](https://github.com/Molunerfinn/PicGo/releases)

​		 ![2022-03-02_093101](https://me-file-list.oss-cn-beijing.aliyuncs.com/img/2022-03-02_093101.png)

- 存储空间名为创建bucket的名字

- 存储区域为概览下外网访问的前半部分，如下图

<img src="https://me-file-list.oss-cn-beijing.aliyuncs.com/img/2022-03-02_093218.png" alt="2022-03-02_093218" style="zoom:75%;" />

- 指定路径若不存在会自动创建

- KeyId&KeySecret 为accessKey的ID与Secret 

  <img src="https://me-file-list.oss-cn-beijing.aliyuncs.com/img/2022-03-02_093626.png" alt="2022-03-02_093626" style="zoom:67%;" /> 

  - 选择使用子AccessKey，并配置访问oss的权限
  
    ![2022-03-02_093915](https://me-file-list.oss-cn-beijing.aliyuncs.com/img/2022-03-02_093915.png)

- 最后设为默认图床

### 3. 配置typora

![uTools_1646185259545](https://me-file-list.oss-cn-beijing.aliyuncs.com/img/uTools_1646185259545.png)

- PicGo 路径设置为picGo.exe存放的路径

- 验证图片上传选项

  

---
title: Postwoman
date: 2019-11-25 08:47:32
tags: [tools]
---

# 介绍

Postwoman是一个Poatman的免费、快速且美观的替代方案，帮助开发人员更快地创建请求，节省宝贵的开发时间，提升工作效率，Postwoman是开源产品，界面美观，方便使用！

Postman的免费，快速，美观的替代方案——Postwoman

<!-- more -->

在线演示：
https://postwoman.io/

Github

https://github.com/liyasthomas/postwoman
相关特性

1、轻巧：采用简约的UI设计。简单的设计是最好的设计。

2、更快，更轻，更简洁，最小化和响应速度

3、快速：实时发送请求并获取/复制响应

 方法：

- GET

- HEAD

- POST

- PUT

- DELETE

- OPTIONS

- PATCH

除了这些，还可自定义背景，前景色和强调色组合
![img](/img/python/工具/postwoman_1.png)

自定义选项：

1、选择主题：Kinda Dark（默认），Clearly White，Just Black和System主题

2、选择强调颜色：绿色（默认），黄色，粉红色，红色，紫色，橙色，青色和蓝色

PWA：

可以PWA的方式安装

特性：

1、 与Service Workers同时加载

2、离线支持

3、低RAM /内存和CPU使用率

4、添加到主屏幕（页脚中的按钮）

5、桌面PWA支持（页脚中的按钮）

WebSocket：

通过单个TCP连接建立全双工通信通道。发送和接收数据

GraphQL：

GraphQL是API的查询语言

除以上以外还具备其他特性就不一一介绍了，使用后一试便知

Postman的免费，快速，美观的替代方案——Postwoman
开发

相比于其作为Postman的替代方案，笔者其实更在乎它的实现方式，Postwaman是一个由基于Vue构建的Web项目，且可以构建成PWA应用：

开发步骤：
```
git clone https://github.com/liyasthomas/postwoman.git
npm install
npm run dev
```
//打开浏览器即可
//或者，使用docker-compose：
```
#pull
docker pull liyasthomas/postwoman
#run
docker run -p 3000:3000 liyasthomas/postwoman:latest
#build
docker build -t postwoman:latest
```
界面截图

![img](/img/python/工具/postwoman_2.png)

![img](/img/python/工具/postwoman_3.png)

Postman的免费，快速，美观的替代方案——Postwoman

### 总结

单从本文介绍可能并不能体验到这样一个颜值颇高的API客户端，如果你觉得Postman不够好用或者确实是想要一个好看的界面，那么Postwoman将是你最佳的选择！

Postman的免费，快速，美观的替代方案——Postwoman

就是比postman逼格高！其它没什么卵用。
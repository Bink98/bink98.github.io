---
layout: post
title:  "通过Firefly III构建个人财务管理系统的解决方案"
date:   2020-3-17 20:40:00 +0800
categories: life
---

## 为什么需要财务管理

一直以来我都没有财务管理的习惯（没什么钱，又懒得记账），但是拥有一个良好的财务管理的意识和习惯毫无疑问会带来诸多好处：

* 记下每一笔账的同时会让你真正意识到自己的开销。
* 绘制季度/年度个人财务表单，能让你清晰地看到自己一段时间以来的资金去向，以便从中总结，合理规划自己未来的支出。
* 可以制定一定时期内的财务计划，如一个周期的预算，往储蓄账户里攒多少钱来买某个想要的物品。
* ......

而我是在前一段时间在短期内开销过大让我意识到自己需要一个财务管理系统来让自己**意识到自己的支出情况，妥善管理自己的财务**。

## 为什么选择Firefly III

*工欲善其事必先利其器*。市面上存在着各色各样的财务管理软件，选择哪一款作为我的财务管理系统的引擎呢？在调查了各类软件的基本情况之后，我列出了选择财务管理软件一些关键的影响因素：

* **软件是否开源**
* **软件是否能跨平台使用和同步**
* **软件是否拥有一个好看的界面**
* **软件是否拥有一些高阶的记账功能**

下面我将详细解释以上几点：

首先软件是否为开源软件在我这里被列为了最重要的因素，原因是市面上存在的一些记账工具大多需要收费，且软件开发者在开发软件的过程中，对于付费的方式时有变更，拿我用过的[MOZE](https://moze.app/)为例，这款软件在3.0版本之前，在iOS上的收费方式为一次性买断，但在3.0版本之后变成了按月订阅的方式，之前买断的用户可以继续用之前的版本，但之前的版本不会再维护，但买断的用户如果使用3.0之后的版本则需要重新付费，这无疑对老用户来说是一种冒犯。而作为财务管理系统，一旦养成习惯，很可能会伴随我们终身，而各个软件之前数据未必能互通，且使用习惯不一样，一旦放弃之前的软件从新的软件开始消耗的成本太高，因此在这里稳定才是最关键的因素。小开发者开发的软件如MOZE，长期的稳定性不能保证，即使是大的开发商，如微软这样的公司，软件烂尾的现象也时有发生。而作为开源软件来说，首先完全免费是肯定的。另外，由于流行的开源软件拥有一个相对稳定的社区，即使原来的开发者完全放弃开发，也会有社区中其他人继续开发，完全烂尾的风险很小。所以我认为选择一个开源的财务管理软件是十分重要的。

此外，软件还要能够跨平台使用，并且同步。因为作为一个记账软件十分重要的是便捷性，不仅要有桌面端的客户端来进行深度操作，还需要有移动端能够随时随地进行记账，各个客户端之前数据互通并且能够同步。桌面端最好能够Windows，Mac，Linux三平台通用，避免更换设备时带来困扰。

作为一个颜控，没有一个好看的界面怎么行！其实这里的“好看”也不需要太好看，毕竟这类软件还是以实用为主，但是最起码的交互界面要符合现代审美，而不是停留在十几年前的设计。毕竟要长期使用，谁不想一打开软件看到好看的UI收获美美的心情呢。

除去一些基本功能，高阶记账功能也是必不可少的，如拆分账单来实现分期付款；一些固定的支出和收入能够按周期自动记账，如工资，手机话费等；能自动生成一些图表来直观的反映一个阶段的资金收支情况等等。这些高阶功能能在很大程度上省去一些不必要的人工，来实现智能化的记账操作。

综合上述条件，我最终选择的财务管理软件就是——[Firefly III](https://github.com/firefly-iii/firefly-iii)这是一款在[Github](https://github.com)上开源了的软件，并且拥有4.5K的Star，社区的大小还是比较可观的。这款软件是基于Linux开发的，并需要通过Web访问。需要注意的是，如果需要跨设备访问和同步就需要一台自己的Linux服务器，并且要通过Web访问服务器的方式来获取服务。这样的部署方式也是好坏参半，一方面通过Web访问的方式完全不用担心跨设备和同步的问题，只要你的设备能访问网页就行，另一方面也是在一定程度上限制了用户的使用，据我所知目前仅有Android的APP，iOS上暂无客户端。不过对于长时间使用电脑的我来说这并不是主要的缺点，即便需要外出也能通过手机访问网页的方式来完成一些操作。

![bea785823dba6858288e609e959a0996.png](/assets/68c79d4f8b074123b88bdac1be6f166e.png)

它提供了一个简洁清晰的用户界面，并且支持高阶的记账操作，作为财务管理系统中的核心，它提供了一种不完美但稳定且功能全面的解决方案。

## 如何部署Firefly III

我的服务器为一个1核CPU，512M内存的小机器，系统为Debian 9，下面我将展示通过[Docker](https://docker.com) + [Postgresql](https://postgresql.org) + [Caddy](https://caddyserver.com/)部署可通过SSL加密访问的Firefly III。在部署之前需要提前安装好Docker和Postgresql，然后运行以下命令创建卷：
```
docker volume create firefly_iii_export
docker volume create firefly_iii_upload
docker volume create firefly_iii_db
```
然后启动数据库容器：
```
docker run -d \
--name=firefly_iii_db \
-e POSTGRES_PASSWORD=CHANGEME_PASSWD \
-e POSTGRES_USER=postgres \
-v firefly_iii_db:/var/lib/postgresql/data \
postgres:10
```
接着启动Firefly III容器:
```
docker run -d \
--name=firefly_iii_app \
--link=firefly_iii_db \
-e DB_HOST=firefly_iii_db \
-e DB_DATABASE=postgres \
-e DB_USERNAME=postgres \
-e DB_PORT=5432 \
-e DB_PASSWORD=CHANGEME_PASSWD \
-e APP_KEY=CHANGEME_32_CHARS \
-e APP_URL=CHANGEME_SERVER_DOMAIN \
-e TRUSTED_PROXIES=** \
-p 8888:80 \
-v firefly_iii_export:/var/www/firefly-iii/storage/export \
-v firefly_iii_upload:/var/www/firefly-iii/storage/upload \
jc5x/firefly-iii:latest
```
其中的这两行命令为开启反向代理所需：
```
# ...
APP_URL=https://firefly.example.com
TRUSTED_PROXIES=**
# ...
```
使用下面的命令来跟踪容器内的过程是否报错：
```
docker container logs -f <containerID>
```
下面是caddy的配置：
```
SERVER_DOMAIN {
 gzip
 proxy / 127.0.0.1:8888 {
  transparent
 }
}
```
虽然caddy可以自动申请证书，但是如果使用CDN加速需要另外配置证书，如[Cloudflare](https://cloudflare.com)可以在面板的SSL一栏进行配置。

经过以上配置就能通过Https在各种设备上来访问自己的域名使用Firefly III了。
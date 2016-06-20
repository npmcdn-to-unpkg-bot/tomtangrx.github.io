---

layout: post

title:  sqlserver alwayson 遇到的问题

category: 技术

tags: sqlserver alwayson

keywords: sqlserver alwayson

---



# sqlserver alwayson 遇到的问题

## 问题一、 账户 必须是使用域账号登陆
登陆用域账号，服务的本地账户也需要是域账号

## 问题二、 要有一个所有电脑都能访问的共享硬盘
用于备份

## 问题三、 sqlserver 必须在集群后安装

## 问题三、 端口 必须开三个端口
* tcp/ip: 数据库端口（1433）， 5022
~~* udp：1434~~

## 问题三、 数据库必须有完整备份


## 只读路由设置[链接](https://technet.microsoft.com/zh-cn/library/hh710054.aspx#Prerequisites)

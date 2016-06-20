---

layout: post

title:  sqlserver alwayson 遇到的问题

category: 技术

tags: sqlserver alwayson

keywords: sqlserver alwayson

---



# sqlserver alwayson 遇到的问题


[TOC]


## 问题一、 账户 必须是使用域账号登陆
登陆用域账号，服务的本地账户也需要是域账号

## 问题二、 要有一个所有电脑都能访问的共享硬盘
用于备份

## 问题三、 sqlserver 必须在集群后安装

## 问题三、 端口 必须开三个端口
* tcp/ip: 数据库端口（1433）， 5022
<br>
~~udp：1434~~

## 问题三、 数据库必须有完整备份


## 只读路由设置[链接](https://technet.microsoft.com/zh-cn/library/hh710054.aspx#Prerequisites)

code

``` sql
ALTER AVAILABILITY GROUP [testdb]  
 MODIFY REPLICA ON  
N'SQL1' WITH   
(SECONDARY_ROLE (ALLOW_CONNECTIONS = READ_ONLY));  
ALTER AVAILABILITY GROUP [testdb]  
 MODIFY REPLICA ON  
N'SQL1' WITH   
(SECONDARY_ROLE (READ_ONLY_ROUTING_URL = N'TCP://192.168.100.241:1433'));  
  
ALTER AVAILABILITY GROUP [testdb]  
 MODIFY REPLICA ON  
N'SQL2' WITH   
(SECONDARY_ROLE (ALLOW_CONNECTIONS = READ_ONLY));  
ALTER AVAILABILITY GROUP [testdb]  
 MODIFY REPLICA ON  
N'SQL2' WITH   
(SECONDARY_ROLE (READ_ONLY_ROUTING_URL = N'TCP://192.168.100.242:1433'));  
  
ALTER AVAILABILITY GROUP [testdb]   
MODIFY REPLICA ON  
N'SQL1' WITH   
(PRIMARY_ROLE (READ_ONLY_ROUTING_LIST=('SQL2','SQL1')));  
  
ALTER AVAILABILITY GROUP [testdb]   
MODIFY REPLICA ON  
N'SQL2' WITH   
(PRIMARY_ROLE (READ_ONLY_ROUTING_LIST=('SQL1','SQL2')));  
GO
```
只读路由验证

``` sql
SELECT
g.name AS AGName,
ar1.replica_server_name AS ReplicaServerName,
ar2.replica_server_name AS RountingReplicaServerName,
ar1.endpoint_url AS ReplicaServerURL,
ar2.endpoint_url AS RountingReplicaServerURL
FROM sys.availability_read_only_routing_lists rl
INNER JOIN sys.availability_replicas ar1 
ON ar1.replica_id=rl.replica_id
INNER JOIN sys.availability_replicas ar2 
ON ar2.replica_id=rl.read_only_replica_id
INNER JOIN sys.availability_groups g 
ON ar1.group_id=g.group_id
WHERE rl.routing_priority=1
```

数据库链接

```
Server=tcp:MyAgListener,1433;Database=Db1;IntegratedSecurity=SSPI;ApplicationIntent=ReadOnly;MultiSubnetFailover=True  
```
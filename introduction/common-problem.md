# Common problem

## Why not support Linq ?

SmartSql hopes that developers will have more exposure to Sql and gain absolute control and security. So there is currently no plan to support the Code First programming model.

>I thought about how to write Sql, and then write Linq. After that, I might have to look at what Sql is output from Linq. This is really a bad experience. In order to absolutely optimize Sql, developers must have absolute control over Sql. In addition, Sql itself is very simple, why should we add a layer of translator?

> **Codefirst A wonderful but unrealistic idea.**

## I am very interested in SmartSql, I don't know where to start?

Please read the sample project:：<https://github.com/Ahoo-Wang/SmartSql-Starter>

## Which databases does SmartSql support?

As long as the database driver database of ADO.NET is supported, you only need to install the corresponding driver.

## Does SmartSql support multiple databases?

Support, you can introduce multiple database instances through configuration:

``` csharp
var sqlServerMapper = MapperContainer.Instance.GetSqlMapper("SmartSqlMapConfig-SqlServer.xml");
var mySqlMapepr = MapperContainer.Instance.GetSqlMapper("SmartSqlMapConfig-MySql.xml");
```

## What is the performance of SmartSql?

SmartSql's performance and Dapper are the same level of ≈ native handwriting (other multiple ORMs in their test reports indicate higher performance than Dapper,**please keep curious**), SmartSql performance comparison in the document The test report is open source code. Anyone interested in performance can use the source code to run the performance test themselves. **The performance test of your own Run is really a test!**

## How is SmartSql available?

SmartSql has been verified by dozens of online micro-service scenarios over a year, and its performance, availability, and productivity have been verified.

## For simple CURD configuration

SmartSql provides templates for regular Sql operations, and developers can generate Xml configuration files directly from templates.

## Why choose Xml as the configuration syntax

Xml's tag syntax is more flexible to use. For example, filter tags, nested reuse, readability.
Complex query scenarios will be very comfortable to use, readability will be better, no processing at the code level, all to Xml.

## How to use the cluster scenario to get cache consistency

SmartSql provides the Redis cache plugin, which can be installed via NuGet:

``` csharp
Install-Package SmartSql.Cache.Redis
```

## How to do Sql configuration in the cluster scenario

SmartSql provides the ZooKeeper configuration plugin, which is installed via NuGet:

``` csharp
Install-Package SmartSql.ZooKeeperConfig
```

- In addition, the author also open source ZooKeeper node management tool:[ZooKeeper-Admin](https://github.com/Ahoo-Wang/ZooKeeper-Admin),Directly configurable directly through this management tool. About[ZooKeeper-Admin](https://github.com/Ahoo-Wang/ZooKeeper-Admin):
- Written using Asp.net Core
- Docker installation:

``` csharp
docker pull ahoowang/zookeeper.admin
docker run --name zooAdmin -p 80:80 ahoowang/zookeeper.admin
```

- Local installation

``` csharp
git clone https://github.com/Ahoo-Wang/ZooKeeper-Admin.git
dotnet run
```

## I have used MyBatis before, I feel that SmartSql has a feeling of deja vu

Yes, the SmartSql programming model borrows from MyBatis. If you have used MyBatis before, SmartSql will be your better choice, because you can get started directly without any programming changes, and you have cross-platform. High performance, and a lot of support for your plugin.

## Handwritten Xml is very painful to have smart tips

SmartSql provides Xml schema documentation for the experience of entering smart tips:
![Smart reminder](../imgs/intellisense.png)
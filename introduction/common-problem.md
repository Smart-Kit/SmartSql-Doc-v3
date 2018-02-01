# Common problem
## 1. Why don't you support Linq ?
SmartSql wants developers to get more access to Sql and get absolute control and sense of security. So there's no plan to support the Linq programming model.

## 2. For simple CURD configuration ?
SmartSql provides a template for regular Sql operations, and developers can generate Xml configuration documents directly through the template.

## 3. Why choose Xml as a configuration syntax ?
1. Xml's tagging syntax can be used more flexibly, such as screening tags, nested reuse, readability.
2. The complex query scene with up will be very comfortable, readability is good, the code level does not need to do any treatment, all the way to the Xml.

## 4. How do cluster scenarios use caching consistency?
SmartSql provides a Redis cache plug-in that can be obtained through NuGet:
```
Install-Package SmartSql.Cache.Redis
```

## 5. How do Sql configure a lot of Copy in the cluster scene?
SmartSql provides the ZooKeeper configuration plug-in, which is obtained through NuGet:
```
Install-Package SmartSql.ZooKeeperConfig
```



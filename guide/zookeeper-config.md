# Zookeeper Distributed Configuration

## Install SmartSql.ZooKeeperConfig

``` chsarp
Install-Package SmartSql.ZooKeeperConfig
```

## Demo

``` csharp
             String connStr = "192.168.31.103:2181";
             Var configLoader = new ZooKeeperConfigLoader(connStr);
             String configPath = "/Config/App1/SmartSqlMapConfig.xml";
             Var sqlMapper = new SmartSqlMapper(configPath, configLoader);
             Var list = sqlMapper.Query<T_Test>(new RequestContext
             {
                 Scope = "T_Test",
                 SqlId = "GetList",
                 Request = new { Ids = new long[] { 1, 2, 3, 4 } }
             });
```

## Tools use [ZooKeeper-Admin](https://github.com/Ahoo-Wang/ZooKeeper-Admin)

![ZooKeeper-Admin](../imgs/zookeeper-config.png)

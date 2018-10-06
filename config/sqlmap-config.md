# SmartSqlMapConfig

## Demo

``` xml
<?xml version="1.0" encoding="utf-8" ?>
<SmartSqlMapConfig xmlns="http://SmartSql.net/schemas/SmartSqlMapConfig.xsd">
  <Settings IsWatchConfigFile="true" />
  <Database>
    <!--ParameterPrefix:[SqlServer:@ | MySQL:? |Oracle::] -->
    <!--<DbProvider Name="MySqlClientFactory" ParameterPrefix="?" Type="MySql.Data.MySqlClient.MySqlClientFactory,MySql.Data"/>-->
    <!--<DbProvider Name="OracleClientFactory" ParameterPrefix=":" Type="Oracle.ManagedDataAccess.Client.OracleClientFactory,Oracle.ManagedDataAccess"/>-->
    <!--<DbProvider Name="NpgsqlFactory" ParameterPrefix="@" Type="Npgsql.NpgsqlFactory,Npgsql"/>-->
    <DbProvider Name="SqlClientFactory" ParameterPrefix="@" Type="System.Data.SqlClient.SqlClientFactory,System.Data.SqlClient"/>
    <Write Name="WriteDB" ConnectionString="Data Source=.;database=TestDB;uid=sa;pwd=SmartSql.net"/>
    <Read Name="ReadDB-0" ConnectionString="Data Source=.;database=TestDB;uid=sa;pwd=SmartSql.net" Weight="80"/>
    <Read Name="ReadDB-1" ConnectionString="Data Source=.;database=TestDB;uid=sa;pwd=SmartSql.net" Weight="20"/>
  </Database>
  <TypeHandlers>
    <TypeHandler Name="Json" Type="SmartSql.TypeHandler.JsonTypeHandler,SmartSql.TypeHandler"/>
    <TypeHandler Name="Xml" Type="SmartSql.TypeHandler.XmlTypeHandler,SmartSql.TypeHandler"/>
  </TypeHandlers>
  <SmartSqlMaps>
    <SmartSqlMap Path="Maps" Type="Directory"></SmartSqlMap>
    <!--<SmartSqlMap Path="Maps/T_Table.xml"></SmartSqlMap>-->
  </SmartSqlMaps>
</SmartSqlMapConfig>

```

## Settings tag attribute

| Attribute | Description |
| :--------- | --------:|
| IsWatchConfigFile | Whether to monitor configuration files for configuration file hot updates, defaults to false |
| ParameterPrefix | Global parameter prefix, defaults to $ for all DBs |
| IgnoreParameterCase | Ignore parameter case |
| IsCacheEnabled| Whether to enable caching |

---

## Database Subtag

| Label | Description |
| :--------- | --------:|
| DbProvider | DbProviderFactory implementation class for each Db Client |
Write | Write Library (Required) |
| Read | Read Library (optional) |

### DbProvider Tags

| Attribute | Description |
| :--------- | --------:|
| Name | Name ID, DB environment recognition for **Env** tags |
| ParameterPrefix | Parameter Prefix: [SqlServer:@ ; MySQL:? ; Oracle::] |
| Type | Type |

### Write Tags

| Attribute | Description |
| :--------- | --------:|
| Name | Name Identification |
| ConnectionString | Database Link String |

### Read Tags

| Attribute | Description |
| :--------- | --------:|
| Name | Name Identification |
| ConnectionString | Database Link String |
| Weight | Weight |

---

## TypeHandlers Subtag

### TypeHandler tag

| Attribute | Description |
| :--------- | --------:|
| Name | Name Identification |
| Type | Type |

---

## SmartSqlMaps Subtag

| Label | Description |
| :--------- | --------:|
| SmartSqlMap | Used to locate the location of SmartSqlMap files |

### SmartSqlMap tag

| Attribute | Description |
| :--------- | --------:|
| Path | Used to locate SmartSqlMap files/directories Location |
| Type | Enumeration type: File/Directory, File: is a file resource; Directory: is a directory resource, take all Map files under this directory |

# SmartSqlMap

| Attribute | Description |
| :--------- | --------:|
| Scope | Domain for SqlMap definition Sql declaration scope |

## ResultMap

``` xml
    <ResultMap Id="UserExtendResultMap">
      <Result Column="Data" Property="Info" TypeHandler="Json"/>
    </ResultMap>
```

| Attribute | Description |
| :--------- | --------:|
| Id | Uniqueness Number |

### ResultMap.Result

| Attribute | Description |
| :--------- | --------:|
| Column | Column Name |
| Property | Property Name |
| TypeHandler | Type processor, built-in (Json/Xml) |

## ParameterMap

``` xml
    <ParameterMap Id="UserExtendParameterMap">
      <Parameter Property="Info" TypeHandler="Json"/>
    </ParameterMap>
```

| Attribute | Description |
| :--------- | --------:|
| Id | Uniqueness Number |

### ParameterMap.Parameter

| Attribute | Description |
| :--------- | --------:|
| Property | Property Name |
| TypeHandler | Type processor, built-in (Json/Xml) |

## MultipleResultMap

``` xml
    <MultipleResultMap Id="QueryByPage_Map">
      <Result Property="Total"/>
      <Result Property="List"/>
    </MultipleResultMap>
```

``` csharp
    Public class QueryByPageResponse
    {
        Public int Total { get; set; }
        Public IEnumerable<T_Entity> List { get; set; }
    }
```

| Attribute | Description |
| :--------- | --------:|
| Id | Uniqueness Number |

### MultipleResultMap.Result

| Attribute | Description |
| :--------- | --------:|
| Property | Attributes |

## Statement tag

| Attribute | Description |
| :--------- | --------:|
| Id | Uniqueness Number |
Cache | Cache policy number, referenced from Cache tag |
| CommandType | Text/StoredProcedure , Default:Text |
SourceChoice | Unknow/Write/Read , Default:Unknow |
ResultMap | ResultMap.Id for result mapping, list VS attribute name mapping, and field type processor processing |
| ParameterMap | ParameterMap.Id |
MultipleResultMap | MultipleResultMap.Id to return multiple result set mappings |

## Statement Filter subtab

| Tags | Real Conditions |
| :--------- | --------:|
IsEmpty | null or empty string or empty IEnumerable |
| IsEqual | Equal to comparison value |
| IsFalse | The parameter is false |
| IsGreaterEqual | Parameter is greater than or equal to comparison value |
| IsGreaterThan | Parameter is greater than comparison value |
| IsLessEqual | Parameter is less than or equal to comparison value |
| IsLessThan | Parameter is less than comparison value |
| IsNotEmpty | !(null or empty string or empty IEnumerable) |
| IsNotEqual | Parameter is not equal to comparison value |
| IsNotNull | Parameter is not equal to null |
| IsNull | Parameters equal to null |
| IsProperty | Query Object Contains Attribute Name |
| IsTrue | The parameter is true |
| Switch | Switch Tags |
| Case | Sub-label of the Switch tag, equal to the comparison value |
Defalut | Subtag of the Switch tag, hitting this tag when any Case subtag is missed |

## Statement Other subtags

| Label | Description |
| :--------- | --------:|
| Env | Used to determine global variables DbProvider |
| Include | Reference External Statement |
Dynamic | Dynamic tags for wrapping filter tags, the prefix of the first filtered tag matched will be ignored |
| For | For parameters IEnumerable, traversal parameters dynamic splicing Sql |
Where | Inherit to Dynamic for wrapping filter tags, matching the first filter tag prefix is ​​ignored, and adding the Where prefix |
| Set | Inherit to Dynamic for Update, parcel filter tag, match the first filter tag prefix is ​​ignored, add the Set prefix, must match at least one subtag, otherwise a SmartSqlException will be thrown. |
| Placeholder | Placeholder tag to replace parameter key values ​​|

## Cache tag

| Attribute | Description |
| :--------- | --------:|
| Id | Uniqueness Label |
Type | Cache type inherits from ICacheProvider, built-in constants: Lru least recently used algorithm, memory cache, Fifo FIFO algorithm, other inheritance from ICacheProvider cache type, for example: Type="SmartSql.Cache.Redis.RedisCacheProvider, SmartSql .Cache.Redis" |

### Cache subtab

| Label | Description |
| :--------- | --------:|
| FlushInterval | Timed Refresh Strategy |
| FlushOnExecute | Event Triggering Strategy |
| Parameter | As ICacheProvider initialization parameter |

### FlushInterval

| Attribute | Description |
| :--------- | --------:|
| Hours | When |
| Minutes | Points |
| Seconds | Seconds |

### FlushOnExecute

| Attribute | Description |
| :--------- | --------:|
| Statement | Statement that triggers a refresh cache |

### Parameter

| Attribute | Description |
| :--------- | --------:|
| Key | Key |
| Value | Value |
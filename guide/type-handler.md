# SmartSql.TypeHandler

## Nuget Installation

``` cmd
Install-Package SmartSql.TypeHandler -Version 3.0.1
```

SmartSql.TypeHandler includes two types of type handlers: JsonTypeHandler & XmlTypeHandler. When the field type needs to be serialized into a Json or Xml scene, you can use the following configuration to serialize the Data property into a Json string and store it in the DB:

### SmartSqlMapConfig child element

``` xml
   <TypeHandlers>
     <TypeHandler Name="Json" Type="SmartSql.TypeHandler.JsonTypeHandler,SmartSql.TypeHandler"/>
     <TypeHandler Name="Xml" Type="SmartSql.TypeHandler.XmlTypeHandler,SmartSql.TypeHandler"/>
   </TypeHandlers>
```

### SmartSqlMap Child Elements

``` xml
   <ResultMaps>
     <ResultMap Id="ExtendedDataResult">
       <Result Property="Data" Column="Data" TypeHandler="Json"/>
     </ResultMap>
   </ResultMaps>
   <ParameterMaps>
     <ParameterMap Id="ExtendedDataParameter">
       <Parameter Property="Data" TypeHandler="Json"/>
     </ParameterMap>
   </ParameterMaps>
```
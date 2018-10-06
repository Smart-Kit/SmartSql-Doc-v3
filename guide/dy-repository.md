# SmartSql.DyRepository

> SmartSql Dynamic Agent Repository, a highly productive component. This component seems to be difficult to understand, in fact only the mapping statement, the function of forwarding requests. But it is significant.

## SmartSql provides a generic generic Repository interface

> SmartSql.DyRepository.IRepository<TEntity, TPrimary>

``` csharp
    public interface IRepository<TEntity, TPrimary> : IRepository
    {
        int Insert(TEntity entity);
        int Delete(object reqParams);
        [Statement(Id = "Delete")]
        int DeleteById([Param("Id")]TPrimary id);
        int Update(TEntity entity);
        [Statement(Id = "Update")]
        int DyUpdate(object dyObj);
        IEnumerable<TEntity> Query(object reqParams);
        IEnumerable<TEntity> QueryByPage(object reqParams);
        [Statement(Execute = ExecuteBehavior.ExecuteScalar)]
        int GetRecord(object reqParams);
        TEntity GetEntity(object reqParams);
        [Statement(Id = "GetEntity")]
        TEntity GetById([Param("Id")]TPrimary id);
        [Statement(Execute = ExecuteBehavior.ExecuteScalar)]
        bool IsExist(object reqParams);
    }
```

## Define the Repository interface

``` csharp
    /// <summary>
    /// Attribute optional: [SqlMap(Scope = "User")] , not set then default Scope template: I{Scope}Repository
    /// Can pass in a custom template
    /// RepositoryBuilder builder=new RepositoryBuilder("I{Scope}DAL");
    /// </summary>
    public interface IUserRepository : IRepository<User, string>
    {
        /// <summary>
        /// Attribute optional [Statement(Execute = ExecuteBehavior.Auto,Id = "Query")]
        /// Default Execute：Auto ，Automatic judgment execution type
        /// Default Id : Method name
        /// </summary>
        /// <param name="reqParams"></param>
        /// <returns></returns>
        [Statement(Sql = "Select Top(@taken) T.* From User T With(NoLock);")]
        IEnumerable<User> QueryBySql(int taken);
    }
```

## Injection dependency

> After the dependency is injected, the Repository interface will be dynamically implemented and the corresponding SqlMap will be mapped.

``` csharp
    services.AddSmartSql();
    services.AddRepositoryFactory();
    services.AddRepositoryFromAssembly((options) =>
    {
        options.AssemblyString = "SmartSql.Starter.Repository";
    });
```

## Attribute

### Repository interface, function feature（StatementAttribute）

``` csharp
    public class StatementAttribute : Attribute
    {
        /// <summary>
        /// Definition SmartSqlMap.Scope This property is optional. By default, the Scope of the Repository interface is used.
        /// </summary>
        public string Scope { get; set; }
        /// <summary>
        /// Optional, the function name is used as the Statement.Id by default.
        /// </summary>
        public string Id { get; set; }
        /// <summary>
        /// Optional, default Execute:Auto, automatic judgment execution type
        /// </summary>
        public ExecuteBehavior Execute { get; set; } = ExecuteBehavior.Auto;
        /// <summary>
        /// Optional, you can directly define Sql when you do not use SmartSqlMap.Statement
        /// </summary>
        public string Sql { get; set; }
        /// <summary>
        /// Optional, Command type
        /// </summary>
        public CommandType CommandType { get; set; } = CommandType.Text;
        /// <summary>
        /// Optional, data source
        /// </summary>
        public DataSourceChoice SourceChoice { get; set; } = DataSourceChoice.Unknow;
    }
```

``` csharp
    /// <summary>
    /// Execution behavior
    /// </summary>
    public enum ExecuteBehavior
    {
        /// <summary>
        /// Automatically determine the type of execution
        /// </summary>
        Auto = 0,
        /// <summary>
        /// Return the number of affected rows
        /// </summary>
        Execute = 1,
        /// <summary>
        /// Returns the value of the first column of the first row of the result, mainly used to return the primary key
        /// </summary>
        ExecuteScalar = 2,
        /// <summary>
        /// Query enumeration object, List
        /// </summary>
        Query = 3,
        /// <summary>
        /// Query a single object
        /// </summary>
        QuerySingle = 4,
        /// <summary>
        /// Return to the DataTable
        /// </summary>
        GetDataTable = 5,
        /// <summary>
        /// Return to the DataSet
        /// </summary>
        GetDataSet = 6，
        /// <summary>
        /// Return to the  ValueTuple
        /// </summary>
        FillMultiple = 7,
        /// <summary>
        /// Return to the Nested Entity
        /// </summary>
        GetNested = 8
    }
```

### Repository interface feature（SqlMapAttribute）

``` csharp
    public class SqlMapAttribute : Attribute
    {
        /// <summary>
        /// SmartSqlMapConfig.Scope Mapping
        /// </summary>
        public string Scope { get; set; }
    }
```

### Function parameter feature（ParamAttribute）

``` csharp
    public class ParamAttribute : Attribute
    {
        public ParamAttribute(string name = "")
        {
            Name = name;
        }
        /// <summary>
        /// DbDataParameter.Name
        /// </summary>
        public String Name { get; set; }
```
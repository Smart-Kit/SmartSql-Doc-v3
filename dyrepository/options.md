---
seo_title: Dynamic agent Repository configuration
---

# DyRepository configuration

The configuration of DyRepository is divided into default configuration, feature configuration and registration configuration, but all must be configured with IoC registration, because you need to create a dynamic interface implementation to IoC.

## Required configuration:

1. Single registration

``` csharp
    services.AddRepository<IUserRepository>();
```

2. Bulk registration

``` csharp
    services.AddSmartSqlRepositoryFromAssembly((options) =>
    {
        //The full name of the assembly where the Repository interface is located
        options.AssemblyString = "SmartSql.Starter.Repository";
    });
```

## Optional configuration

Feature configuration refers to the configuration of the DyRepository configuration item on the interface, and the registration configuration refers to the configuration in the IoC registration method. The following demonstrates the difference between the two.

### Scope configuration

#### Scenes

I{Scope}Repository is the Scope template of the default configuration, such as the Scope of IUserRepository is User. If it is such an interface naming style, there is no need to configure it.
When you need to change the interface naming style, such as querying the User's Dao layer name is IUserDao, you need to configure the corresponding Scope.

#### Feature configuration

``` csharp
    [SqlMap(Scope = "User")]
    public interface IUserDao
    {
    }
```

#### Registration configuration

``` csharp
    //Interface or that interface
    public interface IUserDao
    {
    }

    //Register a single interface in the IoC configuration
    services.AddRepository<IUserDao>(scope:"User");
    //Or bulk registration
    services.AddSmartSqlRepositoryFromAssembly((options) =>
    {
        options.AssemblyString = "SmartSql.Starter.Repository";
        options.ScopeTemplate = "I{Scope}Dao";
    });
```

Note that AddSmartSqlRepositoryFromAssembly can be configured multiple times, as long as the interface being scanned is different, you can configure different properties for different interfaces.

### SqlId configuration

**SqlId defaults to the method name of the warehousing interface. As long as the method name is the same as the SqlId in the xml, no configuration is required.**

#### Scenes

Because SmartSql's sql configuration can be dynamically rendered, when the same SqlId is passed in different parameters, different query conditions can be rendered. E.g:

``` xml
    <Statement Id="Query">
      SELECT * FROM User T
      <Where>
        <IsNotEmpty Prepend="And" Property="Email">
          T.Email = @Email
        </IsNotEmpty>
        <IsNotEmpty Prepend="And" Property="UserName">
          T.UserName Like Concat('%',$UserName,'%')
        </IsNotEmpty>
      </Where>
    </Statement>
```

At this point, if only the default configuration is used, writing two Query(string) methods will have the same method name as the parameter type and cannot be overloaded. Therefore, the interface method name is required at this time, and the same SqlId is specified by configuration.

#### Feature configuration

``` csharp
    [SqlMap(Scope = "User")]
    public interface IUserRepository
    {
        [Statement(Id = "Query")]
        int QueryByEmail(string email);

        [Statement(Id = "Query")]
        int QueryByUserName(string userName);
    }
```

#### Registration configuration

In the registration configuration, the interface method name to SqlId conversion method is implemented by configuring a delegate parameter called sqlIdNamingConvert.

``` csharp
    services.AddSmartSqlRepositoryFactory(sqlIdNamingConvert: (type, method) =>
    {
        if (method.Name.StartsWith("QueryBy"))
            return "Query"; //Returned is the SqlId
    });
    services.AddRepositoryFromAssembly((options) =>
    {
        options.AssemblyString = "SmartSql.Starter.Repository";
    })
```

It should be noted that this configuration needs to separate the AddSmartSqlRepositoryFactory and AddRepositoryFromAssembly methods, because the AddSmartSqlRepositoryFromAssembly method in the previous configuration calls the AddSmartSqlRepositoryFactory internally, and if it is called again, it will cause a conflict.

### Other configuration

#### Scenes

If you want SmartSql to only do interface implementation without invading the interface, the above registration configuration can basically meet most of the requirements.

But if you need to use SmartSql in depth, you can get more extra features with feature configuration and a generic interface.

#### Interface method specifies Sql

That is, directly bind the sql to the interface method, no need to configure sql from the xml, but please note that the parameter prefix still needs to be configured in the corresponding configuration file.

``` csharp
    [Statement(Sql = "Select Top(@taken) T.* From User T With(NoLock);")]
    IEnumerable<User> QueryBySql(int taken);
```

That is, directly bind the sql to the interface method, no need to configure sql from the xml, but please note that the parameter prefix still needs to be configured in the corresponding configuration file....

| Parameter | Default | Description|
| :---: :---: |:---:|
| Scope | Scope of the current interface | Scope of the corresponding xml |
| Id | Method Name | xml corresponds to the ID of the Statement |
Execute | ExecuteBehavior.Auto|Execution type, generally default is good |
Sql| No | After configuring Sql, it will directly execute Sql on this feature|

#### Specify query parameters

That is, the parameter value of the interface method is passed to the parameter of the specified parameter name when the Sql is rendered, for example, the value of the id is passed to @UserId:

``` csharp
     IEnumerable<User> Query([Param("UserId")]int id);
```

#### Generic interface

After inheriting the generic interface, you can directly call the CURD generic method inside it.

1. Synchronous call: `IRepository<TEntity, TPrimary>`
2. Asynchronous call: `IRepositoryAsync<TEntity, TPrimary>`
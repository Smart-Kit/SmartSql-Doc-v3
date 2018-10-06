---
seo_title: Dynamic Agent Repository Introduction
---

# Dynamic Agent Repository

## Introduction

The Dynamic Repository (SmartSql.DyRepository) component is a very unique feature of SmartSql that simplifies the use of SmartSql. There is almost no intrusion in the configuration of the business code. It can be said that using SmartSqlContainer is the original method, and DyRepository automatically implements these methods for you.

The performance of DyRepository is that you only need to define the warehousing interface. These interfaces can be automatically implemented and registered in the IoC container through simple configuration. The principle is to obtain the Scope and SqlId in the xml file of SmartSql through the naming rules of the interface and the interface method, and use the parameters of the interface method as the Request, and automatically determine whether the query or the execution operation is performed by the sql in the xml, and finally realize the call to the ISmartSqlMapper.

## Suitable for the scene

1. Architecture using Repository mode

The Repository model is mainly used in DDD tactics to isolate domains and databases. The functional requirements of DyRepository are mainly found in the practice of DDD. So far, most of the requirements of DDD practice have been met. If there are other related requirements, we welcome the issue.

2. Applications like SqlHepler

DyRepository can implement any tool to query the database, and the CURD method is no exception. The interface can be more decoupled through interface injection.

## Introduction for using

The following is a simple demonstration of the use of DyRepository and ISmartSqlMapper.

### Ready to work

1. First create a Repository, this warehousing does not depend on SmartSql, just a common warehousing interface

``` csharp
    //Repository interface, the default template is I{Scope}Repository, so the scope of this interface is Activity
    public interface IActivityRepository
    {
        //The interface method corresponds to the SqlId, so the SqlId of this method is Insert.
        //The method parameter corresponds to Request, so the Request of this method is activity
        int Insert(Activity activity);

        //The value type parameter is automatically encapsulated as an object, so the request for this method is new { activityId = activityId }
        Activity Query(long activityId);
    }
```

2. Create a configuration xml file SmartSqlMapConfig.xml：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<SmartSqlMapConfig xmlns="http://SmartSql.net/schemas/SmartSqlMapConfig.xsd">
  <Settings IsWatchConfigFile="true" IgnoreParameterCase="true"/>
  <Database>
    <!--ParameterPrefix:[SqlServer:@ | MySQL:? |Oracle::] -->
    <DbProvider Name="SqlClientFactory" ParameterPrefix="@" Type="System.Data.SqlClient.SqlClientFactory,System.Data.SqlClient"/>
    <Write Name="WriteDB" ConnectionString="Data Source=.;Initial Catalog=SmartSqlStarterDB;Integrated Security=True"/>
  </Database>
  <SmartSqlMaps>
    <SmartSqlMap Path="Maps" Type="Directory"></SmartSqlMap>
  </SmartSqlMaps>
</SmartSqlMapConfig>

```

3. Then create the xml configuration file Activity.xml, put it in the Maps directory, and set it to "Always copy" in the properties panel:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<SmartSqlMap Scope="Activity"  xmlns="http://SmartSql.net/schemas/SmartSqlMap.xsd">
  <Statements>
    <Statement Id="Insert">
      INSERT INTO Activity
      (ActivityId
      ,Name
      ,BeginTime
      ,Address
      ,CreationTime
      ,Deleted)
      VALUES
      (@ActivityId
      ,@Name
      ,@BeginTime
      ,@Address
      ,@CreationTime
      ,@Deleted)
      ;Select Scope_Identity();
    </Statement>

    <Statement Id="Query">
      SELECT * FROM Activity
      WHERE
      ActivityId = @activityId;
    </Statement>
  </Statements>
</SmartSqlMap>
```

The preparation is complete, and the differences between the two usages can be shown below.

### Two usages

#### ISmartSqlMapper usage

If you don't use DyRepository, we need to implement this Repository with ISmartSqlMapper.

``` csharp

    public class ActivityRepository : IActivityRepository
    {
        ISmartSqlMapper SqlMapper = MapperContainer.Instance.GetSqlMapper();

        public int Insert(Activity activity)
        {
            return SqlMapper.ExecuteScalar<int>(new RequestContext()
            {
                Scope = "Activity",
                SqlId = "Insert",
                Request = activity
            });
        }

        public Activity Query(long activityId)
        {
            return SqlMapper.Query<Activity>(new RequestContext()
            {
                Scope = "Activity",
                SqlId = "Query",
                Request = new { activityId = activityId }
            });
        }
    }
```

Then register the implementation class to the IoC:

``` csharp
var services = new ServiceCollection();
var services.AddSingleton<IActivityRepository,ActivityRepository>();
```

#### DyRepository

If you use DyRepository, we only need to configure IoC registration.

``` csharp
    var services = new ServiceCollection();
    services.AddSmartSqlRepositoryFromAssembly((options) =>
    {
        options.AssemblyString = "SmartSql.Starter.Repository";
    });
```

#### Injection use

Use the method to inject the interface, and then call the interface method.

``` csharp
    // Assume that the ActivityService is already registered to the IoC container.
    public class ActivityService
    {
        IActivityRepository activityRepository;

        public ActivityService(IActivityRepository activityRepository)
        {
            this.activityRepository = activityRepository;
        }

        public int Create(Activity activity)
        {
            return activityRepository.Insert(activity);
        }

        public int GetById(int id)
        {
            return activityRepository.Insert(id);
        }
    }

```

## summary

Through the simple comparison between DyRepository and ISmartSqlMapper, we can see that the power of DyRepository saves us a lot of code. Of course, ISmartSqlMapper naturally has its flexibility to be used anywhere. But if there are no other special requirements, in terms of architecture, DyRepository is undoubtedly the most recommended way to use because there is almost no intrusion into the business code.

This article only introduces the use of the DyRepository default convention, in fact, it can also achieve more flexible functions through various configuration items. See the next article, DyRepository Configuration for details.
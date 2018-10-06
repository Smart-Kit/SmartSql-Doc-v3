# Quick use

## ISmartSqlMapper Common (Partial) Interface Overview

| Function | Description |
| :--------- | --------:|
Execute | IDbCommand.ExecuteNonQuery, execution returns the number of affected rows |
ExecuteScalar | IDbCommand.ExecuteScalar, execute and return the first column of the first row in the ReultSet returned by the query |
| Query | Execution Returns Entity List |
| QuerySingle | Execution returns a single entity |
| GetDataTable | Execute returns DataTable |
| GetDataSet | Execute returns DataSet |
| BeginTransaction | Open transaction |
| CommitTransaction | Submit a transaction |
| RollbackTransaction | Rollback Transaction |

## Insert

### Statement

``` xml
    <Statement Id="Insert">
      INSERT INTO T_User
      (UserName
      ,Password
      ,Status
      ,LastLoginTime
      ,CreationTime)
      VALUES
      (?UserName
      ,?Password
      ,?Status
      ,?LastLoginTime
      ,?CreationTime)
      ;Select Last_Insert_Id();
    </Statement>
```

### Return primary key

``` csharp

            ISmartSqlMapper SqlMapper = MapperContainer.Instance.GetSqlMapper();
            long userId = _smartSqlMapper.ExecuteScalar<long>(new RequestContext
            {
                Scope = "User",
                SqlId = "Insert",
                Request = new User
                {
                    UserName = request.UserName,
                    Pwd = request.Pwd,
                    Status = Entitiy.UserStatus.Ok,
                    CreationTime = DateTime.Now,
                }
            });
```

### Return number of affected rows

``` csharp
            SqlMapper.Execute(new RequestContext
            {
                Scope = "User",
                SqlId = "Insert",
                Request = new User
                {
                    UserName = request.UserName,
                    Pwd = request.Pwd,
                    Status = Entitiy.UserStatus.Ok,
                    CreationTime = DateTime.Now,
                }
            });
```

## Delete

``` xml
    <Statement Id="Delete">
      Delete FROM  T_User
      Where Id=?Id
    </Statement>
```

``` csharp
            SqlMapper.Execute(new RequestContext
            {
                Scope = "User",
                SqlId = "Delete",
                Request = new { Id = 3 }
            });
```

## Update

### Statement.Update

``` xml
<Statement Id="Update">
      UPDATE T_User
      <Set>
        <IsProperty Prepend="," Property="UserName">
          UserName = ?UserName
        </IsProperty>
        <IsProperty Prepend="," Property="Password">
          Password = ?Password
        </IsProperty>
        <IsProperty Prepend="," Property="Status">
          Status = ?Status
        </IsProperty>
        <IsProperty Prepend="," Property="LastLoginTime">
          LastLoginTime = ?LastLoginTime
        </IsProperty>
        <IsProperty Prepend="," Property="CreationTime">
          CreationTime = ?CreationTime
        </IsProperty>
      </Set>
      Where Id=?Id
    </Statement>
```

### Full update

``` csharp
            SqlMapper.Execute(new RequestContext
            {
                Scope = "User",
                SqlId = "Update",
                Request = new User
                {
                    Id=1,
                    UserName = request.UserName,
                    Pwd = request.Pwd,
                    Status = Entitiy.UserStatus.Ok,
                    CreationTime = DateTime.Now,
                }
            });
```

### Partial update

``` csharp
            SqlMapper.Execute(new RequestContext
            {
                Scope = "User",
                SqlId = "Update",
                Request = new { Id=1 , Pwd = "SmartSql" }
            });
```

## Query return List

``` xml
<Statement Id="Query">
      SELECT T.* From T_User T
      <Where>
        <IsNotEmpty Prepend="And" Property="EqUserName">
          T.UserName=$EqUserName
        </IsNotEmpty>
        <IsNotEmpty Prepend="And" Property="UserName">
          T.UserName Like Concat('%',$UserName,'%')
        </IsNotEmpty>
      </Where>
      <Switch Prepend="Order By" Property="OrderBy">
        <Default>
          T.Id Desc
        </Default>
      </Switch>
      <IsNotEmpty Prepend="Limit" Property="Taken">?Taken</IsNotEmpty>
    </Statement>
```

``` csharp
            var list = SqlMapper.Query<User>(new RequestContext
            {
                Scope = "User",
                SqlId = "Query",
                Request = new
                {
                    Taken = 10
                }
            });

```

## Query returns a single entity

``` xml
    <Statement Id="GetEntity">
      Select T.* From T_User T
      <Where>
        <IsNotEmpty Prepend="And" Property="Id">
          T.Id=?Id
        </IsNotEmpty>
      </Where>
      Limit 1
    </Statement>
```

``` csharp
            var user = SqlMapper.QuerySingle<User>(new RequestContext
            {
                Scope = "User",
                SqlId = "GetEntity",
                Request = new { Id = 1 }
            });
```

## Transaction

``` csharp
            try
            {
                SqlMapper.BeginTransaction();
                //BizCode();
                SqlMapper.CommitTransaction();
            }
            catch (Exception ex)
            {
                SqlMapper.RollbackTransaction();
                throw ex;
            }
```

## Stored procedure

``` csharp
            DbParameterCollection dbParameterCollection = new DbParameterCollection();
            dbParameterCollection.Add(new DbParameter
            {
                Name = "Total",
                DbType = System.Data.DbType.Int32,
                Direction = System.Data.ParameterDirection.Output
            });
            RequestContext context = new RequestContext
            {
                CommandType = System.Data.CommandType.StoredProcedure,
                RealSql = "SP_QueryByPage",
                Request = dbParameterCollection
            };
            var list = SqlMapper.Query<User>(context);
            var total = dbParameterCollection.GetValue<int>("Total");
```
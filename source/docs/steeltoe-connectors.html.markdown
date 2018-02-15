---
title: Service Connectors
order: 40
date: 2018/1/22
tags:
---
<span style="display:inline-block;margin:0 20px;">For use with </span><span style="display:inline-block;vertical-align:top;width:40%"> ![alt text](/images/CFF_Logo_rgb.png "Cloud Foundry")</span>

Steeltoe Connectors simplify the process of connecting and using services on Cloud Foundry. Steeltoe Connectors provide a simple abstraction for .NET based applications running on Cloud Foundry, letting them discover bound services and deployment information at runtime. The connectors also provide support for registering the services as injectable service objects.

The Steeltoe Connectors provide out-of-the-box support for discovering many common services on Cloud Foundry. They also include the ability to use settings-based configuration so that developers can supply configuration settings at development and testing time but then have those settings be overridden when pushing the application to Cloud Foundry.

All connectors use configuration information from Cloud Foundry's `VCAP_SERVICES` environment variable to detect and configure the available services. This a Cloud Foundry standard that is used to hold connection and identification information for all service instances that have been bound to Cloud Foundry applications.

For more information on `VCAP_SERVICES`, see the Cloud Foundry [documentation](https://docs.cloudfoundry.org/).

# 0.0 Initialize Dev Environment

All of the Steeltoe sample applications are in the same repository. If you have not already done so, use git to clone the [Steeltoe samples](https://github.com/SteeltoeOSS/Samples) repository or download it with your browser from GitHub. You can run the following git command to clone the repository from the command line:

```bash
> git clone https://github.com/SteeltoeOSS/Samples.git
```

>NOTE: All connector samples in that repository have a base path of `Samples/Connectors/src/`

Make sure your Cloud Foundry CLI tools are logged in and targeting the correct org and space, as follows:

```bash
> cf login [-a API_URL] [-u USERNAME] [-p PASSWORD] [-o ORG] [-s SPACE] [--skip-ssl-validation]
```

or

```bash
> cf target -o <YourOrg> -s <YourSpace>
```

# 1.0 MySQL

This connector simplifies using MySql ADO.NET providers in an application running on Cloud Foundry.

Currently, the connector supports the following providers:

* [Connector/NET](https://dev.mysql.com/doc/connector-net/en/)
* [MySqlConnector](https://mysql-net.github.io/MySqlConnector/)

In addition to the [Quick Start](#1-1-quick-start), you can refer to several other Steeltoe sample applications to help you understand how to use this connector:

* [AspDotNet4/MySql4](https://github.com/SteeltoeOSS/Samples/tree/master/Connectors/src/AspDotNet4/MySql4): Same as the next Quick Start but built for ASP.NET 4.x.
* [MusicStore](https://github.com/SteeltoeOSS/Samples/tree/master/MusicStore): A sample app showing how to use all of the Steeltoe components together in a ASP.NET Core application. This is a micro-services based application built from the ASP.NET Core MusicStore reference app provided by Microsoft.
* [FreddysBBQ](https://github.com/SteeltoeOSS/Samples/tree/master/FreddysBBQ): A polyglot (Java and .NET) micro-services based sample application showing interoperability between Java and .NET based micro-services running on Cloud Foundry, secured with OAuth2 Security Services, and using Spring Cloud Services.

The source code for this connector can be found [here](https://github.com/SteeltoeOSS/Connectors).

## 1.1 Quick Start

This quick start consists of using several ASP.NET Core sample applications that show how to use the Steeltoe MySql Connector for connecting to a MySql service on Cloud Foundry.

There are three sample applications you can choose from for this quick start:

* AspDotNetCore/MySql: Uses a `MySqlConnection` to issue commands to the bound database.
* AspDotNetCore/MySqlEF6: Uses an Entity Framework 6 `DbContext` to access the bound database.
* AspDotNetCore/MySqlEFCore: Uses a Entity Framework Core `DbContext` to access the bound database.

### 1.1.1 Locate Sample

Depending on your specific interests, pick one of the following samples to work with going forward:

* MySqlConnection sample: Change directory to `Samples/Connectors/src/AspDotNetCore/MySql`.
* Entity Framework 6 `DbContext` sample: Change directory to `Samples/Connectors/src/AspDotNetCore/MySqlEF6`.
* Entity Framework Core `DbContext` sample: Change directory to `Samples/Connectors/src/AspDotNetCore/MySqlEFCore`.

### 1.1.2 Create Service

In this step, we use the Cloud Foundry CLI to create a service instance of MySql on Cloud Foundry.

The commands in this section assume you are using the MySql service provided by Pivotal on Cloud Foundry. If you are using a different service, adjust the `create-service` command below to fit your environment.

```bash
> # Create a MySql service instance on Cloud Foundry
> cf create-service p-mysql 100mb myMySqlService
>
> # Make sure the service is ready
> cf services
```

### 1.1.3 Publish and Push the Sample

See [Publish Sample](#publish-sample) and the sections that follow for instructions on how to publish and push this sample to either Linux or Windows.

### 1.1.4 Observe Logs

Optionally, you can use the `cf logs` command to see log output.

### 1.1.5 View Data

At this point, the application is up and running. On startup, it inserts a couple of rows into the bound MySQL database.

To display those rows, click the "MySql Data" link in the top menu and you should see the row data displayed.

### 1.1.6 Understand the Sample

Each of the samples were created by using the .NET Core tooling `mvc` template (`dotnet new mvc`) and then modified to add the Steeltoe framework.

To understand the Steeltoe related changes to the generated template code, examine the following files:

* `*.csproj` files: Contain the `PackageReference` for the Steeltoe NuGet Connector and the Configuration packages. Also, a `PackageReference` for Oracle's MySql provider, `MySql.Data`, has been added. If Entity Framework has been used, you see references to those packages as well.
* `Program.cs`: Added `.UseCloudFoundryHosting()` for dynamic port binding and `.ConfigureAppConfiguration( -> .AddCloudFoundry()` to read `VCAP_SERVICES` when pushed to Cloud Foundry.
* `Startup.cs`: Code added to the `ConfigureServices()` method to add a `MySqlConnection` or a `DbContext`, depending on the application, to the service container.
* `HomeController.cs`: Code added for injection of a `MySqlConnection` or `DbContext` into the Controller. These are used to obtain data from the database and then to display the data.
* `MySqlData.cshtml`: The view used to display the MySQL data values.
* `Models folder`: Contains code to initialize the database and the definition of `DbContext` classes for the MySqlEF6 and MySqlEFCore samples.

## 1.2 Usage

You should know how the new .NET [Configuration service](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration) works before starting to use the connector. A basic understanding of the `ConfigurationBuilder` and how to add providers to the builder is necessary in order to configure the connector.

You should also know how the ASP.NET Core [Startup](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/startup) class is used in configuring the application services for the app. Pay particular attention to the usage of the `ConfigureServices()` method.

To use this connector:

1. Create a MySQL Service instance and bind it to your application.
1. Optionally, configure any MySql client settings (such as `appsettings.json`) you need.
1. Add the Steeltoe Cloud Foundry configuration provider to your `ConfigurationBuilder`.
1. Add `MySqlConnection` or `DbContext` to your `IServiceCollection`.

### 1.2.1 Add NuGet Reference

To use this connector, add a reference to one of the Steeltoe connector NuGet packages. The following table describes the available packages:

|App Type|ORM|Package|
|---|---|---|
|ASP.NET Core|Entity Framework 6|`Steeltoe.CloudFoundry.Connector.EF6Core`|
|ASP.NET Core|Entity Framework Core|`Steeltoe.CloudFoundry.Connector.EFCore`|
|ASP.NET Core|Other|`Steeltoe.CloudFoundry.ConnectorCore`|
|Other|Other|`Steeltoe.CloudFoundry.ConnectorBase`|

Use the Nuget package manager tools or directly add the appropriate package to your project by using the a `PackageReference`, as shown in the following listing:

```xml
<ItemGroup>
...
    <PackageReference Include="Steeltoe.CloudFoundry.ConnectorBase" Version= "2.0.0"/>
...
</ItemGroup>
```

In addition to the `packageReference`, you need a MySql-specific package: `MySql.Data`, `MySqlConnector`, or `Pomelo.EntityFrameworkCore.MySql`.

### 1.2.2 Configure Settings

The MySql connector supports a variety of configuration options. You can use these settings to develop or test an application locally and override them during deployment.

The following MySQL connector configuration shows how to connect to a database at `myserver:3306`:

```json
{
  ...
  "mysql": {
    "client": {
      "server": "myserver",
      "port": 3306
    }
  }
  ...
}
```

The following table describes the available settings for the connector. These settings are not specific to Steeltoe. They are passed through to the underlying data provider. See the [Oracle MySQL Connection String docs](https://dev.mysql.com/doc/connector-net/en/connector-net-connection-options.html) or [open source MySQL Connection String docs](https://mysql-net.github.io/MySqlConnector/connection-options/).

|Key|Description|Steeltoe Default|
|---|---|:---:|
|server|Hostname or IP Address of the server.|localhost|
|port|Port number of server.|3306|
|username|Username for authentication.|not set|
|password|Password for authentication.|not set|
|database|Schema to which to connect.|not set|
|connectionString|Full connection string.|built from settings|
|sslMode|SSL usage option. One of `None`, `Preferred`, or `Required`.|`None`|
|allowPublicKeyRetrieval|Whether RSA public keys should be retrieved from the server.|not set|
|allowUserVariables|Whether the provider expects user variables in the SQL.|not set|
|connectionTimeout|Seconds to wait for a connection before throwing an error.|not set|
|connectionLifeTime|The maximum length of time a connection to the server can be open.|not set|
|connectionReset|Whether the connection state is reset when it is retrieved from the pool.|not set|
|convertZeroDateTime|Whether to have MySqlDataReader.GetValue() and MySqlDataReader.GetDateTime() return DateTime.MinValue for date or datetime columns that have disallowed values.|not set|
|defaultCommandTimeout|Seconds each command can execute before timing out. Use 0 to disable timeouts.|not set|
|keepalive|TCP keep-alive idle time.|not set|
|maximumPoolsize|Maximum number of connections allowed in the pool.|not set|
|minimumPoolsize|Minimum number of connections to leave in the pool if ConnectionIdleTimeout is reached.|not set|
|oldGuids|Whether to use a GUID of data type BINARY(16).|not set|
|persistSecurityInfo|Whether to allow the application to access to security-sensitive information, such as the password. **_(Not recommended)_**.|not set|
|pooling|Enables connection pooling.|not set|
|treatTinyAsBoolean|Whether to return tinyint(1) as a boolean. Set to `false` to return tinyint(1) as sbyte/byte.|not set|
|useAffectedRows|Set to `false` to report found rows instead of changed (affected) rows.|not set|
|useCompression|If `true` (and server-supported), packets sent between client and server are compressed|not set|

>IMPORTANT: All of the settings described in the preceding table should be prefixed with `mysql:client:`.

The samples and most templates are already set up to read from `appsettings.json`. See [Reading Configuration Values](#reading-configuration-values).

### 1.2.3 Cloud Foundry

To use MySQL on Cloud Foundry, you can create and bind an instance of MySql to your application by using the Cloud Foundry CLI, as follows:

```bash
> # Create MySql service
> cf create-service p-mysql 100mb myMySqlService
>
> # Bind service to `myApp`
> cf bind-service myApp myMySqlService
>
> # Restage the app to pick up change
> cf restage myApp
```

>NOTE: The preceding commands assume you use [MySql for PCF](https://network.pivotal.io/products/p-mysql), provided by Pivotal on Cloud Foundry. If you use a different service, you must adjust the `create-service` command to fit your environment.

Once the service is bound to your application, the connector's settings are available in `VCAP_SERVICES`. See [Reading Configuration Values](#reading-configuration-values).

### 1.2.4 Add MySqlConnection

To use a `MySqlConnection` in your application, add it to the service container in the `ConfigureServices()` method of the `Startup` class, as shown in the following example:

```csharp
using Steeltoe.CloudFoundry.Connector.MySql;

public class Startup {
    ...
    public IConfiguration Configuration { get; private set; }
    public Startup(...)
    {
      ...
    }
    public void ConfigureServices(IServiceCollection services)
    {
        // Add MySqlConnection configured from Configuration
        services.AddMySqlConnection(Configuration);

        // Add framework services.
        services.AddMvc();
        ...
    }
    ...
```

The `AddMySqlConnection(Configuration)` method call configures the `MySqlConnection` by using the configuration built by the application and adds the connection to the service container.

### 1.2.5 Use MySqlConnection

Once you have configured and added the connection to the service container, it is trivial to inject and use in a controller or a view, as shown in the following example:

```csharp
using MySql.Data.MySqlClient;
...
public class HomeController : Controller
{
    public IActionResult MySqlData([FromServices] MySqlConnection dbConnection)
    {
        dbConnection.Open();

        MySqlCommand cmd = new MySqlCommand("SELECT * FROM TestData;", dbConnection);
        MySqlDataReader rdr = cmd.ExecuteReader();

        while (rdr.Read())
        {
            ViewData["Key" + rdr[0]] = rdr[1];
        }

        rdr.Close();
        dbConnection.Close();

        return View();
    }
}
```

### 1.2.6 Add DbContext

To use Entity Framework, inject and use a `DbContext` in your application (instead of a `MySqlConnection`) by using the `AddDbContext<>()` method, as shown in the following example:

```csharp
using Steeltoe.CloudFoundry.Connector.MySql.EFCore;
// OR
using Steeltoe.CloudFoundry.Connector.MySql.EF6;

public class Startup {
    ...
    public IConfiguration Configuration { get; private set; }
    public Startup(...)
    {
      ...
    }
    public void ConfigureServices(IServiceCollection services)
    {
        // If using EF6
        services.AddDbContext<TestContext>(Configuration);

        // If using EFCore
        services.AddDbContext<TestContext>(options => options.UseMySql(Configuration));

        // Add framework services.
        services.AddMvc();
        ...
    }
    ...
```

The `AddDbContext<TestContext>(..)` method call configures `TestContext` by using the configuration built earlier and then adds the DbContext (called `TestContext`) to the service container.

You can define your `DbContext` differently, depending on whether you use Entity Framework 6 or Entity Framework Core.

The following example uses Entity Framework 6:

```csharp
// ------- EF6 DbContext ---------
using MySql.Data.Entity;
using System.Data.Entity;
...

[DbConfigurationType(typeof(MySqlEFConfiguration))]
public class TestContext : DbContext
{
    public TestContext(string connectionString) : base(connectionString)
    {
    }
    public DbSet<TestData> TestData { get; set; }
}
```

The following example uses Entity Framework Core:

```csharp
// ------- EFCore DbContext ------
using Microsoft.EntityFrameworkCore;
...

public class TestContext : DbContext
{
    public TestContext(DbContextOptions options) : base(options)
    {

    }
    public DbSet<TestData> TestData { get; set; }
}

```

### 1.2.7 Use DbContext

Once you have configured and added the DbContext to the service container, inject and use it in a controller or a view, as shown in the following example:

```csharp
using Project.Models;
...
public class HomeController : Controller
{
    public IActionResult MySqlData([FromServices] TestContext context)
    {
        return View(context.TestData.ToList());
    }
```

# 2.0 PostgreSQL

This connector simplifies using PostgreSQL in an application running on Cloud Foundry.

Currently, the connector supports the [Npgsql](http://www.npgsql.org/) provider.

You can find the source code for this connector [here](https://github.com/SteeltoeOSS/Connectors).

## 2.1 Quick Start

This quick start consists of several ASP.NET Core sample applications that show how to use the Steeltoe PostgreSQL Connector to connect to a PostgreSQL service on Cloud Foundry.

You can choose from two sample applications:

* PostgreSql: Shows how to use a `NpgsqlConnection` to issue commands to the bound database.
* PostgreEFCore: Shows how to use an Entity Framework Core `DbContext` to access the bound database.

### 2.1.1 Locate Sample

Depending on your specific interests, pick one of the following samples to work with going forward:

```bash
> # Use a `NpgsqlConnection`
> cd Samples/Connectors/src/AspDotNetCore/PostgreSql
```

```bash
> # Use a Entity Framework Core `DbContext`
> cd Samples/Connectors/src/AspDotNetCore/PostgreEFCore
```

### 2.1.2 Create Service

Now you can use the Cloud Foundry CLI to create a service instance of PostgreSQL on Cloud Foundry.

The commands in this section assume you use the EDB PostgreSQL service on Cloud Foundry.

If you use a different service, adjust the `create-service` command to fit environment.

To create the service and ensure that it is ready, use the following commands:

```bash
> # Create a PostgreSQL service instance on Cloud Foundry
> cf create-service EDB-Shared-PostgreSQL "Basic PostgreSQL Plan" myPostgres
>
> # Make sure the service is ready
> cf services
```

### 2.1.3 Publish and Push Sample

See [Publish Sample](#publish-sample) for instructions on how to publish and push this sample to either Linux or Windows.

### 2.1.4 Observe Logs

Optionally, you can use the `cf logs` command to see log output.

### 2.1.4 What to expect

At this point, the app is up and running. On startup, it inserts a couple of rows into the bound PostgreSQL database.

To display those rows, click on the `Postgres Data` link in the top menu.

### 2.1.5 Understand Sample

Each of the samples were created from the .NET Core tooling `mvc` template (`dotnet new mvc`) and then modified to include the Steeltoe framework.

To gain an understanding of the Steeltoe related changes to the generated template code, examine the following files:

* `PostgreSql.csproj`: Contains a `PackageReference` for the Steeltoe NuGet `Steeltoe.CloudFoundry.ConnectorCore`.
* `PostgreEFCore.csproj`: Contains a `PackageReference` for Steeltoe NuGet `Steeltoe.CloudFoundry.Connector.EFCore`.
* `Program.cs`: Added `.UseCloudFoundryHosting()` for dynamic port binding and `.ConfigureAppConfiguration( -> .AddCloudFoundry()` to read `VCAP_SERVICES` when pushed to Cloud Foundry.
* `Startup.cs`: Code added to the `ConfigureServices()` method to add a `NpgsqlConnection` or a `DbContext`, depending on the application, to the service container.
* `HomeController.cs`: Code added to inject a `NpgsqlConnection` or `DbContext`, depending on the application, into the Controller and obtain data from the database for the view.
* `PostgresData.cshtml`: The view used to display the PostgreSQL data values.
* `Models folder`: Contains code to initialize the database and the `DbContext` for the PostgreEFCore sample.

## 2.2 Usage

You should know how the new .NET [Configuration service](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration) works before starting to use the connector. You need a basic understanding of the `ConfigurationBuilder` and how to add providers to the builder to configure the connector.

You should also know how the ASP.NET Core [Startup](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/startup) class is used to configure the application services for the app. Pay particular attention to the `ConfigureServices()` method.

To use this connector:

1. Create a PostgreSQL Service instance and bind it to your application.
1. Optionally, configure any PostgreSQL client settings (such as `appsettings.json`).
1. Add the Steeltoe Cloud Foundry config provider to your `ConfigurationBuilder`.
1. Add `NpgsqlConnection` or `DbContext` to your `IServiceCollection`.

### 2.2.1 Add NuGet Reference

To use this connector, add a reference to one of the Steeltoe connector NuGet packages. The following table describes the NuGet packages:

|App Type | ORM | Package |
|--- | --- | --- |
|ASP.NET Core | Entity Framework 6 | `Steeltoe.CloudFoundry.Connector.EF6Core` |
|ASP.NET Core | Entity Framework Core | `Steeltoe.CloudFoundry.Connector.EFCore` |
|ASP.NET Core | Other | `Steeltoe.CloudFoundry.ConnectorCore`
|Other | Other | `Steeltoe.CloudFoundry.ConnectorBase`

Use the Nuget package manager tools or directly add the appropriate package to your project by using the a `PackageReference`, as follows:

```xml
<ItemGroup>
...
    <PackageReference Include="Steeltoe.CloudFoundry.ConnectorBase" Version= "2.0.0"/>
...
</ItemGroup>
```

In addition to the preceding package, you also need to add a PostgreSQL package reference to your application, as you would if you were not using Cloud Foundry (for example, `Npgsql` or `Npgsql.EntityFrameworkCore.PostgreSQL`).

### 2.2.2 Configure Settings

The PostgreSQL connector supports several settings for creating the `NpgsqlConnection` to a database. This can be useful when you develop and test an application locally and need to configure the connector for non-default settings.

The following example shows a PostgreSQL connector configuration (in JSON) to set up a connection to a database at `myserver:5432`:

```json
{
  ...
  "postgres": {
    "client": {
      "host": "myserver",
      "port": 5432
    }
  }
  ...
}
```

The following table describes all of the possible settings for the connector:

|Key|Description|Default
|---|---|---|
|server|Hostname or IP Address of server|localhost|
|port|Port number of server|5432|
|username|Username for authentication|not set|
|password|Password for authentication|not set|
|database|Schema to which to connect|not set|
|connectionString|Full connection string|built from settings

>IMPORTANT: All of these settings should be prefixed with `postgres:client:`.

The samples and most templates are already set up to read from `appsettings.json`. See [Reading Configuration Values](#reading-configuration-values).

### 2.2.3 Cloud Foundry

To use PostgreSQL on Cloud Foundry, after a PostgreSQL service is installed, you can create and bind an instance of it to your application by using the Cloud Foundry CLI, as follows:

```bash
> # Create PostgreSQL service
> cf create-service EDB-Shared-PostgreSQL "Basic PostgreSQL Plan" myPostgres
>
> # Bind service to `myApp`
> cf bind-service myApp myPostgres
>
> # Restage the app to pick up change
> cf restage myApp
```

>NOTE: The preceding commands work for the PostgreSQL service provided by EDB on Cloud Foundry. For another service, adjust the `create-service` command to fit your environment.

Once the service is bound to your application, the connector's settings are available in `VCAP_SERVICES`. See [Reading Configuration Values](#reading-configuration-values).

### 2.2.4 Add NpgsqlConnection

To use a `NpgsqlConnection` in your application, add it to the service container in the `ConfigureServices()` method of the `Startup` class, as shown in the following example:

 ```csharp
 using Steeltoe.CloudFoundry.Connector.PostgreSql;

 public class Startup {
     ...
     public IConfiguration Configuration { get; private set; }
     public Startup(...)
     {
       ...
     }
     public void ConfigureServices(IServiceCollection services)
     {
         // Add NpgsqlConnection configured from Cloud Foundry
         services.AddPostgresConnection(Configuration);

         // Add framework services.
         services.AddMvc();
         ...
     }
     ...
 ```

The `AddPostgresConnection(Configuration)` method call configures the `NpgsqlConnection` by using the configuration built by the application and adds the connection to the service container.

### 2.2.5 Use NpgsqlConnection

Once the connection is configured and added to the service container, you can inject and use in a controller or a view, as shown in the following example:

```csharp
using Npgsql;
...
public class HomeController : Controller
{
    public IActionResult PostgresData([FromServices] NpgsqlConnection dbConnection)
    {
        dbConnection.Open();

        NpgsqlCommand cmd = new NpgsqlCommand("SELECT * FROM TestData;", dbConnection);
        var rdr = cmd.ExecuteReader();

        while (rdr.Read())
        {
            ViewData["Key" + rdr[0]] = rdr[1];
        }

        rdr.Close();
        dbConnection.Close();

        return View();
    }
}

```

### 2.2.6 Add DbContext

To use Entity Framework, inject and use a `DbContext` in your application instead of a `NpgsqlConnection` through the `AddDbContext<>()` method, as shown in the following example:

```csharp
using Steeltoe.CloudFoundry.Connector.PostgreSql.EFCore;

public class Startup {
    public IConfiguration Configuration { get; private set; }
    public Startup(...)
    {
      ...
    }
    public void ConfigureServices(IServiceCollection services)
    {
        // Add EFCore TestContext configured with a PostgreSQL configuration
        services.AddDbContext<TestContext>(options => options.UseNpgsql(Configuration));

        // Add framework services.
        services.AddMvc();
        ...
    }
```

The `AddDbContext<TestContext>(options => options.UseNpgsql(Configuration));` method call configures the `TestContext` by using the configuration built by the application and adds the context to the service container.

The following example shows how you would define the `DbContext`:

```csharp
using Microsoft.EntityFrameworkCore;
...

public class TestContext : DbContext
{
    public TestContext(DbContextOptions options) : base(options)
    {
    }
    public DbSet<TestData> TestData { get; set; }
}
```

### 2.2.7 Use DbContext

Once you have configured and added the context to the service container, you can inject and use it in a controller or a view, as shown in the following example:

```csharp
using Project.Models;
...
public class HomeController : Controller
{
    public IActionResult PostgresData([FromServices] TestContext context)
    {
        return View(context.TestData.ToList());
    }
}
```

# 3.0 Microsoft SQL Server

This connector simplifies using Microsoft SQL Server in an application running on Cloud Foundry. The connector is built to work with `System.Data.SqlClient` and provides additional extension methods for using Entity Framework.

The source code for this connector can be found [here](https://github.com/SteeltoeOSS/Connectors).

## 3.1 Quick Start

This quick start consists of several ASP.NET sample applications to show how to use the Steeltoe SQL Server Connector for connecting to a SQL server from an application running on Cloud Foundry.

There are two sample applications to choose from:

* AspDotNet4/MsSql4: Using MVC5 and Entity Framework to issue commands to the bound database.
* AspDotNetCore/SqlServerEFCore: Using ASP.NET Core and Entity Framework Core to access the bound database.

### 3.1.1 Locate Sample

Depending on your specific interests, pick one of the following samples to work with going forward:

```bash
> # Use a .NET4/EF6 sample
> cd Samples/Connectors/src/AspDotNet4/MsSql4
```

or

```bash
> # Use a .NETCORE/EFCore sample
> cd Samples/Connectors/src/AspDotNetCore/SqlServerEFCore
```

### 3.1.2 Create Service

If the [Microsoft SQL Server broker](https://github.com/cf-platform-eng/mssql-server-broker) is installed in your Cloud Foundry instance, you can use it to create a new service instance, as follows:

```bash
> cf create-service SqlServer sharedVM mySqlServerService
```

An alternative to the broker is to use a user-provided service to explicitly provide connection information to the application, as follows:

```bash
> cf cups mySqlServerService -p '{"pw": "|password|","uid": "|user id|","uri": "jdbc:sqlserver://|host|:|port|;databaseName=|database name|"}'
```

### 3.1.3 Publish Sample

#### 3.1.3.1 Publish ASP.NET Core

You can use the `dotnet` CLI to build and publish the application.

>NOTE: Not all quick start samples can be built to run on all frameworks and run-times.

For example, the Entity Framework 6 DbContext sample can run only on Windows and on the .NET Framework, and the Entity Framework Core DbContext sample can run only on .NET Core. To get started, run the following command:

```bash
> dotnet restore --configfile nuget.config
```

Then run one of the following commands:

* Publish for Linux, .NET Core: `dotnet publish -f netcoreapp2.0 -r ubuntu.14.04-x64`
* Publish for Windows, .NET Core: `dotnet publish -f netcoreapp2.0 -r win10-x64`
* Publish for Windows, .NET Framework: `dotnet publish -f net461 -r win10-x64`

#### 3.1.3.2 Publish ASP.NET 4.x

To publish ASP.NET 4.x applications, you can use the Visual Studio publishing tools, as follows:

1. Select MsSql4 project in the Solution Explorer.
1. Right click and select Publish.
1. Select the `FolderProfile` profile (if this profile is missing, create a profile that publishes to a local folder: `bin/Debug/net461/win10-x64/publish`).
1. Click Publish.

### 3.1.4 Push Samples

See [Common References](#push-sample) for instructions on how to push this sample to either Linux or Windows.

### 3.1.5 Observe Logs

You can use the `cf logs` command to see log output.

### 3.1.6 View Data

At this point, the application is up and running. On startup, it inserts two rows into the bound Microsoft SQL database.

Loading the home page of the application shows those rows.

### 3.1.7 Understand Samples

The [Steeltoe samples](https://github.com/SteeltoeOSS/Samples) include two samples for Microsoft SQL Server:

* [An ASP.NET Core sample](#3-1-7-1-asp.net-core-sample)
* [An ASP.NET 4.x sample](#3-1-7-2-asp.net-4.x-sample)

#### 3.1.7.1 ASP.NET Core Sample

This sample was created from the .NET Core tooling mvc template (`dotnet new mvc`) and then modified to include the Steeltoe framework.

To understand the Steeltoe related changes to the generated template code, examine the following files:

* `*.csproj`: Contains the `PackageReference` for the Steeltoe NuGet Connector and the Entity Framework.
* `Program.cs`: Added `.UseCloudFoundryHosting()` for dynamic port binding and `.ConfigureAppConfiguration( -> .AddCloudFoundry()` to read `VCAP_SERVICES` when pushed to Cloud Foundry.
* `Startup.cs`: Code added to the `ConfigureServices()` method to add a `DbContext` to the service container.
* `HomeController.cs`: Code added for injection of a `TestContext` into the Controller to obtain data from the database and then to display the data.
* `Index.cshtml`: The view used to display the data values from SQL Server.
* `Models folder`: Contains code to initialize the database and also the definition of the `DbContext` class.

#### 3.1.7.2 ASP.NET 4.x Sample

This sample was created with the standard Visual Studio template (File -> New Project) and then modified to add the Steeltoe framework.

To understand the Steeltoe related changes to the generated template code, examine the following files:

* `packages.config`: Contains references to the Steeltoe Common, Connector, and Configuration packages along with Entity Framework and StructureMap.
* `DependencyResolution folder`: Contains several classes from the StructureMap package. `IoC.cs` orchestrates application configuration and sets up dependency injection.
* `Data folder`: Contains code to initialize the database and also the definition of the `DbContext` class.
* `HomeController.cs`: Code added for injection of `IBloggingContext` into the Controller to obtain data from the database and then to display the data.
* `Index.cshtml`: The view used to display the data values from SQL Server.

## 3.2 Usage

You should know how the new .NET [Configuration service](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration) works before starting to use the connector. You need a basic understanding of the `ConfigurationBuilder` and how to add providers to the builder to configure the connector.

You should also know how the ASP.NET Core [Startup](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/startup) class is used in configuring the application services. Pay particular attention to the usage of the `ConfigureServices()` method.

To use this connector:

1. Create a Microsoft SQL Service instance and bind it to your application.
1. Optionally, configure any Microsoft SQL Server client settings (such as `appsettings.json`) you need.
1. Add the Steeltoe Cloud Foundry configuration provider to your `ConfigurationBuilder`.
1. Add `SqlConnection` or `DbContext` to your `IServiceCollection`.

### 3.2.1 Add NuGet Reference

To use this connector, add a reference to one of the Steeltoe connector NuGet packages. The following table describes the packages:

|App Type|ORM|Steeltoe Package|SQL Server Package|
|---|---|---|---|
|ASP.NET Core|Entity Framework 6|`Steeltoe.CloudFoundry.Connector.EF6Core`|`EntityFramework`|
|ASP.NET Core|Entity Framework Core|`Steeltoe.CloudFoundry.Connector.EFCore`|`Microsoft.EntityFrameworkCore.SqlServer`|
|ASP.NET Core|Other|`Steeltoe.CloudFoundry.ConnectorCore`|`System.Data.SqlClient`|
|Other|Other|`Steeltoe.CloudFoundry.ConnectorBase`|`System.Data.SqlClient`|

Use the Nuget package manager tools or directly add the appropriate packages to your project by using a `PackageReference`, as shown in the following example:

```xml
<ItemGroup>
...
    <PackageReference Include="Steeltoe.CloudFoundry.ConnectorBase" Version= "2.0.0"/>
    <PackageReference Include="System.Data.SqlClient" Version= "4.4.0"/>
...
</ItemGroup>
```

### 3.2.2 Configure Settings

The Microsoft SQL Server connector supports several configuration options. These settings can be used to develop or test an application locally and then be overridden during deployment.

The following Microsoft SQL Server connector configuration shows how to connect to SQL Server 2016 Express LocalDB:

```json
{
  ...
  "sqlserver": {
    "credentials": {
        "connectionString": "Server=(localdb)\\mssqllocaldb;database=Steeltoe;Trusted_Connection=True;"
    }
  }
  ...
}
```

The following table shows the available settings for the connector:

|Key|Description|Steeltoe Default|
|---|---|---|
|server|Hostname or IP Address of server|localhost|
|port|Port number of server|1433|
|username|Username for authentication|not set|
|password|Password for authentication|not set|
|database|Schema to which to connect|not set|
|connectionString|Full connection string|built from settings|
|integratedSecurity|Enable Windows Authentication (For local use only)|not set|

>IMPORTANT: All of the settings shown in the preceding table should be prefixed with `sqlserver:credentials:`.

The samples and most templates are already set up to read from `appsettings.json`. See [Reading Configuration Values](#reading-configuration-values).

### 3.2.3 Cloud Foundry

To use Microsoft SQL Server on Cloud Foundry, you need a service instance bound to your application. If the [Microsoft SQL Server broker](https://github.com/cf-platform-eng/mssql-server-broker) is installed in your Cloud Foundry instance, use it to create a new service instance, as follows:

```bash
> cf create-service SqlServer sharedVM mySqlServerService
```

An alternative to the broker is to use a user-provided service to explicitly provide connection information to the application, as shown in the following example:

```bash
> cf cups mySqlServerService -p '{"pw": "|password|","uid": "|user id|","uri": "jdbc:sqlserver://|host|:|port|;databaseName=|database name|"}'
```

If you are creating a service for an application that has already been deployed, you need to bind the service and restart or restage the application with the following commands:

```bash
> # Bind service to `myApp`
> cf bind-service myApp mySqlServerService
>
> # Restage the app to pick up change
> cf restage myApp
```

If you have not already deployed the application, a reference in the `manifest.yml` file can take care of the binding for you.

>NOTE: The commands shown in the preceding example may not exactly match the service or plan names available in your environment. You may have to adjust the `create-service` command to fit your environment. Use `cf marketplace` to see what is available.

Once the service is bound to your application, the connector's settings are available in `VCAP_SERVICES`. See [Reading Configuration Values](#reading-configuration-values).

### 3.2.4 Add SqlConnection

To use a `SqlConnection` in your application, add it to the service container in the `ConfigureServices()` method of the `Startup` class, as shown in the following example:

```csharp
using Steeltoe.CloudFoundry.Connector.MySql;

public class Startup {
    ...
    public IConfiguration Configuration { get; private set; }
    public Startup(...)
    {
      ...
    }
    public void ConfigureServices(IServiceCollection services)
    {
        // Add SqlConnection configured from Configuration
        services.AddSqlServerConnection(Configuration);

        // Add framework services.
        services.AddMvc();
        ...
    }
    ...
```

The `AddSqlServerConnection(Configuration)` method call shown in the previous example configures the `SqlConnection` by using the configuration built by the application and adds the connection to the service container.

### 3.2.5 Use SqlConnection

Once you have configured and added the connection to the service container, you can inject it and use it in a controller or a view, as shown in the following example:

```csharp
using System.Data.SqlClient;
...
public class HomeController : Controller
{
    public IActionResult SqlData([FromServices] SqlConnection dbConnection)
    {
        dbConnection.Open();

        MySqlCommand cmd = new MySqlCommand("SELECT * FROM TestData;", dbConnection);
        MySqlDataReader rdr = cmd.ExecuteReader();

        while (rdr.Read())
        {
            ViewData["Key" + rdr[0]] = rdr[1];
        }

        rdr.Close();
        dbConnection.Close();

        return View();
    }
}
```

>NOTE: The preceding code does not create a database or a table or insert data. As written, it fails unless you create the database, table, and data ahead of time.

### 3.2.6 Add DbContext

To use Entity Framework, inject and use a `DbContext` in your application (instead of a `SqlConnection`) by using the `AddDbContext<>()` method, as shown in the following example:

```csharp
using Steeltoe.CloudFoundry.Connector.Sql.EFCore
// OR
using Steeltoe.CloudFoundry.Connector.Sql.EF6;

public class Startup {
    ...
    public IConfiguration Configuration { get; private set; }
    public Startup(...)
    {
      ...
    }
    public void ConfigureServices(IServiceCollection services)
    {
        // If using EF6
        services.AddDbContext<TestContext>(Configuration);

        // If using EFCore
        services.AddDbContext<TestContext>(options => options.UseSqlServer(Configuration));

        // Add framework services.
        services.AddMvc();
        ...
    }
    ...
```

The `AddDbContext<TestContext>(..)` method call configures `TestContext` by using the configuration built earlier and then adds the `DbContext` (`TestContext`) to the service container.

You can define your `DbContext` differently, depending on whether you use Entity Framework 6 or Entity Framework Core.

The following example uses Entity Framework 6:

```csharp
// ------- EF6 DbContext ---------
using System.Data.Entity;
...

public class TestContext : DbContext
{
    public TestContext(string connectionString) : base(connectionString)
    {
    }
    public DbSet<TestData> TestData { get; set; }
}
```

The following example uses Entity Framework Core:

```csharp
// ------- EFCore DbContext ------
using Microsoft.EntityFrameworkCore;
...

public class TestContext : DbContext
{
    public TestContext(DbContextOptions options) : base(options)
    {

    }
    public DbSet<TestData> TestData { get; set; }
}

```

### 3.2.7 Use DbContext

Once you have configured and added the DbContext to the service container, you can inject it and use it in a controller or a view, as shown in the following example:

```csharp
using Project.Models;
...
public class HomeController : Controller
{
    public IActionResult SqlData([FromServices] TestContext context)
    {
        return View(context.TestData.ToList());
    }
```

# 4.0 RabbitMQ

This connector simplifies using the [RabbitMQ Client](https://www.rabbitmq.com/tutorials/tutorial-one-dotnet.html) in an application running on Cloud Foundry. We recommend following that tutorial, because you need to know how to use it before preceeding to use the connector.

The source code for this connector can be found [here](https://github.com/SteeltoeOSS/Connectors).

## 4.1 Quick Start

This quick start uses an ASP.NET Core sample application and the Steeltoe RabbitMQ Connector to connect to a RabbitMQ service on Cloud Foundry.

Specifically, it shows how to use a `RabbitMQ.Client` to send and receive messages on the bound RabbitMQ service.

### 4.1.1 Locate Sample

To get started, change directory to where the samples are stored, as follows:

```bash
> cd Samples/Connectors/src/AspDotNetCore/RabbitMQ
```

### 4.1.2 Create Service

You can use the Cloud Foundry CLI to create a service instance of RabbitMQ on Cloud Foundry.

The following commands assume that you use the RabbitMQ service provided by Pivotal on Cloud Foundry:

```bash
> # Create a RabbitMQ service instance on Cloud Foundry
> cf create-service p-rabbitmq standard myRabbitMQService
>
> # Make sure the service is ready
> cf services
```

If you use a different service, adjust the `create-service` command to fit your environment.

### 4.1.3 Publish and Push Sample

See [Publish Sample](#publish-sample) and the sections that follow for instructions on how to publish and push this sample to either Linux or Windows.

### 4.1.4 Observe Logs

You can use the `cf logs` command to see log output.

### 4.1.5 View Data

At this point, the app is running.

To send a message, click "Send" and use the form to send a message over RabbitMQ.

Having sent a message, click "Receive" to see those messages.

### 4.1.6 Understand the Sample

The sample was created with the .NET Core tooling `mvc` template (`dotnet new mvc`) and then modified to use the Steeltoe framework.

To gain an understanding of the Steeltoe related changes to the generated template code, examine the following files:

* `RabbitMQ.csproj`: Contains the `PackageReference` for the `RabbitMQ.Client` and the Steeltoe NuGet `Steeltoe.CloudFoundry.ConnectorCore`.
* `Program.cs`: Added `.UseCloudFoundryHosting()` for dynamic port binding and `.ConfigureAppConfiguration( -> .AddCloudFoundry()` to read `VCAP_SERVICES` when pushed to Cloud Foundry
* `Startup.cs`: Code added to the `ConfigureServices()` method to add a RabbitMQ `ConnectionFactory` to the service container
* `RabbitMQController.cs`: Code added for injection of a RabbitMQ `ConnectionFactory` into the Controller. The `ConnectionFactory` is used in the `Send` and `Receive` action methods.
* `Receive.cshtml`: The view used to display the received message data values.
* `Send.cshtml`: The view used to submit message data.

## 4.2 Usage

You should know how the new .NET [Configuration service](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration) works before starting to use the connector. To configure the connector, you need a basic understanding of the `ConfigurationBuilder` and how to add providers to the builder.

You should also know how the ASP.NET Core [Startup](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/startup) class is used in configuring the application services for the application. Pay particular attention to the usage of the `ConfigureServices()` method.

You probably want some understanding of how to use the [RabbitMQ Client](https://www.rabbitmq.com/tutorials/tutorial-one-dotnet.html) before starting to use this connector.

To use this Connector:

1. Create and bind a RabbitMQ service instance to your application.
1. Optionally, configure any RabbitMQ client settings (such as in `appsettings.json`)
1. Add the Steeltoe Cloud Foundry config provider to your `ConfigurationBuilder`.
1. Add the RabbitMQ `ConnectionFactory` to your `ServiceCollection`.

### 4.2.1 Add NuGet Reference

To use the connector, you need to add a reference to the appropriate Steeltoe Connector NuGet package.

If your application uses Microsoft's Dependency Injection, use the `Steeltoe.CloudFoundry.ConnectorCore` package. If you do not use Microsoft's Dependency Injection, use `Steeltoe.CloudFoundry.ConnectorBase`.

You can use the NuGetPackage manager or directly add the following package references to your .csproj file to add the desired connector package and `RabbitMQ.Client`:

```xml
<ItemGroup>
...
    <PackageReference Include="Steeltoe.CloudFoundry.ConnectorCore" Version= "2.0.0"/>
    <PackageReference Include="RabbitMQ.Client" Version= "x.y.z"/>
...
</ItemGroup>
```

### 4.2.2 Configure Settings

The connector supports several settings for the RabbitMQ ConnectionFactory that can be useful when you are developing and testing an application locally and you need to have the connector configure the connection for non-default settings.

The following example of the connectors configuration in JSON shows how to setup a connection to a RabbitMQ server at `amqp://guest:guest@127.0.0.1/`:

```json
{
  ...
  "rabbitmq": {
    "client": {
      "uri": "amqp://guest:guest@127.0.0.1/"
    }
  }
  ...
}
```

The following table describes all the possible settings for the connector:

|Key|Description|Default|
|---|---|---|
|server|Hostname or IP Address of the server|127.0.0.1|
|port|Port number of the server|5672|
|username|Username for authentication|not set|
|password|Password for authentication|not set|
|virtualHost|Virtual host to which to connect|not set|
|sslEnabled|Should SSL be enabled|false|
|sslPort|SSL Port number of server|5671|
|uri|Full connection string|built from settings|

>IMPORTANT: All of these settings should be prefixed with `rabbitmq:client:`.

The samples and most templates are already set up to read from `appsettings.json`. See [Reading Configuration Values](#reading-configuration-values).

### 4.2.3 Cloud Foundry

To use RabbitMQ on Cloud Foundry, you can create and bind an instance to your application using the Cloud Foundry CLI, as follows:

```bash
> # Create RabbitMQ service
>cf create-service p-rabbitmq standard myRabbitMQService
>
> # Bind the service to `myApp`
> cf bind-service myApp myRabbitMQService
>
> # Restage the app to pick up changes
> cf restage myApp
```

>NOTE: The preceding commands assume you use the RabbitMQ service provided by Pivotal on Cloud Foundry. If you use a different service, adjust the `create-service` command to fit your environment.

Once the service is bound to your application, the connector's settings are available in `VCAP_SERVICES`. See [Reading Configuration Values](#reading-configuration-values).

### 4.2.4 Add RabbitMQ ConnectionFactory

To use a RabbitMQ `ConnectionFactory` in your application, add it to the service container in the `ConfigureServices()` method of the `Startup` class, as shown in the following example:

```csharp
using Steeltoe.CloudFoundry.Connector.RabbitMQ;

public class Startup {
    ...
    public IConfiguration Configuration { get; private set; }
    public Startup(...)
    {
      ...
    }
    public void ConfigureServices(IServiceCollection services)
    {
        // Add RabbitMQ ConnectionFactory configured from Cloud Foundry
        services.AddRabbitMQConnection(Configuration);

        // Add framework services.
        services.AddMvc();
        ...
    }
    ...
```

### 4.2.5 Use RabbitMQ ConnectionFactory

Once you have configured and added the RabbitMQ `ConnectionFactory` to the service container, you can inject it and use it in a controller or a view, as shown in the following example:

 ```csharp
using RabbitMQ.Client;
 ...
 public class HomeController : Controller
 {
     ...
     public IActionResult RabbitMQData([FromServices] ConnectionFactory factory)
     {

         using (var connection = factory.CreateConnection())
         using (var channel = connection.CreateModel())
         {
             CreateQueue(channel);
             var body = Encoding.UTF8.GetBytes("a message");
             channel.BasicPublish(exchange: "",
                                  routingKey: "a-topic",
                                  basicProperties: null,
                                  body: body);

         }
         return View();
     }

 }

 ```

# 5.0 Redis

 This connector simplifies using a Microsoft [RedisCache](https://docs.microsoft.com/en-us/aspnet/core/performance/caching/distributed#using-a-redis-distributed-cache) or a StackExchange [IConnectionMultiplexer](https://stackexchange.github.io/StackExchange.Redis/) in an application running on Cloud Foundry.

In addition to the [Quick Start](#5-1-quick-start), other Steeltoe sample applications are available to help you understand how to use this connector:

* [DataProtection](https://github.com/SteeltoeOSS/Samples/tree/master/Security/src/RedisDataProtectionKeyStore): A sample application showing how to use the Steeltoe DataProtection Key Storage Provider for Redis.
* [MusicStore](https://github.com/SteeltoeOSS/Samples/tree/master/MusicStore): A sample application showing how to use all of the Steeltoe components together in an ASP.NET Core application. This is a micro-services based application built from the ASP.NET Core reference app MusicStore provided by Microsoft.

The source code for this connector can be found [here](https://github.com/SteeltoeOSS/Connectors).

## 5.1 Quick Start

This quick start consists of using a ASP.NET Core sample application to show how to use the Steeltoe Redis Connector for connecting to a Redis service on Cloud Foundry.

### 5.1.1 Locate Sample

To get started, change directory to where the samples are stored, as follows:

```bash
> cd Samples/Connectors/src/AspDotNetCore/Redis
```

### 5.1.2 Create Service

You can use the Cloud Foundry CLI to create a service instance of Redis on Cloud Foundry.

The following commands assume that you use the Redis service provided by Pivotal on Cloud Foundry:

```bash
> # Create a Redis service instance on Cloud Foundry
> cf create-service p-redis shared-vm myRedisService
>
> # Make sure the service is ready
> cf services
```

If you use a different service, you have to adjust the `create-service` command to fit your environment.

### 5.1.3 Publish and Push Sample, observe logs

See [Publish Sample](#publish-sample) and the sections that follow for instructions on how to publish and push this sample to either Linux or Windows.

### 5.1.4 Observe Logs

You can use the `cf logs` command to see log output.

### 5.1.5 What to expect

At this point, the app is running. On startup, the app inserts two key/value pairs into the bound Redis Cache.

To display those values, click on the "Cache Data" link in the menu and you should see the key/value pairs from the Microsoft `RedisCache`.

Click on the "ConnectionMultiplexer Data" link to view data from the StackExchange `ICollectionMultiplexer`.

### 5.1.6 Understand Sample

The sample was created from the .NET Core tooling `mvc` template (`dotnet new mvc`) and modified to use the Steeltoe frameworks.

To understand the Steeltoe related changes to the generated template code, examine the following files:

* `Redis.csproj`: Contains the `PackageReference` for the Steeltoe NuGet `Steeltoe.CloudFoundry.ConnectorCore`.
* `Program.cs`: Added `.UseCloudFoundryHosting()` for dynamic port binding and `.ConfigureAppConfiguration( -> .AddCloudFoundry()` to read `VCAP_SERVICES` when pushed to Cloud Foundry.
* `Startup.cs`: Code added to the `ConfigureServices()` method to add an `IDistributedCache` and an `IConnectionMultiplexer` to the service container.
* `HomeController.cs`: Code added for injection of a `IDistributedCache` or `IConnectionMultiplexer` into the Controller. These are used to obtain data from the cache and then to display it.
* `CacheData.cshtml`: The view used to display the Redis data values obtained using `IDistributedCache`.
* `ConnData.cshtml`: The view used to display the Redis data values obtained using `IConnectionMultiplexer`.
* `Models folder`: Contains code to initialize the Redis cache.

## 5.2 Usage

You should know how the .NET [Configuration service](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration) works before starting to use the connector. To configure the connector, you need a basic understanding of the `ConfigurationBuilder` and how to add providers to the builder.

You should also know how the ASP.NET Core [Startup](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/startup) class is used in configuring the application services for the app. Pay particular attention to the usage of the `ConfigureServices()` method.

You probably want some understanding of how to use the [RedisCache](https://docs.microsoft.com/en-us/aspnet/core/performance/caching/distributed#using-a-redis-distributed-cache) or [IConnectionMultiplexer](https://stackexchange.github.io/StackExchange.Redis/) before starting to use this connector.

To use this connector:

1. Create a Redis Service instance and bind it to your application.
1. Optionally, configure any Redis client settings (for example, in `appsettings.json`).
1. Add the Steeltoe Cloud Foundry config provider to your ConfigurationBuilder.
1. Add DistributedRedisCache or ConnectionMultiplexer to your ServiceCollection.

### 5.2.1 Add NuGet Reference

To use the connector, you need to add a reference to the appropriate Steeltoe Connector NuGet package and a reference to `Microsoft.Extensions.Caching.Redis`, `StackExchange.Redis`, or `StackExchange.Redis.StrongName`.

>NOTE: This requirement is a change for version 2.x - version 1.x packages do not require a direct Redis package reference.

If your application uses Microsoft's Dependency Injection, use the `Steeltoe.CloudFoundry.ConnectorCore` package. If you do not use Microsoft's Dependency Injection, use `Steeltoe.CloudFoundry.ConnectorBase`.

To include the Steeltoe connector and Redis libraries, use the NuGet Package Manager tools or directly add the following package references to your .csproj file, as shown in the following example:

```xml
<ItemGroup>
...
    <PackageReference Include="Steeltoe.CloudFoundry.ConnectorCore" Version= "2.0.0"/>
    <PackageReference Include="Microsoft.Extensions.Caching.Redis" Version= "x.y.z"/>
...
</ItemGroup>
```

>NOTE: Because `Microsoft.Extensions.Caching.Redis` depends on `StackExchange.Redis.StrongName`, adding a reference to the Microsoft library also enables access to the StackExchange classes, as seen in the sample application.

### 5.2.2 Configure Settings

The connector supports several settings for the Redis connection that can be useful when you are developing and testing an application locally and you need to have the connector configure the connection for non-default settings.

The following example of the connector's configuration in JSON that shows how to set up a connection to a Redis server at `http://foo.bar:1111`:

```json
{
  ...
  "redis": {
    "client": {
      "host": "http://foo.bar",
      "port": 1111
    }
  }
  ...
}
```

The following table table describes all possible settings for the connector

|Key|Description|Default|
|---|---|---|
|host|Hostname or IP Address of the server.|localhost|
|port|Port number of the server.|6379|
|endPoints|Comma-separated list of host:port pairs.|not set|
|clientName|Identification for the connection within redis.|not set|
|connectRetry|Times to repeat initial connect attempts.|3|
|connectTimeout|Timeout (ms) for connect operations.|5000|
|abortOnConnectFail|Will not create a connection while no servers are available.|true|
|keepAlive|Time (seconds) at which to send a message to help keep sockets alive.|-1|
|resolveDns|Whether DNS resolution should be explicit and eager, rather than implicit.|false|
|ssl|Whether SSL encryption should be used.|false|
|sslHost|Enforces a particular SSL host identity on the server's certificate.|not set|
|writeBuffer|Size of the output buffer.|4096|
|connectionString|Connection string to use instead of values shown earlier.|not set|
|instanceId|Cache ID. Used only with `IDistributedCache`.|not set|

>IMPORTANT: All of these settings should be prefixed with `redis:client:`.

The samples and most templates are already set up to read from `appsettings.json`See [Reading Configuration Values](#reading-configuration-values).

### 5.2.3 Cloud Foundry

To use Redis on Cloud Foundry, create and bind an instance to your application by using the Cloud Foundry CLI, as shown in the following example:

```bash
> # Create Redis service
> cf create-service p-redis shared-vm myRedisCache
>
> # Bind service to `myApp`
> cf bind-service myApp myRedisCache
>
> # Restage the app to pick up change
> cf restage myApp
```

>NOTE: The preceding commands assume you use the Redis service provided by Pivotal on Cloud Foundry. If you use a different service, you have to adjust the `create-service` command to fit your environment.

Once the service is bound to your application, the connector's settings are available in `VCAP_SERVICES`. See [Reading Configuration Values](#reading-configuration-values).

### 5.2.4 Add IDistributedCache

To use Microsoft's `IDistributedCache` in your application, add it to the service container, as shown in the following example:

 ```csharp
using Steeltoe.CloudFoundry.Connector.Redis;
public class Startup {
    public IConfiguration Configuration { get; private set; }
    public Startup()
    {
    }
    public void ConfigureServices(IServiceCollection services)
    {
        // Add Microsoft Redis Cache (IDistributedCache) configured from Cloud Foundry
        services.AddDistributedRedisCache(Configuration);

        // Add framework services
        services.AddMvc();
    }
```

The `AddDistributedRedisCache(Configuration)` method call configures the `IDistributedCache` by using the configuration built by the application earlier and adds the connection to the service container.

### 5.2.5 Use IDistributedCache

The following example shows how to inject and use the `IDistributedCache` in a controller once it has been added to the service container:

 ```csharp
 using Microsoft.Extensions.Caching.Distributed;
 ...
 public class HomeController : Controller
 {
     private IDistributedCache _cache;
     public HomeController(IDistributedCache cache)
     {
         _cache = cache;
     }
     ...
     public IActionResult CacheData()
     {
         string key1 = Encoding.Default.GetString(_cache.Get("Key1"));
         string key2 = Encoding.Default.GetString(_cache.Get("Key2"));

         ViewData["Key1"] = key1;
         ViewData["Key2"] = key2;

         return View();
     }
 }
 ```

### 5.2.6 Add IConnectionMultiplexer

To use a StackExchange `IConnectionMultiplexer` in your application directly, add it to the service container in the `ConfigureServices()` method of the `Startup` class, as shown in the following example:

 ```csharp
using Steeltoe.CloudFoundry.Connector.Redis;

public class Startup {
    ...
    public IConfiguration Configuration { get; private set; }
    public Startup(...)
    {
      ...
    }
    public void ConfigureServices(IServiceCollection services)
    {

        // Add StackExchange IConnectionMultiplexer configured from Cloud Foundry
        services.AddRedisConnectionMultiplexer(Configuration);

        // Add framework services
        services.AddMvc();
        ...
    }
    ...
```

The `AddRedisConnectionMultiplexer(Configuration)` method call configures the `IConnectionMultiplexer` by using the configuration built by the application and adds the connection to the service container.

>NOTE: If necessary, you can use both `IDistributedCache` and `IConnectionMultiplexer` in your application.

### 5.2.7 Use IConnectionMultiplexer

Once you have configured and added the `IConnectionMultiplexer` to the service container, you can inject it and use it in a controller or a view, as shown in the following example:

 ```csharp
 using Microsoft.Extensions.Caching.Distributed;
 ...
 public class HomeController : Controller
 {
     private IConnectionMultiplexer _conn;
     public HomeController(IConnectionMultiplexer conn)
     {
         _conn = conn;
     }
     ...
      public IActionResult ConnData()
    {
        IDatabase db = _conn.GetDatabase();

        string key1 = db.StringGet("ConnectionMultiplexerKey1");
        string key2 = db.StringGet("ConnectionMultiplexerKey2");

        ViewData["ConnectionMultiplexerKey1"] = key1;
        ViewData["ConnectionMultiplexerKey2"] = key2;

        return View();
    }
 }
 ```

# 6.0 OAuth

This connector simplifies using Cloud Foundry OAuth2 security services (for example, [UAA Server](https://github.com/cloudfoundry/uaa) or [Pivotal Single Sign-on](https://docs.pivotal.io/p-identity/)) by exposing the Cloud Foundry OAuth service configuration data as injectable `IOption<OAuthServiceOptions>`. It is used by the [Cloud Foundry External Security Providers](../steeltoe-security) but can be used separately.

## 6.1 Quick Start

This quick start consists of an ASP.NET Core sample application showing how to use the OAuth Connector to expose the binding information provided by the Cloud Foundry UAA Server.

### 6.1.1 Locate Sample

To get started, change directory to where the samples are stored, as follows:

```bash
> cd Samples/Connectors/src/AspDotNetCore/OAuth
```

### 6.1.2 Create Service

You must first create an instance of an OAuth2 service in an org and a space. In this quick start, we will the UAA Server. (The other choice is Pivotal Single Sign-on.)

To set up UAA, we need to create a user-provided service that provides the appropriate UAA server configuration data to the application.

```bash
> # Create a OAuth service instance on Cloud Foundry
> cf cups myOAuthService -p oauth.json
>
> # Make sure the service is ready
> cf services
```

>NOTE: The contents of `oauth.json` must be modified to match your Cloud Foundry configuration.

### 6.1.3 Publish and Push Sample

See [Publish Sample](#publish-sample) and the sections that follow for instructions on how to publish and push this sample to either Linux or Windows.

### 6.1.4 Observe Logs

You can use the `cf logs` command to see log output.

### 6.1.5 View Data

At this point, the app is running.

On the "Apps" menu, click on the "OAuth Options" menu item to see meaningful configuration data for the bound OAuth service.

### 6.1.6 Understand Sample

The sample was created using the .NET Core tooling `mvc` template (`dotnet new mvc`)  and then modified to use the Steeltoe frameworks.

To gain an understanding of the Steeltoe related changes to the generated template code, examine the following files:

* `OAuth.csproj`: Contains the `PackageReference` for the Steeltoe NuGet `Steeltoe.CloudFoundry.ConnectorCore`
* `Program.cs`: Added `.UseCloudFoundryHosting()` for dynamic port binding and `.ConfigureAppConfiguration( -> .AddCloudFoundry()` to read `VCAP_SERVICES` when pushed to Cloud Foundry.
* `Startup.cs`: Code added to the `ConfigureServices()` method to add a `OAuthServiceOptions` to the service container.
* `HomeController.cs`: Code added for injection of a `OAuthServiceOptions` into the Controller. The `OAuthServiceOptions` contains the binding information from Cloud Foundry.
* `OAuthOptions.cshtml`: The view used to display the OAuth data.

## 6.2 Usage

You should know how the new .NET [Configuration service](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration) works before starting to use the connector. To configure the connector, you need a basic understanding of the `ConfigurationBuilder` and how to add providers to the builder.

You should also know how the ASP.NET Core [Startup](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/startup) class is used in configuring the application services for the application. Pay particular attention to the usage of the `ConfigureServices()` method.

You probably want some understanding of Cloud Foundry OAuth2 security services (such as [UAA Server](https://github.com/cloudfoundry/uaa) or [Pivotal Single Sign-on](https://docs.pivotal.io/p-identity/)) before starting to use this connector.

To use this Connector:

1. Create an OAuth service instance and bind it to your application.
1. (Optional) Configure any additional settings the OAuth connector needs.
1. Add the Steeltoe Cloud Foundry configuration provider to your ConfigurationBuilder.
1. Add the OAuth connector to your ServiceCollection.
1. Access the OAuth service options.

### 6.2.1 Add NuGet Reference

To use the connector, you need to add a reference to the appropriate Steeltoe Connector NuGet package. If your application uses Microsoft's Dependency Injection, use the `Steeltoe.CloudFoundry.ConnectorCore` package. If you do not use Microsoft's Dependency Injection, use `Steeltoe.CloudFoundry.ConnectorBase`.

To include the Steeltoe connector library, use the NuGet Package Manager tools or directly add the following package references to your .csproj file, as shown in the following listing:

```xml
<ItemGroup>
...
    <PackageReference Include="Steeltoe.CloudFoundry.ConnectorCore" Version= "2.0.0"/>
...
</ItemGroup>
```

### 6.2.2 Configure Settings

Configuring additional settings for the connector is not typically required, but, when Cloud Foundry uses self-signed certificates, you might need to disable certificate validation, as shown in the following example:

```json
{
  ...
  "security": {
    "oauth2": {
      "client": {
        "validateCertificates": false
      }
    }
  }
  ...
}
```

>CAUTION: Self-signed certificates are inherently insecure. Never use them for a production environment.

The samples and most templates are already set up to read from `appsettings.json`. See [Reading Configuration Values](#reading-configuration-values).

### 6.2.3 Cloud Foundry

There are multiple ways to set up OAuth services on Cloud Foundry.

In the [quick start](#6-1-quick-start), we used a user-provided service to define a direct binding to the Cloud Foundry UAA server. Alternatively, you can use the [Pivotal Single Sign-on](https://docs.pivotal.io/p-identity/)) product to provision an OAuth service binding. The process to create service binding varies for each of the approaches.

Regardless of which you choose, once the service is bound to your application, the connector's settings are available in `VCAP_SERVICES`. See [Reading Configuration Values](#reading-configuration-values).

### 6.2.4 Add OAuthServiceOptions

Once the OAuth service has been bound to the application, add the OAuth connector to your service collection in the `ConfigureServices()` method of the `Startup` class, as shown in the following example:

```csharp
using Steeltoe.CloudFoundry.Connector.OAuth;

public class Startup {
    ...
    public IConfiguration Configuration { get; private set; }
    public Startup(...)
    {
      ...
    }
    public void ConfigureServices(IServiceCollection services)
    {
        // Configure and Add IOptions<OAuthServiceOptions> to the container
        services.AddOAuthServiceOptions(Configuration);

        // Add framework services.
        services.AddMvc();
        ...
    }
    ...
```

The `AddOAuthServiceOptions(Configuration)` method call configures a `OAuthServiceOptions` instance by using the configuration built by the application and adds it to the service container.

### 6.2.5 Use OAuthServiceOptions

Finally, you can inject and use the configured `OAuthServiceOptions` into a controller, as shown in the following example:

 ```csharp
 using Steeltoe.CloudFoundry.Connector.OAuth;
 ...
 public class HomeController : Controller
 {
     OAuthServiceOptions _options;

     public HomeController(IOptions<OAuthServiceOptions> oauthOptions)
     {
         _options = oauthOptions.Value;
     }
     ...
     public IActionResult OAuthOptions()
     {
         ViewData["ClientId"] = _options.ClientId;
         ViewData["ClientSecret"] = _options.ClientSecret;
         ViewData["UserAuthorizationUrl"] = _options.UserAuthorizationUrl;
         ViewData["AccessTokenUrl"] = _options.AccessTokenUrl;
         ViewData["UserInfoUrl"] = _options.UserInfoUrl;
         ViewData["TokenInfoUrl"] = _options.TokenInfoUrl;
         ViewData["JwtKeyUrl"] = _options.JwtKeyUrl;
         ViewData["ValidateCertificates"] = _options.ValidateCertificates;
         ViewData["Scopes"] = CommaDelimit(_options.Scope);

         return View();
     }
 }
 ```

# Common References

## Publish Sample

You can use the `dotnet` CLI to build and locally publish the application with your preferred framework and runtime. To get started, run the following command:

```bash
> dotnet restore --configfile nuget.config
```

Then you can use one of the following commands to publish:

* Linux with .NET Core: `dotnet publish -f netcoreapp2.0 -r ubuntu.14.04-x64`
* Windows with .NET Core: `dotnet publish -f netcoreapp2.0 -r win10-x64`
* Windows with .NET Platform: `dotnet publish -f net461 -r win10-x64`

## Push Sample

Use the Cloud Foundry CLI to push the published application to Cloud Foundry using the parameters that match what you selected for framework and runtime:

```bash
> # Push to Linux cell
> cf push -f manifest.yml -p bin/Debug/netcoreapp2.0/ubuntu.14.04-x64/publish
>
>  # Push to Windows cell, .NET Core
> cf push -f manifest-windows.yml -p bin/Debug/netcoreapp2.0/win10-x64/publish
>
>  # Push to Windows cell, .NET Framework
> cf push -f manifest-windows.yml -p bin/Debug/net461/win10-x64/publish
```

> Note: all sample manifests have been defined to bind their application to their service(s) as created above.

## Observe Logs

To see the logs as you startup the application, use `cf logs oauth`.

On a Linux cell, you should see something resembling the following during startup:

```bash
2016-06-01T09:14:14.38-0600 [CELL/0]     OUT Creating container
2016-06-01T09:14:15.93-0600 [CELL/0]     OUT Successfully created container
2016-06-01T09:14:17.14-0600 [CELL/0]     OUT Starting health monitoring of container
2016-06-01T09:14:21.04-0600 [APP/0]      OUT Hosting environment: Development
2016-06-01T09:14:21.04-0600 [APP/0]      OUT Content root path: /home/vcap/app
2016-06-01T09:14:21.04-0600 [APP/0]      OUT Now listening on: http://*:8080
2016-06-01T09:14:21.04-0600 [APP/0]      OUT Application started. Press Ctrl+C to shut down.
2016-06-01T09:14:21.41-0600 [CELL/0]     OUT Container became healthy
```

On Windows cells, you should see something slightly different but with the same information.

## Reading Configuration Values

Once the connector's settings have been defined, the next step is to read them so that they can be made available to the connector.

The code in the next example reads connector settings from the `appsettings.json` file with the .NET JSON configuration provider (`AddJsonFile("appsettings.json"))` and from `VCAP_SERVICES` with `AddCloudFoundry()`. Both sources are then added to the configuration builder. The following code shows how to read from both sources:

```csharp
public class Program {
    ...
    public static IWebHost BuildWebHost(string[] args)
    {
        return new WebHostBuilder()
            ...
            .UseCloudFoundryHosting()
            ...
            .ConfigureAppConfiguration((builderContext, configBuilder) =>
            {
                var env = builderContext.HostingEnvironment;
                configBuilder.SetBasePath(env.ContentRootPath)
                    .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                    .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
                    .AddEnvironmentVariables()
                    // Add to configuration the Cloudfoundry VCAP settings
                    .AddCloudFoundry();
            })
            .Build();
    }
    ...
```

When pushing the application to Cloud Foundry, the settings from service bindings merge with the settings from other configuration mechanisms (such as `appsettings.json`).

If there are merge conflicts, the last provider added to the Configuration takes precedence and overrides all others.

To manage application settings centrally instead of with individual files, use [Steeltoe Configuration](/docs/steeltoe-configuration) and a tool such as [Spring Cloud Config Server](https://github.com/spring-cloud/spring-cloud-config)

>NOTE: If you use the Spring Cloud Config Server, `AddConfigServer()` automatically calls `AddCloudFoundry()` for you.

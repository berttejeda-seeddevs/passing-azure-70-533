
# Configure Web Apps

## Objectives:
- [Define and use app settings connection strings, handlers, and virtual directories](#Settings)
- Configure certificates and custom domains
- Configure SSL bindings and runtime configurations
- Manage Web Apps by using Azure PowerShell and Xplat-CLI

<a name="Settings"></a>

## Define and use app settings connection strings, handlers, and virtual directories

### Quick Facts
* Enabling Java disables the .NET, PHP, and Python options.
* 64-bit environment requires Basic or Standard
* Remote debugging will get disabled after 48 hours.

### App Settings
* Are injected into the .NET configuration `AppSettings` at runtime, **overriding** existing settings.
* Pyton, Java, Node and PHP applications can access these settings using an *environment variable*. For each settings, two environment variables will be created: One with the name of the settings and one with the prefix `APPSETTING_`.

### Connection strings
* Are injected into the .NET configuration `connectionStrings` at runtime, **overriding** existing settings.
* Pyton, Java, Node and PHP applications can access the connection strings using an *environment variable*. They are *prefixed* with the connection type, e. g.:
    * SQL Server: `SQLCONNSTR_`
    * MySQL: `MYSQLCONNSTR_`
    * SQL Database: `SQLAZURECONNSTR_`
    * Custom: `CUSTOMCONNSTR_`

### Resource
[Configure web apps in Azure App Service](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-configure)
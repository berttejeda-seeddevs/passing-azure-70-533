# Deploy Web Apps

**Objectives:** 
* [Define deployment slots](#Slots)
* [Roll back deployments](#Rollback)
* [Implement pre- and post- deployment actions](#Actions)
* [Create, configure, and deploy packages](#Configure)
* [Create App Service plans](#Createasp)
* [Migrate Web Apps between App Service plans](#Migrate)
* [Create a Web App within an App Service plan](#Createwa)

<a name="Slots"></a>

## Define deployment slots

### Quick Facts
* Requires **Standard** (5 slots) or **Premium** (20 slots) App Service Plan.
* Slots are actualy live apps with their own hostnames.
* Can be used to *prewarm* an update before releasing it to production.
* There is *no content* after the slot is created.

### PowerShell
The following snippet will create a new slot called `my-new-slot` in the web app called `70-533-wa` using PowerShell:
```powershell
New-AzureRmWebAppSlot `
    -ResourceGroupName '70-533-rg' `
    -Name '70-533-wa' `
    -Slot 'my-new-slot' `
    -AppServicePlan '70-533-asp'
```

### Azure CLI
The following snippet will create a new slot called `my-new-slot` in the web app called `70-533-wa` using xPlat CLI
```
azure webapp create --slot 'my-new-slot' --resource-group '70-533-rg' --location 'West Europe' --plan '70-533-asp' --name '70-533-wa'
```


### Swap with preview
Keeps the destination slot unchanged but applies the settings of the destination slot to the source slot **including** slot specific settings. This allows you to preview how the app will behave with the destination slot settings.

Swap with preview is **not** supported for web apps on Linux.

### Autoswap
Autoswap allows to continuously deploy an app with zero cold start and zero downtime because the app gets *prewarmed* before swapped. 

Autoswap is **not** supported for web apps on Linux. 

### Settings swap behavior
**Settings that are swapped**
* General Settings (Framework version, Always On, ...)
* Handler mapping
* Monitoring and diagnostic
* WebJob content

**Settings that can be swapped (configurable)**
* App Settings
* Connection Strings

**Settings that are not swapped**
* WebJob schedulers
* Scale settings
* SSL certificates & binding
* Endpoints
* Custom Domain Name

### Resources
[Set up staging environments in Azure App Service](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-staged-publishing)

<a name="Rollback"></a>

## Roll back deployments
If there is an error within the production slot after a swap, roll back to the previous state by swapping the same to slots again.

### PowerShell
The following snippet will *swap* the **staging** slot with the **production** using PowerShell:
```powershell
$ParametersObject = @{targetSlot  = "production"}
Invoke-AzureRmResourceAction `
    -ResourceGroupName "70-533-rg" `
    -ResourceType Microsoft.Web/sites/slots `
    -ResourceName "70-533-wa/staging" `
    -Action slotsswap `
    -Parameters $ParametersObject `
    -ApiVersion 2015-07-01
```

### Azure CLI
The following snippet will *swap* the **staging** slot with the **production** using xPlat CLI:
```
azure webapp config set --resource-group '70-533-rg' --name '70-533-wa' --slot 'staging'
```

### Resources

[To rollback a production app after swap](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-staged-publishing#Rollback)

<a name="Actions"> </a>

## Implement pre- and post- deployment actions
https://github.com/projectkudu/kudu/wiki/Web-hooks
https://github.com/projectkudu/kudu/wiki/Post-Deployment-Action-Hooks

<a name="Deploy"> </a>

## Create, configure, and deploy packages

* `/site/wwwroot/App_Data/Jobs/` is the directory for web jobs
* `/site/wwwroot/` is the directory for web apps

1. FTP
2.  Kudu (Git /Mercurial or OneDrive / Dropbox)
    * Content Sync from OneDrive or Dropbox (**manual**)
    * Repository-based CI with **auto** sync from **Github**, **Bitbucket** and **VSTS**
    * Repository-based deployment with **manual** sync from a *local* Git.
3.  Web Deploy (supports diff-only deployment, database creation, transforms of connection strings)

Automatic CI builds only possible using cloud-based source code management (SCM) like VSTS, GitHub or BitBucket.

### Resources
[Azure App Service deployment overview](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-staged-publishing#overview)

<a name="Createasp"> </a>

## Create App Service plans
App Service plans define:
* Region (North Europe)
* Scale count (1, 2, 3, ...)
* Instance size (Small, Medium, Large)
* SKU (Free, Shared, Basic, Standard and Premium)

### Quick facts
* SKU and Scale of the App Service plan determines the *cost* and not the number of apps hosted in it.
* All applications assigned to an App Service Plan share the resources defined by it.
* Basic SKU or higher is necessary to specify the size and scale count of the VMs.

### Reasons to isolate an app into a new App Service plan:
* The App is resource intensive
* The App needs resource in a different geographical region
* The App scales different then other apps hosted in an existing plan.

### PowerShell
Create an App Service Plan using PowerShell:
```powershell
New-AzureRmAppServicePlan `
    -Name '70-533-asp' `
    -Location 'West Europe' `
    -Tier Standard `
    -NumberofWorkers 1 `
    -WorkerSize Medium `
    -ResourceGroupName '70-533-rg'
```
### Azure CLI
Create an App Service Plan using xPlat CLI:
```
azure appserviceplan create --name 70-533-asp --location "West Europe" --resource-group '70-533-rg' --sku S1 --instances 1
```

### Resources
[Azure App Service plans in-depth overview](https://docs.microsoft.com/en-us/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)

<a name="Migrate"> </a>

## Migrate Web Apps between App Service plans
To move an app to a different App Service plan, the plan must be in the **same resource group** and **geographical region**. Clone allows you to copy your app to a new or existing App Service plan in **any** region.

### Resources
[Move an app to a different App Service plan](https://docs.microsoft.com/en-us/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview#move-an-app-to-a-different-app-service-pl/)


<a name="Createwa"> </a>

## Create a Web App within an App Service plan

### PowerShell
Create a Web App using PowerShell
```powershell
New-AzureRmWebApp `
    -ResourceGroupName $resourceGroupName `
    -Name $appName `
    -Location $resourceLocation `
    -AppServicePlan $appServicePlanName
```


### Azure CLI
Create a Web App using xPlat CLI
```
azure webapp create --name '70-533-wa' --resource-group '70-533-rg' --plan '70-533-asp' --location 'West Europe'
```

### Resources
* [Using Azure Resource Manager-Based XPlat CLI for Azure App Service](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-app-azure-resource-manager-xplat-cli)

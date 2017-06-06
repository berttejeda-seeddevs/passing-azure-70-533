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
The following snippet will create a new slot called `my-new-slot` in the web app called `70-533-wa` using Azure CLI 2.0:
```
az webapp deployment slot create --name 70-533-wa --resource-group 70-533-rg --slot my-new-slot
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
The following snippet will *swap* the **staging** slot with the **production** using Azure CLI 2.0:
```
az webapp deployment slot swap --resource-group 70-533-rg --target-slot production --slot staging --name 70-533-wa
```

### Resources

[To rollback a production app after swap](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-staged-publishing#Rollback)




<a name="Actions"> </a>
## Implement pre- and post- deployment actions

<a name="Deploy"> </a>
## Create, configure, and deploy packages

<a name="Createasp"> </a>
## Create App Service plans

<a name="Migrate"> </a>
## Migrate Web Apps between App Service plans

<a name="Createwa"> </a>
## Create a Web App within an App Service plan

## Quick facts:
- Swap with preview is **not** supported for web apps on Linux.
- Autoswap is **not** supported for web apps on Linux.
- All applications assigned to an App Service Plan share the resources defined by it.
- Basic SKU or higher is necessary to specify the size and scale count of the VMs.
- SKU and Scale of the App Service plan determines the cost and not the number of apps hosted in it.
- An app can be associated with only one App Service plan.
- Clone allows you to copy your app to a new or existing App Service plan in **any** region.
- App services plan still incur charges even if they have no apps associated with it.
- To move an app to a different App Service plan, the plan must be in the **same resource group** and **geographical region**.
- Automatic CI builds only possible using cloud-based source code management (SCM) like VSTS, GitHub or BitBucket.

## 3 Deployment methods
1. FTP
2.  Kudu (Git /Mercurial or OneDrive / Dropbox)
    * Content Sync from OneDrive or Dropbox (**manual**)
    * Repository-based CI with **auto** sync from **Github**, **Bitbucket** and **VSTS**
    * Repository-based deployment with **manual** sync from a *local* Git.
3.  Web Deploy (supports diff-only deployment, database creation, transforms of connection strings)

## App Service plans
### Definition 
- Region (North Europe)
- Scale count (1, 2, 3, ...)
- Instance size (Small, Medium, Large)
- SKU (Free, Shared, Basic, Standard and Premium)

### Reasons to isolate an app into a new App Service plan:
- The App is resource intensive
- The App needs resource in a different geographical region
- The App scales different then other apps hosted in an existing plan.



```
# login
Login-AzureRmAccount

# create the resource group
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceLocation

# create the app service plan
New-AzureRmAppServicePlan `
    -Name $appServicePlanName `
    -Location $resourceLocation `
    -Tier Standard `
    -NumberofWorkers 1 `
    -WorkerSize Medium `
    -ResourceGroupName $resourceGroupName

# create the web app
New-AzureRmWebApp `
    -ResourceGroupName $resourceGroupName `
    -Name $appName `
    -Location $resourceLocation `
    -AppServicePlan $appServicePlanName

# create a deployment slot
New-AzureRmWebAppSlot `
    -ResourceGroupName $resourceGroupName `
    -Name $appName `
    -Slot $appSlotName `
    -AppServicePlan $appServicePlanName

# swap deployment slots
$ParametersObject = @{targetSlot  = "production"}
Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType Microsoft.Web/sites/slots `
    -ResourceName "$appName/$appSlotName" `
    -Action slotsswap `
    -Parameters $ParametersObject `
    -ApiVersion 2015-07-01

# clean up
Remove-AzureRmResourceGroup -Name $resourceGroupName -Force
```






## Additional Resources
- [Azure App Service plans in-depth overview](https://docs.microsoft.com/en-us/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)
- [Deploy your app to Azure App Service](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-deploy)

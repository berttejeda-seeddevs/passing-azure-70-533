# Deploy Web Apps

**Objectives:** Define deployment slots; roll back deployments; implement pre- and post- deployment actions; create, configure, and deploy packages; create App Service plans; migrate Web Apps between App Service plans; create a Web App within an App Service plan


## Quick facts:
- All applications assigned to an App Service Plan share the resources defined by it.
- Basic SKU or higher is necessary to specify the size and scale count of the VMs.
- SKU and Scale of the App Service plan determines the cost and not the number of apps hosted in it.
- An app can be associated with only one App Service plan.
- Clone allows you to copy your app to a new or existing App Service plan in **any** region.
- App services plan still incur charges even if they have no apps associated with it.
- To move an app to a different App Service plan, the plan must be in the **same resource group** and **geographical region**.

## 3 Deployment methods
1. FTP
2.  Kudu (Git /Mercurial or OneDrive / Dropbox)
    * Content Sync from OneDrive or Dropbox
    * Repository-based CI with **auto** sync from **Github**, **Bitbucket** and **VSTS**
    * Repository-based deployment with **manual** sync from a *local* Git.
3.  Web Deploy

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


## Additional Resources
- [Azure App Service plans in-depth overview](https://docs.microsoft.com/en-us/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)
- [Deploy your app to Azure App Service](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-deploy)

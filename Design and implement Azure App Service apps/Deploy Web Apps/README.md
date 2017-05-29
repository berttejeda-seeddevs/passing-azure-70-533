# Deploy Web Apps


## Objectives
- Define deployment slots
- Roll back deployments
- Implement pre- and post- deployment actions
- Create, configure, and deploy packages
- Create App Service plans
- Migrate Web Apps between App Service plans
- Create a Web App within an App Service plan

## Content
### App Service plans
defines
- Region (North Europe)
- Scale count (1, 2, 3, ...)
- Instance size (Small, Medium, Large)
- SKU (Free, Shared, Basic, Standard and Premium)

Quick facts:
- All applications assigned to an App Service Plan share the resources defined by it.
- Basic SKU or higher is necessary to specify the size and scale count of the VMs.
- SKU and Scale of the App Service plan determines the cost and not the number of apps hosted in it.
- An app can be associated with only one App Service plan.

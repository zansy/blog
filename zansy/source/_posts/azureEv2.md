title: Azure Ev2 Notes
author: zansy
toc: true
tags:
  - Azure
categories:
  - 唯有爱与工作不可辜负
date: 2021-10-21 14:47:00
---
[Ev2 (Express v2)](https://ev2docs.azure.net/overview/intro.html) provides secure, automated solutions for deploying Azure resources in public and sovereign clouds. Ev2 orchestrates resource deployment across multiple regions and scale-units. It includes health checks to catch any potential problems before they are widely deployed. By using Ev2, you can comply with safe deployment practices for Azure.
<!--more-->
# Overview
<u>Ev2 enables internal teams to create declarative, automated **deployments** of Azure services for **all regions and clouds**.</u> It uses and extends ARM templates for deploying Azure resources. Ev2 extends ARM templates by providing a global service topology model and gradual rollout of changes.

When you use Ev2, you get the following benefits:

- Fewer live site incidents by following safe deployment practices (SDP)
- Orchestration to safely rollout across multiple regions and resources
- A portal to track rollouts, visualize models, and view rollout analytics
- Regulated role isolation with no direct human access to privileged resources
- Secure multi-tenancy
- Secure secret-handling at deployment time
- Full-fidelity audit trail of rollouts in all Azure clouds

# Tutorial
Ev2 **enables you to deploy your service to Azure in stages.** Rather than deploying your service all at once, and then discovering a problem that affects all customers, you minimize downtime by catching problems before they are widely deployed. With Ev2, you deploy to a region and then assess the health of your service. The rollout to other regions is paused until the health checks have passed. If your service is operating as expected, the rollout continues to more regions where the deployment is again paused for health checks before continuing to deploy in the next region. With this approach, you improve the resiliency and availability of your service.

Ev2 helps you automate the staged rollout of your service. You define the regions to deploy to, the order of deployment to those regions, and the health checks to run after each stage. Ev2 orchestrates the steps you’ve defined.

In this tutorial, you <u>deploy a sample web app to West US</u>. Your deployment waits for 3 minutes and then proceeds to the East US region. The 3-minute wait is meant to simulate health checks you would typically perform as part of a staged rollout.

## Artifacts
![](/images/Ev2/tutorial-artifacts.png)

## ARM template
In the ARM template, you **define the resources to deploy for your service.** The template you create for Ev2 is no different than a template you or customers would create to deploy solutions to Azure.

start/ServiceGroupRoot/Templates/WebApp.Template.json.
```Json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "appService_name": {
            "type": "String"
        },
        "appServicePlan_location": {
            "type": "String"
        },
        "appServicePlan_name": {
            "type": "String"
        },
        "appServicePlan_skuName": {
            "type": "String"
        },
        "servicePackageLink": {
            "type": "securestring",
            "metadata": {
                "description": "The URL for the GitHub repository that contains the project to deploy."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Web/serverfarms",
            "apiVersion": "2018-02-01",
            "name": "[parameters('appServicePlan_name')]",
            "location": "[parameters('appServicePlan_location')]",
            "sku": {
                "name": "[parameters('appServicePlan_skuName')]"
            },
            "properties": {
                "name": "[parameters('appServicePlan_name')]"
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-11-01",
            "name": "[parameters('appService_name')]",
            "location": "[parameters('appServicePlan_location')]",
            "kind": "WebApp",
            "identity": {
                "type": "SystemAssigned"
            },
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlan_name'))]"
            ],
            "tags": {
                "[concat('hidden-related:', resourceId(resourcegroup().name,'Microsoft.Web/serverfarms', parameters('appServicePlan_name')))]": "empty"
            },
            "properties": {
                "name": "[parameters('appService_name')]",
                "serverFarmId": "[resourceId(resourcegroup().name,'Microsoft.Web/serverfarms', parameters('appServicePlan_name'))]",
                "kind": "WebApp",
                "httpsOnly": true
            },
            "resources": [
                {
                    "type": "Extensions",
                    "apiVersion": "2018-11-01",
                    "name": "MSDeploy",
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/Sites', parameters('appService_name'))]"
                    ],
                    "properties": {
                        "packageUri": "[parameters('servicePackageLink')]"
                    }
                }
            ]
        }
    ]
}
```

## ARM template parameters
To **reuse your template for multiple environments**, you add parameters. During deployment, you pass in different values for those parameters. 

start/ServiceGroupRoot/Parameters/WebApp.parameters.json.
```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "paths": [
      {
        "parameterReference": "servicePackageLink"
      }
    ],
    "parameters": {
      //there's a solution that lets you use a single parameter file. Instead of hard-coding the parameter values, you add placeholder text. During deployment, Ev2 swaps out that text with values that are relevant to the current scope.
      "appServicePlan_location": {
        "value": "__LOCATION__"
      },
      "appServicePlan_skuName": {
        "value": "__SAMPLE_SKU__"
      },   
      //Change the text to a value that is unique to you. Keep in mind that each web app must have unique name across Azure, so you'll want to use a name that is unlikely to be used by someone else. For example, you can use your initials and the date. The actual parameter values might look similar to:{yourprefix} like "value": "{yourprefix}WebApp-__LOCATION_ABBR__"   
      "appService_name": {
        "value": "tf0629WebApp-__LOCATION_ABBR__"
      },
      "appServicePlan_name": {
        "value": "tf0629WebApp-__LOCATION_ABBR__-AppPlan "
      },
      //Use the paths property to specify any additional files that need to be uploaded during deployment. These files typically perform set-up steps on the newly created resources. In this tutorial, the zip file that contains the application code to deploy for the web app. After the web app is deployed, this code is added to the app so you have a fully-functional web app.
      "servicePackageLink": {
        "value": "bin\\WebApp.zip"
      }
    }
}
```

## Service Model
In the service model, you **define the topology of your service**. You **describe the subscription, regions, resource groups and resources to create**. You provide the path to your ARM template and parameter file to use for deploying to the resource groups.

Final service model file looks like:
```JSON
{
  "$schema": "https://ev2schema.azure.net/schemas/2020-01-01/serviceModel.json",
  //Provide metadata values for your service. The property contains the name of your service. The property contains your description of the environment you are deploying to.
  "serviceMetadata": {
    "serviceGroup": "Contoso-MktPlace",
    "environment": "Test"
  },
  //serviceResourceGroupDefinitions as containing the model and serviceResourceGroups as containing implementations of that model.

  //In serviceResourceGroupDefinitions, you create a reusable definition for the resources to deploy.
  "serviceResourceGroupDefinitions": [
  {
    "name": "ContosoAppGroup",
    "serviceResourceDefinitions": [
    {
      "name": "ContosoWebApp",
      "composedOf": 
      {
        //provide the path to the ARM template and parameter file. There’s also a path for rollout parameters.
        "arm": 
        {
          "templatePath": "Templates\\WebApp.Template.json",
          "parametersPath": "Parameters\\WebApp.Parameters.json"
        },
        "extension": 
        {
          "rolloutParametersPath": "Parameters\\WebApp.RolloutParameters.json"
        }
      }
    }]
  }],
  //add a service resource group for the West US region.
  "serviceResourceGroups": [
  {
    "azureResourceGroupName": "demoEv2ContosoWest",
    "location": "West US",
    "instanceOf": "ContosoAppGroup",
    "azureSubscriptionId": "00000000-0000-0000-0000-000000000000",
    "scopeTags": [
    {
      "name": "MyAppCompute"
    }],
    "serviceResources": [
    {
      "name": "ContosoWebAppWestUS",
      "instanceOf": "ContosoWebApp",
      "scopeTags": [
      {
        "name": "WestUSRegion"
      }]
    }]
  },
  //Add a service group for East US.
  {
    "azureResourceGroupName": "demoEv2ContosoEast",
    "location": "East US",
    "instanceOf": "ContosoAppGroup",
    "azureSubscriptionId": "00000000-0000-0000-0000-000000000000",
    "scopeTags": [
    {
      "name": "MyAppCompute"
    }],
    "serviceResources": [
    {
      "name": "ContosoWebAppEastUS",
      "instanceOf": "ContosoWebApp",
      "scopeTags": [
      {
        "name": "EastUSRegion"
      }]
    }]
  }]
}
```
## Rollout spec
So far, you've **defined the resources to deploy in the ARM template**. You've **defined the topology of your service in the service model**. Now, you need to **lay out the steps to take to create the topology. You create the steps in the rollout spec.**
```JSON
{
  "$schema": "https://ev2schema.azure.net/schemas/2020-01-01/rolloutSpecification.json",
  "rolloutMetadata": {
    //The service model path is the path to the file you just created.
    "serviceModelPath": "ServiceModel.json",
    //The scope binding path is the path to the file.
    "scopeBindingsPath": "ScopeBindings.json",
    //The name is any name you would like to use for the rollout.
    "name": "Contoso WebApp 1.0.1",
    //The rollout type indicates whether it is a major, minor, hotfix release.
    "rolloutType": "Major",
    "buildSource": {
      "parameters": {
        "versionFile": "buildver.txt"
      }
    },
    //For email, add your email.
    "notification": {
      "email": {
        "to": "demo@contoso.org"
      }
    }
  },
  //Define the steps. The target names refer to service resources that you created in the service model. 
  "orchestratedsteps": [
    {
      "name": "West Region",
      "targetType": "ServiceResource",
      "targetName": "ContosoWebAppWestUS",
      //The first action deploys and the second action waits. 
      "actions": [
        "Deploy",
        "Wait/wait3Minutes"
      ]
    },
    {
      "name": "East Region",
      "targetType": "ServiceResource",
      "targetName": "ContosoWebAppEastUS",
      "actions": [
        "Deploy"
      ],
      //The "East Region" step depends on the "West Region" step, which means it starts only after that step has completed. If the West region deployment fails, the East region deployment is not run.
      "dependsOn": [
        "West Region"
      ]
    }
  ]
}
```

## Scope Bindings
In the scope bindings file, you **define common values to use for different scopes**(those placeholder values you've added to files). These values are used for matching scope tags in the service groups.

Full scope bindings file is:
```JSON
{
  "$schema": "https://ev2schema.azure.net/schemas/2020-01-01/scopeBindings.json",
  "contentVersion": "0.0.0.1",

  "scopeBindings": [
  {
    //To see where you used the MyAppCompute tag, open your service model. You'll see it's used for the service resource group.
    "scopeTagName": "MyAppCompute",
    "bindings": [
    {
      //The value for this parameter is dynamically set to S1 for those deployments.
      "find": "__SAMPLE_SKU__",
      "replaceWith": "S1"
    }]
  },
  //Add scope bindings for values that need to vary by region.
  {
    "scopeTagName": "WestUSRegion",
    "bindings": [
    {
      "find": "__LOCATION_ABBR__",
      "replaceWith": "wus"
    },
    {
      "find": "__LOCATION__",
      "replaceWith": "West US"
    },
    {
      "find": "__WESTUS_WAIT_TIME__",
      "replaceWith": "PT3M"
    }]
  },
  {
    "scopeTagName": "EastUSRegion",
    "bindings": [
    {
      "find": "__LOCATION_ABBR__",
      "replaceWith": "eus"
    },
    {
      "find": "__LOCATION__",
      "replaceWith": "East US"
    }]
  }]
}
```
personal note(please correct me if I am wrong):

A search-replace file for replace some parameter setted before by searching <u>scope tag names and placeholder strings</u> to swich different scope.

## Rollout parameters 

Rollout parameters define the parameters for any actions to be run for the service resource. In the rollout spec, you have an action for Wait/wait3Minutes.
```JSON
{
  "$schema": "https://ev2schema.azure.net/schemas/2020-01-01/rolloutParameters.json",
  "contentVersion": "1.0.0.0",
  "wait": [
  {
    //The placeholder text refers to a value in your scope bindings file.
    "name": "wait3Minutes",
    "properties": 
    {
      "duration": "__WESTUS_WAIT_TIME__"
    }
  }]
}
```

## Rollout
You're ready to run the staged rollout. [Use either PowerShell or CLI](https://ev2docs.azure.net/overview/tutorial.html#rollout).

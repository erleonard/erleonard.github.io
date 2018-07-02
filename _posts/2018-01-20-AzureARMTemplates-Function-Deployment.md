---
layout: single
title:  "Azure Resource Manager Templates: Deploy Template With Resource Functions"
date:   2018-01-20 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - ArmTemplates
  - DevOps
---

This post is part of a series. The previous posts in the series can be found here: 
 - [ARM Templates: Substring Function](http://erleonard.me/azure/AzureARMTemplates-SubstringFunction/)
 - [ARM Templates: Split Function](http://erleonard.me/azure/AzureARMTemplates-SplitFunction/)

 In the first article, we worked with the parameter subnetName to manipulate the string to remove the 0 at the end and creating a sequential count for each additional subnet. We used the following functions: substring, length, concat, and sub.

The second article, we manipulated the addressPrefix parameter to extract the subnet address and get each octet. We used the split function to achieve this.

Continuing with our simple scenario, we are building a single virtual network and create multiple sequential subnets for the deployment. Our final task will be to combine everything and deploy our vNet.

To assemble our template, we need to use another numeric function, **CopyIndex**. When CopyIndex is used it returns the current iteration of the loop. The iteration value starts at zero. You can use an integer as a parameter to set the starting value.

In our scenario, we will set the CopyIndex value at 1.

```json
"addressPrefix": "[concat(variables('SeperatedValues')[0],'.',variables('SeperatedValues')[1],'.', copyIndex(1),'.0/24')]"
```

In conjunction  with CopyIndex, we need to use the **Copy** element to specify the number of iterations (count) and a name for this loop (name).

The following example, we set the copyindex() with a starting value of 1 and we have set the Copy element with the required name and count.

```json
{
    "apiVersion": "2017-08-01",
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "name": "[concat(parameters('name'),'/',variables('subnetModName'),copyindex(1))]",
    "location": "[resourceGroup().location]",
    "properties": {
        "addressPrefix": "[concat(variables('SeperatedValues')[0],'.',variables('SeperatedValues')[1],'.', copyIndex(1),'.0/24')]"
    },
    "copy": {
        "name": "subnetCount",
        "count": "[parameters('subnetCount')]"
    },
    "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', parameters('name'))]"
    ]
}
```
Result: **Failure**

Deploying the above template fails as it's trying to deploy the subnets in parallel and it does not have the necessary information.
![DeploymentFailure]({{ site.url }}{{ site.baseurl }}/assets/images/2018/2018-01-20-DeploymentFailure.png)

For the template deployment to succeed we need to modify the **Copy** element to instruct the template not to do it in parallel but serially and set the batchSize to the number of instances to deploy at a time.

For example, to serially deploy a resource on at a time, use:
```json
"copy": {
        "name": "subnetCount",
        "count": "[parameters('subnetCount')]",
        "mode": "Serial",
        "batchSize": 1
    }
```

Combining everything we have looked at in this series we will be able to deploy our scenario that we have been using to build a single virtual network and create multiple sequential subnets.

Here is the completed template:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string",
            "defaultValue": "VNET",
            "metadata": {
                "description": "vNet Name"
              }
        },
        "addressPrefix": {
            "type": "string",
            "defaultValue": "10.2.0.0/20",
            "metadata": {
                "description": "vNet Address prefix"
              }
        },
        "subnetAddressPrefix": {
            "type": "string",
            "defaultValue": "10.2.0.0/24",
            "metadata": {
                "description": "Subnet Address prefix"
              }
        },
        "subnetCount": {
            "type": "int",
            "defaultValue": "2",
            "minValue": 2,
            "maxValue": 10,
            "metadata": {
                "description": "Number of subnets to deploy, minimum value of 2 and max of 10."
                }
            }
        },
        "variables": {
            "subnetName": "subnet0",
            "subnetModName": "[substring(variables('subnetName'),0,sub(length(variables('subnetName')), 1))]",
            "SeperatedValues": "[split(parameters('addressPrefix'),'.')]"
        },
        "resources": [
            {
                "apiVersion": "2017-08-01",
                "name": "[parameters('name')]",
                "type": "Microsoft.Network/virtualNetworks",
                "location": "[resourceGroup().location]",
                "properties": {
                    "addressSpace": {
                        "addressPrefixes": [
                            "[parameters('addressPrefix')]"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnetName')]",
                            "properties": {
                                "addressPrefix": "[parameters('subnetAddressPrefix')]"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2017-08-01",
                "type": "Microsoft.Network/virtualNetworks/subnets",
                "name": "[concat(parameters('name'),'/',variables('subnetModName'),copyindex(1))]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "addressPrefix": "[concat(variables('SeperatedValues')[0],'.',variables('SeperatedValues')[1],'.', copyIndex(1),'.0/24')]"
                },
                "copy": {
                    "name": "subnetCount",
                    "count": "[parameters('subnetCount')]",
                    "mode": "Serial",
                    "batchSize": 1
                },
                "dependsOn": [
                    "[concat('Microsoft.Network/virtualNetworks/', parameters('name'))]"
                ]
            }
        ]
    }
```

The code is also available on Github: [https://github.com/erleonard/AzureARMTemplates/blob/master/Networking/spoke.json](https://github.com/erleonard/AzureARMTemplates/blob/master/Networking/spoke.json)
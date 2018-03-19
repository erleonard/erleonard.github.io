---
layout: single
title:  "ARM Templates: Networking"
date:   2018-02-24 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - ArmTemplates
  - DevOps
---


It’s been a very busy last few months for me with work and doing lots of deployments in Azure. What I like doing the most is writing ARM templates for each deployment as I know the next day or sometimes a little later that I will need to perform a deployment that is very similar.

A great example is deploying a virtual network for the DMZ, in most cases, it’s always a public-facing resource with a restricted backend that holds a database(s) and/or storage.

Usually to deploy a virtual network you need to figure out a few things first:
1.	Which region will you create the virtual network?
2.	How large does my address space need to be?
3.	How many subnets to I need to create?
4.	Do I need to connect to on-premise resources?
5.	How many resources will be used on the virtual network?

Answering these questions will help you create your ARM template, and most of all help you figure out what parameters you template needs. In the example below, I'm creating a vNet with a large address space of 172.16.0.0/16 and creating two subnets. The subnets are called Frontend and Backend, and this will be used to segment my web services and my SQL database.


``` json
{
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "apiVersion": "2017-10-01",
            "location": "[variables('Location')]",
            "properties": {
                "provisioningState": "Succeeded",
                "addressSpace": {
                    "addressPrefixes": [
                        "172.16.0.0/16"
                    ]
                },
                "subnets": [
                    {
                        "name": "Frontend",
                        "properties": {
                            "addressPrefix": "172.16.1.0/24",
                            "serviceEndpoints": []
                        }
                    },
                    {
                        "name": "Backend",
                        "properties": {
                            "addressPrefix": "172.16.2.0/24",
                            "serviceEndpoints": [
                                {
                                    "service": "Microsoft.Sql",
                                    "locations": [
                                        "[variables('Location')]"
                                    ]
                                },
                                {
                                    "service": "Microsoft.Storage",
                                    "locations": [
                                        "[variables('Location')]",
                                        "canadaeast"
                                    ]
                                }
                            ]
                        }
                    }
                ],
                "virtualNetworkPeerings": [],
                "enableDdosProtection": false,
                "enableVmProtection": false
            },
            "dependsOn": []
        }
```

Awesome you have a virtual network, but it’s pretty boring and doesn’t have anything in it. Well, let’s add a Network Security Group (NSG) to create rules to allow or deny network traffic based on the subnet, connected devices or public IP address resource that is attached to a VM. As well since we are already doing this, I’m going to add service endpoint to my backend subnet to ensure my Azure storage account and Azure SQL database is limited to my virtual network.

``` json
 {
            "type": "Microsoft.Network/networkSecurityGroups",
            "name": "[parameters('nsgBackend')]",
            "apiVersion": "2017-10-01",
            "location": "[variables('Location')]",
            "properties": {
                "securityRules": [],
                "defaultSecurityRules": []
            },
            "dependsOn": []
        },
        {
            "type": "Microsoft.Network/networkSecurityGroups",
            "name": "[parameters('nsgFrontEnd')]",
            "apiVersion": "2017-10-01",
            "location": "[variables('Location')]",
            "properties": {
                "securityRules": [],
                "defaultSecurityRules": []
            },
            "dependsOn": []
        }
```

The NSG's are created by not applied to your virtual network, and we are missing the service endpoints, let's configure the virtual network first:

```json
{
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vNetName')]",
            "apiVersion": "2017-10-01",
            "location": "[variables('Location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "172.16.0.0/16"
                    ]
                },
                "subnets": [
                    {
                        "name": "Frontend",
                        "properties": {
                            "addressPrefix": "172.16.1.0/24",
                            "networkSecurityGroup": {
                                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgFrontEnd'))]"
                            },
                            "serviceEndpoints": []
                        }
                    },
                    {
                        "name": "Backend",
                        "properties": {
                            "addressPrefix": "172.16.2.0/24",
                            "networkSecurityGroup": {
                                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgBackend'))]"
                            },
                            "serviceEndpoints": [
                                {
                                    "service": "Microsoft.Sql",
                                    "locations": [
                                        "[variables('Location')]"
                                    ]
                                },
                                {
                                    "service": "Microsoft.Storage",
                                    "locations": [
                                        "[variables('Location')]",
                                        "canadaeast"
                                    ]
                                }
                            ]
                        }
                    }
                ],
                "virtualNetworkPeerings": [],
                "enableDdosProtection": false,
                "enableVmProtection": false
            },
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgFrontEnd'))]",
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgBackend'))]"
            ]
        }

```

Last part is to associate the NSG and service endpoints to there respective subnets.

```json
,
        {
            "type": "Microsoft.Network/virtualNetworks/subnets",
            "name": "[concat(parameters('vNetName'), '/', parameters('subnetsFrontend'))]",
            "apiVersion": "2017-10-01",
            "properties": {
                "addressPrefix": "172.16.1.0/24",
                "networkSecurityGroup": {
                    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgFrontEnd'))]"
                },
                "serviceEndpoints": []
            },
            "dependsOn": [
                "[resourceId('Microsoft.Network/virtualNetworks', parameters('vNetName'))]",
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgFrontEnd'))]"
            ]
        },
        {
            "type": "Microsoft.Network/virtualNetworks/subnets",
            "name": "[concat(parameters('vNetName'), '/', parameters('subnetsBackend'))]",
            "apiVersion": "2017-10-01",
            "properties": {
                "addressPrefix": "172.16.2.0/24",
                "networkSecurityGroup": {
                    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgBackend'))]"
                },
                "serviceEndpoints": [
                    {
                        "service": "Microsoft.Sql",
                        "locations": [
                            "[variables('Location')]"
                        ]
                    },
                    {
                        "service": "Microsoft.Storage",
                        "locations": [
                            "[variables('Location')]",
                            "canadaeast"
                        ]
                    }
                ]
            },
            "dependsOn": [
                "[resourceId('Microsoft.Network/virtualNetworks', parameters('vNetName'))]",
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgBackend'))]"
            ]
        }
```
There you have it, a simple virtual network deployment with two subnets with NSG's and service endpoint enabled.

As always the code is also available on Github: [https://github.com/erleonard/AzureARMTemplates/blob/master/Networking/spoke.json](https://github.com/erleonard/AzureARMTemplates/blob/master/Networking/spoke.json)
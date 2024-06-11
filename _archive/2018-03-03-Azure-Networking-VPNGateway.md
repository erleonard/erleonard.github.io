---
layout: single
title:  "ARM Templates: VPN Gateway"
date:   2018-03-03 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - ArmTemplates
  - DevOps
---

This post is part of a series. The previous posts in the series can be found here:
- ARM Templates: Networking

In this post, I wanted to talk about creating a Point-to-Site VPN connection. This is something that I do often when a customer wants to create a dev/test environment without exposing it to the outside world. A point-to-site connection is simply a VPN connection from a device to your Azure network gateway.

![VPN Network Overview]({{ site.url }}{{ site.baseurl }}/assets/images/2018/2018-03-03-VPN-Gateway.png)

Building on the previous Azure ARM template, I have added the Gateway Subnet that will contain the public IP address needed to configure the Azure Virtual Network Gateway.

``` json
{
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vNetName')]",
            "apiVersion": "2017-10-01",
            "location": "[variables('Location')]",
            "scale": null,
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
                    },
                    {
                        "name": "GatewaySubnet",
                        "properties": {
                            "addressPrefix": "172.16.0.0/27",
                            "serviceEndpoints": []
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
        },
        {
            "type": "Microsoft.Network/virtualNetworks/subnets",
            "name": "[concat(parameters('vNetName'), '/', parameters('subnetsGateway'))]",
            "apiVersion": "2017-10-01",
            "properties": {
                "addressPrefix": "172.16.0.0/24",
                "serviceEndpoints": []
            },
            "dependsOn": [
                "[resourceId('Microsoft.Network/virtualNetworks', parameters('vNetName'))]"
            ]
        }
```

Next, we need to add a Public IP Address resource to our template that will be used to connect to the Virtual Network Gateway.

``` json
{
"apiVersion": "2017-08-01",
"type": "Microsoft.Network/publicIPAddresses",
"name": "[parameters('newPublicIpAddressName')]",
"location": "[parameters('location')]",
"properties": {
    "publicIPAllocationMethod": "Dynamic"
}
```

Lastly, we need to add the Virtual Network Gateway to the template.

``` json
{
    "apiVersion": "2017-06-01",
    "name": "[parameters('name')]",
    "type": "Microsoft.Network/virtualNetworkGateways",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', parameters('newPublicIpAddressName'))]"
    ],
    "properties": {
        "gatewayType": "[parameters('gatewayType')]",
        "ipConfigurations": [
            {
                "name": "default",
                "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "subnet": {
                        "id": "[parameters('subnetId')]"
                    },
                    "publicIpAddress": {
                        "id": "[resourceId('test', 'Microsoft.Network/publicIPAddresses', parameters('newPublicIpAddressName'))]"
                    }
                }
            }
        ],
        "vpnType": "[parameters('vpnType')]",
        "sku": {
            "name": "[parameters('sku')]",
            "tier": "[parameters('sku')]"
        }
    }
}
```

Once you have completed building your template to deploy a Point-to-Site VPN gateway we need to configure the certificates for the authentication.

In the following steps, you will create a root certificate for the Azure Virtual Network Gateway and a client certificate to be installed on each machine.

To complete the steps, you will need to do this on a Windows 10 workstation or Windows Server 2016.


``` powershell

# Generate root certificate
$rootcert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign

# Generate client certificate
$clientcert = New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\CurrentUser\My" -Signer $rootcert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

# export client certificate
$pwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText
Export-PfxCertificate -Password $pwd -Cert (get-item -Path Cert:\CurrentUser\My\$($clientcert.Thumbprint)) -FilePath c:\cert\clientCert.pfx

# export and apply root cert to Azure Virtual Network Gateway
$ClientCertBase64 = Get-ChildItem Cert:\CurrentUser\My\ -Recurse | Where-Object { $_.Subject -like '*P2SRootCert'}
$([Convert]::ToBase64String($rootcert.Export('Cert'), [System.Base64FormattingOptions]::InsertL
ineBreaks))

Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName "P2SRootCert" -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $ClientCertBase64

```
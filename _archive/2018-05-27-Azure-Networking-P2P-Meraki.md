---
layout: single
title:  "Azure Site to Site VPN with Cisco Meraki"
date:   2018-05-27 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
---

Recently I received a Cisco Meraki Z3 from my work to be used at home as a teleworker gateway.  If you don't know what a Meraki Z3 it's a teleworker gateway that provides enterprise-class firewall, VPN gateway and router all in one.

My coworkers that work with Cisco Meraki day in a and day out love this equipment.

In this article, we are going to create a site to site VPN with the Meraki Z3 and Azure VPN gateway.

![Site-to-Site VPN Gateway cross-premises connection diagram](https://github.com/MicrosoftDocs/azure-docs/raw/master/articles/vpn-gateway/media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

# Azure Steps
The following steps are completed in PowerShell and take roughly 45 minutes to complete due to the creation time required for the VPN gateway.

## Create Resource Group
Create a new resource group in your Azure subscription.
<script src="https://gist.github.com/erleonard/636f95f9224ffc0025b23b7795ac9711.js"></script>

## Create vNet and Subnets
Create a virtual network with two subnets. The first subnet called "default" is where your endpoints in Azure will reside. But you need to create another subnet called "GatewaySubnet", it must be this name, or else Azure won’t treat it as a subnet gateway.

<script src="https://gist.github.com/erleonard/efa541497b0c4de2c9c2b2ecdeff42ba.js"></script>

## Create local network gateway (on-premise)
Create the local network gateway which specifies the specifics of your on-premises location. In the case of this example, my lab has three subnets I want to expose. The GatewayIpAddress parameter refers to your public IP address for your on-premises location.

<script src="https://gist.github.com/erleonard/446207146a5a9365b3c6e40b1e1a6fb0.js"></script>

## Create Public IP address
Create the public IP address for your VPN gateway to be able to communicate back to your on-premises location.

<script src="https://gist.github.com/erleonard/e9099f0ba3489c13ac336eadc594a717.js"></script>

## Create the VPN Gateway Connectivity
Create the VPN gateway connectivity by assigning the subnet and public IP address.

<script src="https://gist.github.com/erleonard/d1acd2144348d9245dfa516211203ce5.js"></script>

## Create the VPN gateway
We will combine all the previous steps to create a VPN gateway. Building a VPN gateway can take some time to complete, for me, it took on average 30 minutes to complete.

<script src="https://gist.github.com/erleonard/0452846cf4579499a1f194de8978e8e1.js"></script>


## Configure the connection
Create and configure the connection between Azure and your on-site router. I used the cmdlet New-Guid to randomly generate a PassPhrase and output me the results so that I can use it in the next step to configuring pfSense.

<script src="https://gist.github.com/erleonard/78a94e7cb54cda7d7582ce12bce1c0c4.js"></script> 

# Cisco Meraki Steps

## Configure site-to-site VPN

1. Login to your Meraki dashboard https://dashboard.meraki.com
2. Go to **Teleworker gateway** and select **site-to-site VPN**
![Site-to-Site VPN Gateway cross-premises connection diagram]({{ site.url }}{{ site.baseurl }}/assets\images\2018\2018-05-27-Meraki-Z3-Menu.png)
3. On the site-to-site VPN page, under type select Hub (Mesh)
![Site-to-Site VPN Gateway cross-premises connection diagram]({{ site.url }}{{ site.baseurl }}/assets\images\2018\2018-05-27-Meraki-Z3-Hub.png)
4. Further down on the page, under VPN settings, select the appropriate local networks that will be available for the VPN connection.
![Site-to-Site VPN Gateway cross-premises connection diagram]({{ site.url }}{{ site.baseurl }}/assets\images\2018\2018-05-27-Meraki-Z3-LocalNet.png)
5. Continuing on the same page, under Organization-wide settings, Add a peer.
![Site-to-Site VPN Gateway cross-premises connection diagram]({{ site.url }}{{ site.baseurl }}/assets\images\2018\2018-05-27-Meraki-Z3-VPNpeering01.png)
6. The non-Meraki VPN peers will appear and add the required information:
 - Name: provide name for the connection
 - Public IP: public IP of the Azure VPN gateway
 - Private subnet: Azure virtual network address space (do not enter individual subnets)
 - IPsec policies: click on default and change the preset to Azure
 - Preshared secret: enter the preshared key you used to create the Azure VPN gateway.
![Site-to-Site VPN Gateway cross-premises connection diagram]({{ site.url }}{{ site.baseurl }}/assets\images\2018\2018-05-27-Meraki-Z3-VPNpeering02.png)
![Site-to-Site VPN Gateway cross-premises connection diagram]({{ site.url }}{{ site.baseurl }}/assets\images\2018\2018-05-27-Meraki-Z3-IPsecPolicies.png)

## Verify connectivity
1. Go to **Teleworker gateway** and select **VPN status**
![Site-to-Site VPN Gateway cross-premises connection diagram]({{ site.url }}{{ site.baseurl }}/assets\images\2018\2018-05-27-Meraki-Z3-VPNstatus01.png)
2. Go to **Non-Meraki peer**, ensure the status color is green.
![Site-to-Site VPN Gateway cross-premises connection diagram]({{ site.url }}{{ site.baseurl }}/assets\images\2018\2018-05-27-Meraki-Z3-VPNstatus02.png)
3. If the status is not green, go to the event log to troubleshoot.

# Lessons Learned
I ran into a few issues during the setup and here are some of the errors I did and how I corrected it.
1. Azure VPN gateway was set to route-based. I had to delete the VPN gateway and recreate the gateway with the VPN type as **Policy-based**
2. When configuring the site-to-site VPN on the Meraki dashboard, ensure the **private subnets** equals the address space configuration for your Azure virtual network.
---
layout: single
title:  "Azure Site-To-Site VPN"
date:   2017-05-22 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - HowTo
  - PowerShell
---
This article will how to create a site-to-site VPN from Azure subscription to my local pfSense virtual router. This is useful when you need to test some hybrid connectivity from Azure.

# Azure Steps

## Create Resource Group
Create a new resource group in your Azure subscription.
<script src="https://gist.github.com/erleonard/636f95f9224ffc0025b23b7795ac9711.js"></script>

## Create vNet and Subnets
Create a virtual network with two subnets. The first subnet called "default" is where your endpoints in Azure will reside. But you need to create another subnet called "GatewaySubnet", it must be this name or else Azure won’t treat it as a subnet gateway.

<script src="https://gist.github.com/erleonard/efa541497b0c4de2c9c2b2ecdeff42ba.js"></script>

## Create local network gateway (on-premise)
Create the local network gateway which specifies the specifics of your on-premises location. In the case of this example, my lab has 3 subnets I want to expose. The GatewayIpAddress parameter refers to your public IP address for your on-premises location.

<script src="https://gist.github.com/erleonard/446207146a5a9365b3c6e40b1e1a6fb0.js"></script>

## Create Public IP address
Create the public IP address for your VPN gateway to be able to communicate back to your on-premises location.

<script src="https://gist.github.com/erleonard/e9099f0ba3489c13ac336eadc594a717.js"></script>

## Create the VPN Gateway Connectivity
Create the VPN gateway connectivity by assigning the subnet and public IP address.

<script src="https://gist.github.com/erleonard/d1acd2144348d9245dfa516211203ce5.js"></script>

## Create the VPN gateway
This will combine all the previous steps and create a VPN gateway. This can take some time to complete, for me, it took on average 30 minutes to complete.

<script src="https://gist.github.com/erleonard/0452846cf4579499a1f194de8978e8e1.js"></script>


## Configure the connection
Create and configure the connection between Azure and your on-site router. I used the cmdlet New-Guid to randomly generate a PassPhrase and output me the results so that I can use it in the next step to configuring pfSense.

<script src="https://gist.github.com/erleonard/78a94e7cb54cda7d7582ce12bce1c0c4.js"></script>


# pfSense Steps

1. Open a web browser and navigate to the pfSense WebGUI.
2. Click on VPN > IPSec and click + Add P1
3. Enter the following details for Phase 1:
    * Key Exchange Version: Auto
    * Remote Gateway: Public IP of Azure from Above
    * Description: Add a description. ex: "pfSense-Azure"
    * Pre-Shared Key: PassPhrase outputed from the previous step.
    * Encryption Algorithm: AES / 256 bits
    * Hash Algorithm: SHA256
    * Phase 1 Lifetime: 10800
4. Click on Show Phase 2 Entries (0) and clock + Add P2
5. Enter the following details for Phase 2:
    * Local Network: Select your LAN network
    * Remote Network: Azure Subnet.
    * Description: Optional
    * Protocol: ESP
    * Encryption Algorithms: AES / 128 Bit (Don’t change anything else)
    * Hash Algorithm: SHA1
    * PFS Key Group: 2 (1024 bit)

6. Click Save and then Apply Settings.

7. Now click Status > IPsec

8. Click Connect VPN. Press F5 to refresh and see the connectivity status as connected.
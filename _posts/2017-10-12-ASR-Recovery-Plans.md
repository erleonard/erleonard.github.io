---
layout: single
title:  "Azure Site Recovery: Recovery Plans"
date:   2017-10-12 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - Azure Site Recovery
---

Continuing on our series of blog posts for Azure Site Recovery we will be configuring the Recovery Services Vault by preparing our infrastructure and creating a Recover Plan.

*This part has to be completed manually.*

In the following steps we will establish disaster recovery protection for on-premises Hyper-V VMs to Azure. 

## Prepare Your Infrastructure

### Step 1: Protection Goal

1. Go to your Recovery Vault your created in the previous article.
2. Under Getting Started, click Site Recovery and select Prepare Infrastructure
3. In Protection Goal, ensure the following options are set:
    - Where are your machines located? **On-Premise**
    - Where do you want to replicate your machines to? **To Azure**
    - Are you machines virtualized? **Yes, with Hyper-V**

### Step 2: Configure your On-Premise Environment

1. In Prepare Source, select **Hyper-V Site** and give it a good descriptive name.
2. Still in Prepare Source, select **Hyper-V Server**.
3. Add Server section, download the Microsoft Azure Site Recovery provider and vault registration key and transfer files to your Hyper-V host.
4. On your Hyper-V host, run the installation of the Azure Site Recovery Provider and, when you receive a prompt, provide the vault registration key.
    - **Note:** Hyper-V host registration can take up to 30 minutes to showup in Azure.

### Step 3: Configure Target Environment

1. In Prepare Target, select your subscription and resource group where the Azure VM's will be created after failover.
2. Storage Account: ensure the storage account that was created previously is selected.
3. Virtual Network: ensure the virtual network that was created previously is selected.

### Step 4: Configure Replication Settings

1. In Replication Policy, select **Create and associate policy**
2. Specify a policy name
3. In Copy frequency, select how often you wish to replicate the data delta (30 seconds, 5 minutes or 15 minutes).
4. In Recovery point retention in hours,  specify how often recovery points containing application-consistent snapshots will be created.
5. In Initial replication start time, select immediately to start the initial replication or you can defer it to non-business hours.
6. In Encrypt data stored on Azure, select **ON**.
7. Click Ok.

## Enable Replication
1. In Source, select the Hyper-V site.
2. In Target, Ensure the following is set:
    - Subscription: Set to correct subscription
    - Post-failover group: set to previously created Resource Group
    - Post-failover deployment model: Resource Manager
    - Storage Account: set to previously created Storage Account
    - Azure Network: set to previously created Virtual Network
        - Subnet: Select the appropriate subnet
3. In Virtual machines, select the VMs you want to replicate.
4. In Configure Properties, select the OS Type.
5. In Replication Settings, review information and click **ok**.
6. Click **Enable Replication**.

You can track progress of the Enable Protection job in Monitoring and Reports, Jobs, Site Recovery jobs. After the Finalize Protection job runs the machine is ready for failover.
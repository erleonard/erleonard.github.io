---
layout: single
title:  "Azure Governance"
date:   2017-11-11 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
---

Governance in Azure or any location is a complex topic that requires a lot of thought and planning. During the DevOps Enterprise Summit, Josh Atwell tweeted an excellent summary from what compliance is VS governance.
<br>

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Governance vs Compliance<br><br>Compliance = checking the box<br>Governance = Awareness and active management of risk<a href="https://twitter.com/CapitalOne?ref_src=twsrc%5Etfw">@capitalone</a> at <a href="https://twitter.com/hashtag/Does17?src=hash&amp;ref_src=twsrc%5Etfw">#Does17</a></p>&mdash; 2017 Josh Atwell🔊🥃 (@Josh_Atwell) <a href="https://twitter.com/Josh_Atwell/status/930483619281248256?ref_src=twsrc%5Etfw">November 14, 2017</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

### Challenges
What are some of the challenges you will face?
- How do I meet our legal requirements for data soverignty?
- How do I enable charge back across departments / Teams
- How do I ensure that someone does not inadvertently change a critical system?

To address these questions, you can break it down into 4 pillars:
1. Naming Convention
2. Policies
3. Resource Tags
4. Resource Locks

### Naming Convention
#### Subscriptions
Verbose Azure subscription names make understanding the context and purpose of each subscription clear.

Sample naming convention: <br>

**Company**|**Department**|**Product Line or Service**|**Environment**|**Full Name**
:-----:|:-----:|:-----:|:-----:|:-----:
TLAB|satAZUREday|AwesomeService|Production|TLAB satAZUREday AwesomeService Production
TLAB|SocialGaming|AwesomeService|Dev|TLAB satAZUREday AwesomeService Dev

<br>
#### Resources
Verbose azure resources names make it easy to understand the purpose and workload.

**Resource**|**Naming Covention**|**Examples**
:-----:|:-----:|:-----:
Resource Groups: |RG-Region-Type-Subtype/Workload| RG-CC-VM-Identity, RG-CE-Network
Networking:| Vnet-Region-Type-SubType/Workload| Vnet-CC-GW, ILB-CE-SQL
Storage Accounts:|sa[company][department][region][Type][workload]|  sacticamsccvmsql.core.windows.net
Virtual Machines:|[Region][Role][Number]| ccdc01

### Policies

Azure Policy is a service in Azure that you can use to create, assign and manage policy definitions.

Policy definitions enfore different rules and actions over your resources, so those resources stay complian with your corporate standards and service level agreements.

See my blog post on [Azure Policy](http://erleonard.me/azure/Azure-Policy-Preview/) for more information.

### Resource Tags
Resource Tags are extermely important to associate resources with the appropriate tags. ex: department, customer, environment.

Resources tags are flexible and easy to implement. It can be included in your ARM templates.

Examples of common resource tags are:
- BillTo
- Department (or Business Unit)
- Environment (Production, Stage, Development)
- Tier (Web Tier, Application Tier)
- Application Owner
- ProjectName


### Resource Locks
Resource locks enable you to restrict operations on high-value resources where modifying or deleting them would have a significant impact on your applications or cloud infrastructure.

You can apply locks on subscriptions, resource groups and individual resources.
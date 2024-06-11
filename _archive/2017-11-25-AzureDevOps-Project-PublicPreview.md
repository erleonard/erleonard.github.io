---
layout: single
title:  "Azure DevOps Project"
date:   2017-11-25 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - DevOps
  - VSTS
---

Wow! How did I miss this.  Microsoft annouce on November 15th, 2017 the [Azure DevOps Project](https://blogs.msdn.microsoft.com/devops/2017/11/15/azure-devops-project/). This new blade in the azure portal helps accelerate your development, deployment and monitoring of your app.

**Key benefits of a DevOps Project**
- Get up and running with a new app and a full DevOps pipeline in just a few minutes
- Support for a wide range of popular frameworks such as .NET, Java, PHP, Node, and Python
- Start fresh or bring your own application from GitHub
- Built-in Application Insights integration for instant analytics and actionable insights
- Cloud-powered CI/CD using Visual Studio Team Services (VSTS)

**Deployment Walkthrough**
1. Get start by creating [Azure DevOps Project](https://portal.azure.com/#create/Microsoft.AzureProject) now.
2. Start fresh with a new applicant by selecting your application or bring your code. 

![2017-11-25-PIC1]({{ site.url }}{{ site.baseurl }}/assets/images/2017/2017-11-25-PIC01.png)

3. In my case, I selected Static Website and click Next. On the next page, click Next.
4. On the Select and Azure Service to deploy the application, select Web App for Containers or Web App and click Next. For this example, I selected Web App.
5. On the Almost there! page fillout the required information.
![2017-11-25-PIC2]({{ site.url }}{{ site.baseurl }}/assets/images/2017/2017-11-25-PIC02.png)

6. You are done!  When the deployment is complete, you should have a similar blade.

![2017-11-25-PIC3]({{ site.url }}{{ site.baseurl }}/assets/images/2017/2017-11-25-PIC03.png)


Way cool!
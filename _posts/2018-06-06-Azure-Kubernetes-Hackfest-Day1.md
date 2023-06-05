---
layout: single
title:  "Azure Kubernetes Hackfest - Day 1"
date:   2018-06-06 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - Kubernetes
  - Containers
---


I travelled to Toronto this week to attend the Azure Kubernetes Hackfest for Microsoft and Partners to learn more about container orchestration and some of the open source tools that can go with it. Back in 2016 I got excited about containers and started learning about it and attending the local Docker meetup, but then when I switched jobs, I got so busy learning the new job that I never really went back to containers (Big mistake!). Fast forward today and containers, container orchestration and the tools around it have completely changed. Thus why I'm here today in Toronto.

## Containers 101

The morning started off by getting everyone on the same page and review what containers are. This was a good review and helped unearth some of those memories that had gone dormant.

Here is a quick illustration of a container VS. a traditional virtual machine:
![Containers101]({{ site.url }}{{ site.baseurl }}/assets/images/2018/2018-06-06-Containers.png)

Some fascinating resources shared:
- https://12factor.net/
- https://github.com/dotnet-architecture/eShopOnContainers


## Kubernetes Overview
After the container 101 part was complete we dived right into Kubernetes and some of the services that are offered in Azure. Hopefully, I got this right, managing virtual machines on one host is easy to do but as soon as you start getting five hosts, the job of maintaining it becomes pretty hard. Container orchestration tools like Kubernetes are like that; they manage all the containers at scale.

Kubernetes architecture was covered at length, and we discussed the different components.

**Master Components**
- kube-apiserver
- etcd
- kube-scheduler
- kube-controller-manager
- cloud-controller-manager

**Node Components**
- kubelet
- kube-proxy
- Container Runtime


![K8sArchitecture]({{ site.url }}{{ site.baseurl }}/assets/images/2018/2018-06-08-K8s-arch.png)

Afterwards, we went into how Kubernetes fits into Azure and how we can use it.

Azure Container instance (ACI) is a simple way of deploying a container in Azure. 

Azure Container Registry (ACR) allows you to build & store your application container images in a private registry.

## Monitoring Containers
By midafternoon we got to monitoring containers and geek out on this part of the day.  I got the opportunity to work with helm (package manager) to install Prometheus (monitoring and alerting) and Grafana (Analytics platform).

Prometheus was installed easily into my environment but to be fair; they provided the configuration file for the install.

![Prometheus]({{ site.url }}{{ site.baseurl }}/assets/images/2018/2018-06-06-Prometheus.png)

Next up was Grafana, this tool was easy to install, but I lost some time configuring Grafana.  I think I lost 20 minutes trying to figure out why I couldn't get Grafana to get the data from Prometheus, only to realize that I forgot to select the Type. #Facepalm

![GrafanaDataSource]({{ site.url }}{{ site.baseurl }}/assets/images/2018/2018-06-06-Grafana-DataSource.png)

After configuring the data source, I added my dashboard to Grafana and I was done.

![GrafanaDashboard]({{ site.url }}{{ site.baseurl }}/assets/images/2018/2018-06-06-Grafana.png)

The day finished off by discussing Storage, Online Service Broker for Azure and upgrading Kubernetes clusters. Which at that point was way too much information for me and will need some time to review and learn some more.

Today was an excellent day and couldn't have been possible without our presenters today Kevin Harris (@KevinBHar) and Ray Kao (@raykao ) and Mathieu Benoit. They are all excellent speakers and really know what they are talking about.

Tomorrow is the final day of the Hackfest and looking very much forward to it.

Eric.
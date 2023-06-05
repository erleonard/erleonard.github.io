---
layout: single
title:  "Using Azure Cloud Shell in Visual Studio Code"
date:   2017-12-10 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - DevOps
  - Visual Studio Code
---

![VSCode]({{ site.url }}{{ site.baseurl }}/assets/images/2017/2017-12-10-VSCode.png)

Over the course of the last year, I have switched over to Visual Studio Code as my defacto code editor.  It allows me to create PowerShell scripts, work with my JSON templates for Azure, do some cookbook development with Chef and most of all generate blog articles with markdown.

A few days ago, I read a tweet from Michael Crump about integrating Visual Studio Code with Azure Cloud Shell ,and I thought what I great idea this was. 

<blockquote class="twitter-tweet" data-cards="hidden" data-lang="en"><p lang="en" dir="ltr"><a href="https://twitter.com/hashtag/Azure?src=hash&amp;ref_src=twsrc%5Etfw">#Azure</a> Tips and Tricks Part 49 - Add Azure Cloud Shell to Visual Studio <a href="https://twitter.com/code?ref_src=twsrc%5Etfw">@Code</a> - <a href="https://t.co/9Lj82Xmxg5">https://t.co/9Lj82Xmxg5</a></p>&mdash; Michael Crump (@mbcrump) <a href="https://twitter.com/mbcrump/status/930018655710121984?ref_src=twsrc%5Etfw">November 13, 2017</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

The article shows how to open Bash in Cloud Shell, but I wanted to open Cloud Shell with PowerShell. The next steps will show you how I integrated Azure Cloud Shell (PowerShell) with Visual Studio Code.

### Prerequirements

1. Install Visual Studio Code
2. Install Azure Account extension

### Sign into Azure in VSCode

1. Launch Visual Studio Code
2. Access the Command Palette (Shift-Ctrl-P or click View in the toolbar)
3. Type: Azure Sign In
4. You will see a popup that you need to authenticate your device. Click Copy & Open.
![VSCode]({{ site.url }}{{ site.baseurl }}/assets/images/2017/2017-12-10-VSCodeSetup1.png)
5. The Device Login page should launch in your browser, the code is in your clipboard and simply paste the code in the textbox.
![VSCode]({{ site.url }}{{ site.baseurl }}/assets/images/2017/2017-12-10-VSCodeSetup2.png)
6. Click Continue, follow the steps to authenticate your Azure account, and your device will be authenticated.
![VSCode]({{ site.url }}{{ site.baseurl }}/assets/images/2017/2017-12-10-VSCodeSetup3.png)

### Configure VSCode for Azure Cloud Shell (PowerShell)

1. Access the Command Palette (Shift-Ctrl-P or click View in the toolbar)
2. Type: Azure Open PowerShell in Cloud Shell
3. You will be prompted to download and install Node.js, download the installer that is recommended for most users.
4. Install Node.js on your device by accepting the defaults and relaunch VSCode.
5. Open the Command Palette once again and type: Azure Open Powershell in Cloud Shell
6. Popup will appear informing you that it is "Provisioning PowerShell in Cloud Shell may take a few seconds." In my case, it felt more like a minute or two.
![VSCode]({{ site.url }}{{ site.baseurl }}/assets/images/2017/2017-12-10-VSCodeSetup4.png)

If the PowerShell Cloud Shell fails to launch on the first try, just relaunch the Shell, and it should work just fine.

![VSCode]({{ site.url }}{{ site.baseurl }}/assets/images/2017/2017-12-10-VSCodeSetup6.png)
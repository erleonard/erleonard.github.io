---
layout: single
title:  "Secure your IaaS VMs with Duo Security"
date:   2018-02-09 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - Security
---


Deploying a developer or jump VM in the cloud can be easily done but once you add a public IP address to your VM you are exposed. Attackers consistently scan cloud providers IP ranges to locate machines with exposed management end points. Once detected, attackers will try to brute force and exploit these end points, taking advantage of new machines with weak passwords and/or bad configurations.

As an example, I deployed a VM and left it running for 24 hours and had 321 failed login attempts.

![FailedLogin]({{ site.url }}{{ site.baseurl }}/assets/images/2018/2018-02-09-Failed-Logins.PNG)

Using Duo Security, I can add two-factor authentication to all Windows logins via local console or RDP. Duo provides a free version that supports up to 10 users. Check out the plans comparison table to find out which version is right for you: [https://duo.com/pricing](https://duo.com/pricing)

The setup of Duo Authentication was extremely easy and was accomplished in 5 easy steps:
1.	Sign up for a Duo account. [https://signup.duosecurity.com/](https://duo.com/pricing)
2.	Log in to the Duo Admin Panel and navigate to Applications.
3.	Click Protect an Application and locate Microsoft RDP in the applications list. 
![FailedLogin]({{ site.url }}{{ site.baseurl }}/assets/images/2018/2018-02-09-Duo-ProtectApplication.PNG)

4. Click Protect this Application to get your integration key, secret key, and API hostname.
![FailedLogin]({{ site.url }}{{ site.baseurl }}/assets/images/2018/2018-02-09-Duo-ProtectApplicationRDP.PNG)

5.	[Download the Duo Authentication for Windows Logon Installer Package.](https://dl.duosecurity.com/duo-win-login-latest.exe)

**If you want to install the package silently from the command line of powershell, you can use the following command:** duo-win-login-latest.exe /S /V" /qn IKEY="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" SKEY="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" HOST="api-XXXXXX.duosecurity.com" AUTOPUSH="#1" FAILOPEN="#0" RDPONLY="#0""

Once the agent is installed on your server, you simply logout and when you re-login you will be prompted by Duo Security to authenticate with your phone.
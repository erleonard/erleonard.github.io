---
layout: single
title:  "Azure DevTest Labs: Communication and Custom Images Changes"
date:   2017-11-30 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
---

New features have been added to Azure DevTest Labs this week.

### 1. Post communication to your lab users
This new features allows you to post a small message to your lab users when they visit the overview tab, that is opened by default when you go into your lab.

To enable this feature:
1. Go to your Azure DevTest lab in the portal.
2. Under Settings, select Configuration and policies.
3. Under Settings, select Lab announcement.
4. Move the toggle to yes for it to be enabled and fill out the message you want to send to your lab users.
5. Click Save
6. Done! Now you can see your annoucement on the Overview page.

![2017-11-30-Annoucement]({{ site.url }}{{ site.baseurl }}/assets/images/2017/2017-11-30-Annoucement.png)

### 2. Accept License Terms for Custome Images
This new feature is a little more subtle but a welcomed addition, when you are adding a custom image you can now add license terms that must be accepted before the image is accessible to users in the lab.

To enable this feature:
1. Go to your Azure DevTest lab in the portal.
2. Under Settings, select Configuration and policies.
3. Under Virtual machine bases, select Custom Images.
4. Click Add.
5. On the Custom Image page, fill out the required fields:
    - Name: Name of the image.
    - Description: short description of the custom image and software.
    - OS Type: Windows or Linux
    - VHD: Select the appropriate custom image
    - Plan name: specify the name of the image
    - Plan offer: specify the product of the image
    - Plan publisher: specify the publisher of the image
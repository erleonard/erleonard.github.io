---
layout: single
title:  "How I use VSCode, VSTS and GitHub to develop Azure ARM Templates"
date:   2018-01-23 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
  - ArmTemplates
  - DevOps
---

These days I live in Visual Studio Code (VSCode), it's the place where I go to write my Azure ARM templates, where I create my PowerShell scripts, and most of all create my blog articles in markdown.

Developing and deploying Azure ARM templates can be accomplished on your workstation, but you don’t share your code with the community. I thought I would share how I work in VSCode with GitHub and Visual Studio Team Services (VSTS).

The three tools mentioned above working together about allows me to have source control, code versioning, testing and debugging.

Here’s my setup:

### Visual Studio Code
VSCode is an open source code editor that includes support for debugging, embedded Git control, syntax highlighting, intelligent code completion, snippets, and code refactoring. It’s pure awesomeness. You can download here: [https://code.visualstudio.com/](https://code.visualstudio.com/)

I also have the following extensions installed in VSCode:
1.	[Azure Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack)
2.	[Powershell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

### GitHub
GitHub allows you to version your code and allows you to collaborate with others using features such as bug tracking, feature requests, task management, and wikis for every project. You can create a free account here: [https://github.com](https://github.com)

My GitHub repositories contain all the Azure ARM templates that I share. It is also integrated with Visual Studio Code to allow me to manage my code. All my public repositories can are found here: [https://github.com/erleonard](https://github.com/erleonard)

As a side note, GitHub allows you to host your website for you or your projects. I created a one-day conference called satAzureday, and the site was created in its own GitHub repository and hosted by GitHub Pages.

### Visual Studio Team Services
So we have covered the code editor, how the code is sourced controlled and versioned but what about testing and debugging; enter Visual Studio Team Services. VSTS allows for Continous Integration (CI) so that every time I commit code to my repository in GitHub, it will automatically build and test my code.
Here are the steps to accomplish this.
1.	If you don’t have a VSTS account, you can create one here: [https://www.visualstudio.com/team-services](https://www.visualstudio.com/team-services)
2.	Create a new Project and ensure version control is set to Git.
3.	Click Build and Release, and then click Builds.
4.	Click “+ New definition”.
5.	Select the Empty build template
6.	Provide a name and choose “Hosted VS2017” for Agent queue.
7.	Click the Get sources task and select GitHub.
8.	Provide a connection name and then click the Authorize using OAuth button.
9.	A new window will popup and sign-in to GitHub and authorize access to your VSTS account.
![Sample]({{ site.url }}{{ site.baseurl }}/assets/images/2018/2018-01-23-GitHub-Authorize.png)
10.	 Once your GitHub account is authorized, select your repository.
11.	 Click on the + sign in Phase 1 to add a Task.
12.	 In the Add Task window, search for NPM and click Add.
13.	 For this task, we will be installing jsonLint to validate only our JSON syntax. Provide the following information:
  *	Name: npm install jsonLint
  * Command: Custom
  * Command and arguments: install jsonlint -g
![Sample]({{ site.url }}{{ site.baseurl }}/assets/images/2018/2018-01-23-Install-jsonLint.png)
14.	 Add another task, search for Command line and click Add.
15.	 This task we will run jsonLint against the ARM template.
  * Display name: Run jsonLint on ARM template
  * Tool: jsonlint
  * Arguments: azuredeploy.json
16.	Optional, you can add another task to run jsonLint against the parameter file for your ARM template.
17.	Add another task, search for Azure Resource Group Deployment and click Add.
18.	This task will validate the resources provided in your template to deploy into Azure.
  * Display name: Validate Deployment of ARM Template
  * Subscription: Select the subscription you wish to do your deployment to
  * Action: Create or Update Resource Group
  * Resource Group: jsonDeploy
  * Location: Select your desired location
  * Template: Select the ARM template
  * (Optional) Template Parameters: Select the parameter file for the ARM template
  * Deployment Mode: Validate Only
![Sample]({{ site.url }}{{ site.baseurl }}/assets/images/2018/2018-01-23-ARM-Deploy-Validate.png)

19.	Click on Save & Queue and select Save.

The last part is that we need to enable Continous Integration to automatically test the ARM template every time there is a commit to the repository.
1.	Continuing from the previous step, click on Triggers.
2.	Under Continuous integration, click on your repository and click on the checkbox to enable continuous integration.
3.	Save one more time, and we are done.

The next time code is committed to the repository, VSTS will detect the commit and run the requested build tasks to validate and debug the ARM template.

![Sample]({{ site.url }}{{ site.baseurl }}/assets/images/2018/2018-01-23-CI-Completed.png)
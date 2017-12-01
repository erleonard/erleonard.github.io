---
layout: single
title:  "Azure DevTest Labs: Public Repo"
date:   2017-11-01 14:17:12 -0500
categories:
  - Azure
tags:
  - Azure
---

Adding custom artifacts and templates to your Azure DevTest labs offers a great way to enhance your DevTest experience. To add this functionality to your DevTest Lab will you need to create a Git repository. <<add more details>>

In this example, I will show you how to create a repository in GitHub and then attach it to your Azure DevTest Lab.

### Create Repository in GitHub
1. Sign in or create an account with GitHub
2. Create a repository.
    - Not sure how to create a repository, check out GitHub help for [instructions](https://help.github.com/articles/create-a-repo/).
3. In your new repository create two folders; Artifacts and Templates
4. In the upper-right corner, select the profile image, and then select Settings.
5. In the Personal settings menu on the left, select Developer settings
6. In the Developer settings menu on the left, select Personal access tokens
7. Select Generate new token.
8. In the New personal access token page, provide a description (ex: Azure-DTL-PROD), and then select Generate Token.
9. In the Personal Access token pages, make sure to copy your new personal access token now. You won’t be able to see it again.
    - Warning: Treat your tokens like passwords and keep them secret.
10. From here we can attach your lab with the repository.

### Attach Repository to Azure DevTest Labs
1. Go to your Azure DevTest lab in the portal.
2. Under Settings, select Configuration and policies.
3. Under External resources, select Repositories.
4. Click Add
5. On the Repositories page, fill out the required fields:
    - Name: provide name for the repository
    - Git clone URI: get the HTTPS link to the GitHub repository
    - Branch: name of the branch (ex: master)
    - Personal Access Token: The personal access token you generated in the previous section
    - Folder Path
        - Artifacts: path to the artifacts folder in your repository
        - Templates: path to the templates folder in your repository
6. Click Save

Once you have completed the two sections above you should be able to see your repository in the repositories section under External Resources.

Next steps will be to populate this repository.
---
layout: single
title:  "How to use GitHub Actions and Azure Active Directory Federated Identity"
date:   2023-06-05 12:00:00 -0500
toc: false
toc_icon: "cloud"
categories:
  - Azure
  - GitHub
tags:
  - Azure
  - GitHub
  - OIDC
---

GitHub Actions is a powerful tool for automating workflows and deploying applications to various platforms. One of the challenges of using GitHub Actions is how to securely authenticate and authorize access to Azure resources without exposing secrets or keys. Fortunately, there is a solution: using OpenID Connect (OIDC) tokens with Azure Active Directory Federated Identity.

In this blog post, I will show you how to set up Federated Identity using the Azure CLI and use it in a GitHub repository to securely deploy to Azure.

## What is Federated Identity?

Federated Identity is a feature of Azure Active Directory that allows you to trust tokens issued by external identity providers, such as GitHub, and use them to access Azure resources. This way, you don't need to store any secrets or keys in your GitHub repository or workflow, and you can leverage the existing identity and permissions of your GitHub account.

## How does it work?

The basic steps are:

1. Create an Azure Active Directory application and a service principal that can access your Azure resources.
2. Add a federated credential for the Azure Active Directory application that trusts GitHub as an OIDC provider.
3. Create a GitHub Actions workflow that requests an OIDC token from GitHub and uses it with the azure/login action to authenticate with Azure.

## Prerequisites
- A GitHub account
- An Azure Subscription
    - You must have sufficient permissions to deploy resources and assign RBAC
    - You must have sufficient permissions to register an application with your Azure AD tenant
- Azure CLI - [installation instruction](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)
- GitHub CLI - [installation instruction](https://cli.github.com/)

## Step 0: Set Variables
Set your variables that you will use in this script to create the Azure Active Directory application, federated credentials, assign RBAC permission for Azure and configure GitHub workflows.
```bash
GH_ENVIRONMENT=""
GH_USERNAME=""
GH_REPO=""
GH_BRANCH=""
GH_OIDC_NAME=GitHub-$GH_USERNAME-$GH_ENVIRONMENT-OIDC
GH_ACTION_NAME=$GH_USERNAME-$GH_REPO-Actions
AZURE_TENANT_ID=$(az account show --query tenantId --output tsv)
AZURE_SUBSCRIPTION_ID=$(az account show --query id -o tsv)
AZURE_RESOURCEGROUP=""
```

## Step 1: Create an Azure Active Directory application and a service principal
You create an Azure Active Directory application and a service principal using the Azure CLI.

```bash
app_id=$(az ad app create --display-name $GH_OIDC_NAME --query "appId" -o tsv)
az ad sp create --id $app_id
```

## Step 2: Add a federated credential for the Azure Active Directory application
You add the federated credential for the Azure Active Directory application using the Azure CLI.

```bash
postBody='{"name":"'${GH_ACTION_NAME}'", '\
'"issuer":"https://token.actions.githubusercontent.com", '\
'"subject":"repo:'${GH_USERNAME}'/'${GH_REPO}':ref:refs/heads/'${GH_BRANCH}'", '\
'"description":"A GH federated id for the '${GH_USERNAME}'/'${GH_REPO}'", '\
'"audiences":["api://AzureADTokenExchange"]}'

az ad app federated-credential create --id $app_id --parameters "${postBody}"
```

## Step 3: Role Assignment
Assign the contributor role of the application on the Azure resource group.

```bash
az role assignment create \
--role contributor \
--subscription $AZURE_SUBSCRIPTION_ID \
--assignee-object-id $app_id \
--assignee-principal-type ServicePrincipal \
--scope "/subscriptions/$AZURE_SUBSCRIPTION_ID/resourceGroups/$AZURE_RESOURCEGROUP"
```

## Step 4:
Add the Azure AD Application (client id), subscription (id) and tenant (id)
```bash
gh secret set AZURE_CLIENT_ID --app actions --body $app_id --repos $GH_USERNAME/$GH_REPO
gh secret set AZURE_TENANT_ID --app actions --body $AZURE_TENANT_ID --repos $GH_USERNAME/$GH_REPO
gh secret set AZURE_SUBSCRIPTION_ID --app actions --body $AZURE_SUBSCRIPTION_ID --repos $GH_USERNAME/$GH_REPO
```

## Step 5: Create a GitHub Actions workflow that requests an OIDC token from GitHub and uses it with the azure/login action

You create a GitHub Actions workflow that requests an OIDC token from GitHub and uses it with the azure/login action to authenticate with Azure. The workflow should include the following steps:

- Set up permissions for requesting an OIDC token by adding `id-token: write` to your workflow
- Set up permissions for actions/checkout by adding `contents: read` to your workflow
- Request an OIDC token from GitHub by using environment variables
- Use other Azure actions or commands to interact with your Azure resources

```yaml
name: azure-oidc-test
on:
  workflow_dispatch:

permissions:
      id-token: write
      contents: read
      
jobs: 
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Az CLI login'
      uses: azure/login@v1
      with:
          client-id: ${{ secrets.AZURE_CLIENT_ID }}
          tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
  
    - name: 'Run Azure CLI commands'
      run: |
          az account show
          az group list
          pwd
```

## Conclusion

Using OpenID Connect tokens with GitHub Actions and Azure Active Directory Federated Identity is a secure and convenient way to access your Azure resources without exposing any secrets or keys. You can leverage your existing GitHub identity and permissions, and enjoy the benefits of automated workflows and deployments.

I hope you found this blog post helpful. If you have any questions or feedback, please let me know in the comments below.

Eric.

### Reference
[Connect GitHub and Azure](https://learn.microsoft.com/en-us/azure/developer/github/connect-from-azure)
[Create a trust relationship between a user-assigned managed identity and an external identity provider](https://learn.microsoft.com/en-us/azure/active-directory/workload-identities/workload-identity-federation-create-trust-user-assigned-managed-identity)
[Configuring OpenID Connect in Azure](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-azure)
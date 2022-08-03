# Workshop Steps
If you don't have the Azure CLI installed, install it now as follows

https://docs.microsoft.com/en-us/cli/azure/install-azure-cli

Login to the Azure CLI:

**Command** 

> az login

```shell
❯ az login
The default web browser has been opened at https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize. Please continue the login in the web browser. If no web browser is available or if the web browser fails to open, use device code flow with `az login --use-device-code`.
[
  {
    "cloudName": "AzureCloud",
    "homeTenantId": "yyyyyy",
    "id": "hhhhhhhh",
    "isDefault": true,
    "managedByTenants": [],
    "name": "Sponsorship",
    "state": "Enabled",
    "tenantId": "yyyyyy",
    "user": {
      "name": "pas.apicella@snyk.io",
      "type": "user"
    }
  }
]
```

For alternative authentication methods see: https://docs.microsoft.com/en-us/cli/azure/authenticate-azure-cli#sign-in-with-credentials-on-the-command-line

---

## Lab 1: Scanning from Source Control

**Step 1 - Import a GitHub repository to Azure repos (20 mins)**

In order for us to import a public GitHub repository into Azure repos we will need to gather the following information and pass those as parameters. Make sure you are alreday logged into the Azure portal from the CLI as above.

Logged in to your Azure account, navigate to Azure DevOps: https://dev.azure.com/**<YOUR_ORG_NAME_HERE>**/

*-> Note that your Azure Org name is the user name that you created your account under e.g.  **<YOUR_ORG_NAME_HERE>** = your Azure user name

You will need an empty Azure DevOps Project for this lab. You can create a new project in Azure DevOps or even use the Azure CLI if you prefer. Name your project **snyk-azure-project**

**CLI Method:**

```shell
> az devops project create --name snyk-azure-project --org https://dev.azure.com/**<YOUR_ORG_NAME_HERE>** --source-control git --visibility private
```
**UI Method:**

![alt tag](https://i.ibb.co/jgy4ytt/snyk-azure-workshop-10.png)

**Import Parameters**

1. git-source-url = https://github.com/tess-snyk/juice-shop
2. project = name of the empty azure repos project that you created above **snyk-azure-project**
3. org = Your Azure DevOps organization URL https://dev.azure.com/**<YOUR_ORG_NAME_HERE>**
4. repository = Use the same name as project above

**Import as follows**

Note: You may encounter this error so if you do please select "**Y**" and continue for the command to complete

```shell
The command requires the extension azure-devops. Do you want to install it now? The command will continue to run after the extension is installed. (Y/n): y
```
**Command**

> az repos import create --git-source-url https://github.com/tess-snyk/juice-shop --project snyk-azure-project --org https://dev.azure.com/**<YOUR_ORG_NAME_HERE>** --repository snyk-azure-project

```shell
> az repos import create --git-source-url https://github.com/tess-snyk/juice-shop --project snyk-azure-project --org https://dev.azure.com/**<YOUR_ORG_NAME_HERE>** --repository snyk-azure-project
{
  "detailedStatus": {
    "allSteps": [
      "Processing request",
      "Analyzing repository objects",
      "Storing objects",
      "Storing index file",
      "Updating references",
      "Import completed successfully"
    ],
    "currentStep": 6,
    "errorMessage": null
  .....
```
Once complete the following should exist in your Azure DevOps ORG 

![alt tag](https://i.ibb.co/j5RhxFG/AR-Success.png)

If you have any trouble this guide explains how this command works

https://docs.microsoft.com/en-us/cli/azure/repos/import?view=azure-cli-latest
# Introduction to Snyk on Azure Workshop

Available in the Azure Marketplace, Snyk helps developers build their Azure workloads securely

In this **hands-on** workshop we will achieve the follow:

* Step 1 Import a GitHub repository to Azure repos
* Step 2 Import a DockerHub repository container image to ACR
* Step 3 Setup Azure Repos Integration
* Step 4 Setup ACR Integration
* Step 5 Using VS Code to Secure your code as you develop
* Step 6 Using Snyk IaC to test ARM templates
* Step 7 Using Snyk Kubernetes Integration on AKS

## Prerequisites

* public GitHub account - http://github.com
* git CLI - https://git-scm.com/downloads
* Microsoft Azure Subscription - https://azure.microsoft.com/en-au/free/
* Azure CLI - https://docs.microsoft.com/en-us/cli/azure/install-azure-cli
* snyk CLI - https://support.snyk.io/hc/en-us/articles/360003812538-Install-the-Snyk-CLI
* Registered account on Snyk App - http://app.snyk.io
* Docker Hub Account - http://hub.docker.com

# Workshop Steps

_Note: It is assumed you have already logged into Azure using the Azure CLI as shown below_

If you don't have the Azure CLI installed, install it now as follows

https://docs.microsoft.com/en-us/cli/azure/install-azure-cli

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

## Step 1 Import a GitHub repository to Azure repos

In order for us to import a public GitHub repository into Azure repos we will need to gather the following information and pass those as parameters. Make sure you are alreday logged into the Azure portal from the CLI as per above

1. git-source-url = https://github.com/papicella/dotNET-goof-v2
2. project = name of an empty azure repos project you might have to create that before importing
3. org = Azure DevOps organization URL
4. repository = Use the same name as project above

* Import as follows

```shell
az repos import create --git-source-url https://github.com/papicella/dotNET-goof-v2 --project snyk-azure-project --org https://dev.azure.com/pasapicella0207 --repository snyk-azure-project
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
  },
  "importRequestId": 9,
  "parameters": {
    "deleteServiceEndpointAfterImportIsDone": null,
    "gitSource": {
      "overwrite": false,
      "url": "https://github.com/papicella/dotNET-goof-v2"
    },
    "serviceEndpointId": null,
    "tfvcSource": null
  },
  "repository": {
    "defaultBranch": null,
    "id": "c46382e4-6686-439c-877d-766dd0203d21",
    "isDisabled": false,
    "isFork": null,
    "name": "snyk-azure-project",
    "parentRepository": null,
    "project": {
      "abbreviation": null,
      "defaultTeamImageUrl": null,
      "description": null,
      "id": "1b8473fa-1854-49fb-aad5-65b213c4f302",
      "lastUpdateTime": "2022-01-06T00:25:29.357Z",
      "name": "snyk-azure-project",
      "revision": 76,
      "state": "wellFormed",
      "url": "https://dev.azure.com/pasapicella0207/_apis/projects/1b8473fa-1854-49fb-aad5-65b213c4f302",
      "visibility": "private"
    },
    "remoteUrl": "https://pasapicella0207@dev.azure.com/pasapicella0207/snyk-azure-project/_git/snyk-azure-project",
    "size": 0,
    "sshUrl": "git@ssh.dev.azure.com:v3/pasapicella0207/snyk-azure-project/snyk-azure-project",
    "url": "https://dev.azure.com/pasapicella0207/1b8473fa-1854-49fb-aad5-65b213c4f302/_apis/git/repositories/c46382e4-6686-439c-877d-766dd0203d21",
    "validRemoteUrls": null,
    "webUrl": "https://dev.azure.com/pasapicella0207/snyk-azure-project/_git/snyk-azure-project"
  },
  "status": "completed",
  _"url": "https://dev.azure.com/pasapicella0207/snyk-azure-project/_apis/git/repositories/c46382e4-6686-439c-877d-766dd0203d21/importRequests/9"_
}
```
* Once complete the following should exist in your Azure DevOps ORG 

![alt tag](https://i.ibb.co/sy4KyYr/snyk-azure-workshop-1.png)

If you have any trouble this guide explains how this command works

https://docs.microsoft.com/en-us/cli/azure/repos/import?view=azure-cli-latest

## Step 2 Import a DockerHub repository container image to ACR

In order to import a container image from DockerHub we will need a container registry on Azure. Using the following link create a container registry called "**snykazureregistry**". If you already have a container registry you can simply use that and skip this but please make sure you use your registry name in the commands that follow.

https://docs.microsoft.com/en-au/azure/container-registry/container-registry-get-started-portal

![alt tag](https://i.ibb.co/FWhqznF/snyk-azure-workshop-2.png)

* Login to the container registry as shown below

```shell
❯ az acr login --name snykazureregistry
Login Succeeded
```

* Import the image as shown below. You will need to supply your docker username/password to access the public image ""

* Import the following container into your ACR as follows, please be sure to use the correct name of your container registry if you did not use "**snykazureregistry**". You will also need to supply your Dockerhub username and password

1. DOCKERHUB_USER
2. DOCKERHUB_PASSWORD

```shell
❯ az acr import --name snykazureregistry --source docker.io/pasapples/springbootemployee:multi-stage-add-layers --image springbootemployee:multi-stage-add-layers  --username DOCKERHUB_USER --password DOCKERHUB_PASSWORD
```

* If all went well the previous command should show the image in your container registry as shown below

![alt tag](https://i.ibb.co/tYRvcSQ/snyk-azure-workshop-3.png)

If you have any trouble this guide explains how this command works

https://docs.microsoft.com/en-us/azure/container-registry/container-registry-import-images?tabs=azure-cli

## Step 3 Setup Azure Repos Integration

Snyk integrates with Microsoft Azure Repos to enable you to import your projects and monitor the source code for your repositories. Snyk tests the projects you’ve imported for any known security vulnerabilities found in the application’s dependencies, testing at a frequency you control.

* Login to http://app.snyk.io Sign up if you haven't already.
* Navigating to Integrations -> Source Control -> Azure Repos
* Enter in your Azure DevOps organization name as shown below and click Next

![alt tag](https://i.ibb.co/PFD4NWT/snyk-azure-workshop-4.png)

* Create a PAT in Azure DevOps the instructions shown to the right are all that is required here, once done click Save

![alt tag](https://i.ibb.co/zZ7t4mh/snyk-azure-workshop-5.png)

* If all went well then it should show a green button to import our first repo

![alt tag](https://i.ibb.co/1Gn91RC/snyk-azure-workshop-6.png)

More information of how to setup and use this integration can be found here

https://docs.snyk.io/features/integrations/git-repository-scm-integrations/azure-repos-integration

* Go ahead and click on "**Add your Azure Repos repositories to Snyk**"
* Search for "**dotNET goof v2**" and click on "Add selected repositories" top right hand corner of the page

_Note: This may take a few minutes to scan but once done you should some something as follows, ignore the warnings_

![alt tag](https://i.ibb.co/2jRz2FR/snyk-azure-workshop-7.png)

* Click on the link for "**dotNETGoofV2.Website.csproj**" so we can view the open source dependencies vulnerabilities that exist here

![alt tag](https://i.ibb.co/6Drf31b/snyk-azure-workshop-8.png)

For each Vulnerability, Snyk displays the following ordered by our [Proprietary Priority Score](https://snyk.io/blog/snyk-priority-score/) :
1. The module that introduced it and, in the case of transitive dependencies, its direct dependency,
2. Details on the path and proposed Remediation, as well as the specific vulnerable functions
3. Overview
4. Exploit maturity
5. Links to CWE, CVE and CVSS Score
6. Plus more ...

Not only is Snyk able to clearly identify the vulnerable library but if it can be fixed we will provide the ability to create a Pull Request to actually fix the issue.

* Go ahead and click onm "**Fix this vulnerable**" to see how Snyk creates a PR directly on the Azure Repo repository we imported

Here is what a PR on Azure Repos would look like if you upgraded "**Halibut from 4.4.4 to 4.4.7**"

![alt tag](https://i.ibb.co/yg06NBL/snyk-azure-workshop-9.png)

## Step 4 Setup ACR Integration

TODO://

## Step 5 Using VS Code to Secure your code as you develop

TODO://

## Step 6 Using Snyk IaC to test ARM templates

TODO://

## Step 7 Using Snyk Kubernetes Integration on AKS

TODO://

Thanks for attending and completing this workshop

![alt tag](https://i.ibb.co/7tnp1B6/snyk-logo.png)

<hr />
Pas Apicella [pas at snyk.io] is an Solution Engineer at Snyk APJ
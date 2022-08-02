# Code to Cloud and Back to Code - Securing Azure

Available in the Azure Marketplace, Snyk helps developers build their Azure workloads securely

In this **hands-on** workshop we will achieve the following:

**Part 1: From Code...**

* [Lab 1: Scanning from Source Control](#Lab-1-Scanning-from-Source-Control)
* [Lab 2: Secure as you Code - IDE](#Lab-2-Secure-as-you-Code---IDE)
* [Lab 3: Secure as you Code - CLI](#Lab-3-Secure-as-you-Code---CLI)

**Part 2: ...to Cloud...**

* [Lab 4: Secure Containers](#Lab-4-Secure-Containers)
* [Lab 5: Secure IAC](#Lab-5-Secure-IAC)
* [Lab 6: Securing your Pipeline](#Lab-6-Securing-your-Pipeline)

**Part 3: ...and Back to Code.**

* [Lab 7: Detecting Issues in Production](Lab-7-Detecting-Issues-in-Production)

**Bonus Labs**

* [Lab 8: Scan Your ACR Registry](Lab-8-Scan-Your-ACR-Registry)
* [Lab 9: Monitor Kubernetes with Snyk](Lab-9-Monitor-Kubernetes-With-Snyk)

## Prerequisites (15 mins)

* Microsoft Azure Subscription - https://azure.microsoft.com/en-au/free/
* Azure CLI - https://docs.microsoft.com/en-us/cli/azure/install-azure-cli
* snyk CLI - https://support.snyk.io/hc/en-us/articles/360003812538-Install-the-Snyk-CLI
* git CLI - https://git-scm.com/downloads
* Registered account on Snyk App - http://app.snyk.io
* Docker Hub Account - http://hub.docker.com
* Docker Desktop running locally - https://docker.com/products/docker-desktop
* **Install NPM: !!ADD URL!!**

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

## Part 1: From Code...

## Lab 1: Scanning from Source Control

**Step 1 - Import a GitHub repository to Azure repos**

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
  },
  "importRequestId": 9,
  "parameters": {
    "deleteServiceEndpointAfterImportIsDone": null,
    "gitSource": {
      "overwrite": false,
      "url": "https://github.com/tess-snyk/juice-shop"
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
Once complete the following should exist in your Azure DevOps ORG 

![alt tag](https://i.ibb.co/j5RhxFG/AR-Success.png)

If you have any trouble this guide explains how this command works

https://docs.microsoft.com/en-us/cli/azure/repos/import?view=azure-cli-latest

**Step 2 Setup Azure Repos Integration**

Snyk integrates with Microsoft Azure Repos to enable you to import your projects and monitor the source code for your repositories. Snyk tests the projects you’ve imported for any known security vulnerabilities found in the application’s dependencies, testing at a frequency you control.

* Login to http://app.snyk.io Sign up if you haven't already.
* Navigating to Integrations -> Source Control -> Azure Repos
* Enter in your Azure DevOps organization name as shown below and click Next

![alt tag](https://i.ibb.co/PFD4NWT/snyk-azure-workshop-4.png)

* Create a PAT in Azure DevOps the instructions shown to the right are all that is required here, once done click Save

![alt tag](https://i.ibb.co/zZ7t4mh/snyk-azure-workshop-5.png)

* If all went well then it should show a green button to import our first repo

![alt tag](https://i.ibb.co/1Gn91RC/snyk-azure-workshop-6.png)

Navigate to Snyk Settings and Snyk Code settings. Enable Snyk Code if it is not enabled already.

![alt tag](https://i.ibb.co/qpfZWmS/Code-Settings.png)

Navigate back to Snyk Settings and Infrastructure as Code settings. Enable Infrastructure as Code scanning if it is not enabled already.

![alt tag](https://i.ibb.co/rcbggQc/Ia-C-Settings.png)

Navigate back to the Azure Repos Integration Settings page. Enable PR Checks for Snyk Open Source. Enable Manual Fix PRs.

![alt tag](https://i.ibb.co/w02TDTj/STOPTHEBLEED.png)

More information of how to setup and use this integration can be found here

https://docs.snyk.io/features/integrations/git-repository-scm-integrations/azure-repos-integration

**Step 3 Explore Issues in Open Source Vulnerabilities**

* Go ahead and click on "**Add your Azure Repos repositories to Snyk**"
* Search for "**snyk-azure-project**" and click on "Add selected repositories" on the top right hand corner of the page

_Note: This may take a few minutes to scan but once done you should see the following, ignore the warnings_

![alt tag](https://i.ibb.co/6JgQRC9/Juic-Shop.png)

Snyk gives you a picture of the security of your whole cloud native application from your repo. You will see a proprietary code analysis, a dockerfile analysis, IAC analysis (Kubernetes Yaml file) and open source manifest files.

* Click on the link for "**package.json**" at the bottom so we can view the open source dependencies vulnerabilities that exist here.

Move from the 'Issues' view to the 'Dependencies' view and select the dependency tree view.
![alt tag](https://i.ibb.co/bvtDf6c/Tree-View.png)

How many dependencies does the application use in total? How many dependencies are listed? Where are the rest?

Move back to the 'Issues' view.

![alt tag](https://i.ibb.co/5RJfMZx/Move-to-deps.png)

For each Vulnerability, Snyk displays the following ordered by our [Proprietary Priority Score](https://snyk.io/blog/snyk-priority-score/) :
1. The module that introduced it and, in the case of transitive dependencies, its direct dependency,
2. Details on the path and proposed Remediation, as well as the specific vulnerable functions
3. Overview
4. Exploit maturity
5. Links to CWE, CVE and CVSS Score
6. Plus more ...

**Step 4 Explore Proprietary Code Issues**

Navigate back to the Projets view and expand  **snyk-azure-project** project. Click on the **"Code analysis"** Snyk Code project.

On the top issue **Cross-site Scripting (XSS)** note that the issue type is select "Full Details".

![alt tag](https://i.ibb.co/9NhHkfT/Code-Issue.png)

Snyk Code performs Semantic Code Analysis on your proprietary code. This means that it reads the code with intelligence to make a high fidelity estimate of how the code will function. 

![alt tag](https://i.ibb.co/wwV1VGC/Data-Flow.png)

The Data Flow Analysis shows where potentially tainted data enters the application; 'Source'. The data flow is tracked at each step accross multiple files where the data is manipulated in the code. Each of these steps is a potential location to sanitise the data. The location where the data is executed is the 'Sink'. Scroll down on the left as shown in the image above to see the Sink.

If the data has not been sanitised by the time it reaches a Sink, your code may be exploited. The type of exploit possible defines the issue type raised. In this case, a **Cross Site Scripting (XSS) attack.**

![alt tag](https://i.ibb.co/THb8Mqb/Fix-analysis.png)

Click "Fix Analysis" to find out about how to avoid and fix Cross Site Scripting (XSS) vulnerabilities. Snyk will provide three example fixes from Open Source to point you in the right direction.

![alt tag](https://i.ibb.co/102spPv/Ignore-issue.png)

Imagine this issue was detected in an application back end and you want to come back to review it in 90 days after fixing some higher priority vulnerabilities. Click 'ignore' and ignore the issue for 90 days.

Close the issue and view the ignored issues using the filter on the left hand side of the project view.

![alt tag](https://i.ibb.co/KbHfQMV/Filter-ignore.png)

**Step 5 'Stop the Bleeding'**

The first thing many organisations do when they start to use Snyk is monitor to capture a baseline of existing issues. The next step is to stop new issues from being introduced.

To complete this step, you will need to install git if you have not already done so. https://git-scm.com/book/en/v2/Getting-Started-Installing-Git

You will need to have npm available to complete this step. ***!!! Add NPM URL !!!**

Once you have Git installed locally, clone the repository that you set up in Step 1. You can follow the steps under "Get the clone URL of an Azure Repos Git repo": https://docs.microsoft.com/en-us/azure/devops/repos/git/clone?view=azure-devops&tabs=visual-studio-2019

Lets add an open source library that we know is vulnerable in order to see how we can be alerted about the new issue being introduced right out of Azure Repos.

* Create a new branch in your local git folder

> git branch add_reports

* Ensure you are actively working on that branch code base

> git checkout add_reports

* Verify the branch was properly switched to add_reports

> git status

Edit package.json with your favorite editor (vi).

*Note: this is only a snippet from the bottom of the file. Look for this snippet under the "Dependencies" section of the code*

Before:

```"swagger-ui-express": "^4.1.4",
    "unzipper": "0.9.15",
    "winston": "^3.3.3",
    "yaml-schema-validator": "^1.2.2",
    "z85": "^0.0.2"
  },
```
Add the tinymce package:

After:

```"swagger-ui-express": "^4.1.4",
    "unzipper": "0.9.15",
    "winston": "^3.3.3",
    "yaml-schema-validator": "^1.2.2",
    "z85": "^0.0.2",
    "tinymce": "4.1.0" 
  },
```
Run npm install to install tinymce dependency and rebuild the package-lock.json

**Step 6 Sec and Dev Collaboration**

Not only is Snyk able to clearly identify the vulnerable library but if it can be fixed we will provide the ability to create a Pull Request to actually fix the issue. This can allow security teams to collaborate with Developers to prioritise 'quick wins' within the tools Developers already use as part of their day to day work.

* Go ahead and click on "**Fix this vulnerable**" to see how Snyk creates a PR directly on the Azure Repo repository we imported

Here is what a PR on Azure Repos would look like if you upgraded "**Halibut from 4.4.4 to 4.4.7**"

![alt tag](https://i.ibb.co/yg06NBL/snyk-azure-workshop-9.png)

For some issues without an easy upgrade path e.g. where the dependency is not being maintained or it is a proprietary code issue, a ticket raised from the Snyk UI into a tool such as Jira is a better fit. **TD - DEMO**

## Lab 2: Secure as you Code - IDE

**Step 1 Using VS Code to Secure your code as you develop**

IDE integrations use Snyk Code’s fast analysis and response, allowing you to spot an issue, understand and learn more about it, and fix it, as you write the code before you check the code in. So you can find possible security flaws in your code as you write it, on a line-by-line basis. This helps to prevent seucrity technical debt from entering the value stream in the first place 

Snyk Code supports a VS Code plugin to support issue finding and fixing, directly from the IDE

* Clone or download a ZIP of your Azure Repo repository and open vscode from the directory "**dotNETGoofV2.Website**"
* Install the VS Code Snyk plugin using the link below

https://docs.snyk.io/features/integrations/ide-tools/visual-studio-code-extension-for-snyk-code

* Using the Snyk Icon on the left hand sidebar, it should auto run the test for you if not use the play buttons

![alt tag](https://i.ibb.co/S7KgFsy/snyk-azure-workshop-16.png)

## Lab 3: Secure as you Code - CLI

Before we get started please make sure you have setup the Snyk CLI. There are various install options as per the links below. Using the prebuilt binaries means you don't have to install NPM to install the Snyk CLI.

1. Install Page - https://support.snyk.io/hc/en-us/articles/360003812538-Install-the-Snyk-CLI
1. Prebuilt Binaries - https://github.com/snyk/snyk/releases

_Note: Make sure you have the following version installed or later_

**Command**

> snyk --version

```bash
❯ snyk --version
1.801.0
```

* Authorize the snyk CLI with your account as follows

**Command**

> snyk auth
 
```bash
$ snyk auth

Now redirecting you to our auth page, go ahead and log in,
and once the auth is complete, return to this prompt and you'll
be ready to start using snyk.

If you can't wait use this url:
https://snyk.io/login?token=ff75a099-4a9f-4b3d-b75c-bf9847672e9c&utm_medium=cli&utm_source=cli&utm_campaign=cli&os=darwin&docker=false

Your account has been authenticated. Snyk is now ready to be used.
```

_Note: If you are having trouble authenticating via a browser with the Snyk App you can setup authentication using the API token as shown below
[Authenticate using your API token](https://support.snyk.io/hc/en-us/articles/360004008258-Authenticate-the-CLI-with-your-account#UUID-4f46843c-174d-f448-cadf-893cfd7dd858_section-idm4557419555668831541902780562)_

**Step 1 Scanning for Open Source Dependencies in the CLI**

In your terminal, change directory local code repository that you cloned in Lab 1, Step 3.

First, scan the proprietary code in your local repository.

**Command**

> snyk code test

```bash
Snyk Test results
```

Now, run a test of the Open Source libraries.

**Command**

> snyk test

```bash
Snyk Test results
```

The results of the CLI scan can be sent to the Snyk UI to monitor.

**Command**

> snyk monitor

```bash
Snyk Monitor notification
```

Return to the Snyk UI in Project view. Filter results to show monitored CLI / CI results.

The Snyk CLI drives local scans and makes for powerful CI integrations with Snyk. You may choose to set thresholds to break a build or define local or shared ignores. See the CLI cheat sheet to get started: https://snyk.io/blog/snyk-cli-cheat-sheet/

We will continue to use the Snyk CLI in lab 4, lab 5 and lab 6.

## Part 2 ...to Cloud

## Lab 4: Secure Containers

_Note: You must have docker desktop running locally before you start these steps
Docker Desktop running locally - https://docker.com/products/docker-desktop_

BUILD LOCAL IMAGE INSTRUCTIONS

**Command**

> snyk container test IMAGE_NAME

```bash
Snyk Container Test results
```

The results of the CLI scan can be sent to the Snyk UI to monitor.

**Command**

> snyk container monitor IMAGE_NAME

```bash
Snyk Monitor notification
```

Return to the Snyk UI projets view. Filter results to show monitored CLI / CI results.

Click on "IMAGE NAME" to view the image vulnerabilities.

Notice how we are given multiple different base images we can upgrade to, so we can completely remove all vulnerabilities as shown below.

In the filters, select "User image layers". This will show the remaining vulnerabilities to be remediated that are being pulled in by addional user layers rather than the base image.


## Lab 5: Secure IAC

**Step 1 Scan an ARM template**

Snyk Infrastructure as Code for Azure Resource Manager (ARM) supports scanning json format.

Clone this repository as shown below. This repo has some arm templates we will scan shortly

**Command**

> git clone https://github.com/papicella/snyk-azure-workshop

```shell
❯ git clone https://github.com/papicella/snyk-azure-workshop
Cloning into 'snyk-azure-workshop'...
remote: Enumerating objects: 48, done.
remote: Counting objects: 100% (48/48), done.
remote: Compressing objects: 100% (22/22), done.
remote: Total 48 (delta 13), reused 46 (delta 11), pack-reused 0
Receiving objects: 100% (48/48), 15.60 KiB | 939.00 KiB/s, done.
Resolving deltas: 100% (13/13), done.
```

* Switch to the directory as follows "**snyk-azure-workshop/arm-templates**"

**Command**

> cd snyk-azure-workshop/arm-templates

```shell
❯ cd snyk-azure-workshop/arm-templates
```

* Test all ARM templates at the same time using a command as follows

**Command**

> snyk iac test *.json

```shell
❯ snyk iac test *.json

Testing lamp-app.json...


Infrastructure as code issues:
  ✗ Hardcoded admin password in VM configuration [High Severity] [SNYK-CC-TF-263] in Compute
    introduced by resources[5] > properties > osProfile > adminPassword

  ✗ Azure Network Security Rule allows public access [Medium Severity] [SNYK-CC-TF-35] in Network
    introduced by resources[2] > properties > securityRules[1] > properties > sourceAddressPrefix

  ✗ Linux VM scale set encryption at host disabled [Medium Severity] [SNYK-CC-AZURE-475] in Compute
    introduced by resources[5] > properties > securityProfile > encryptionAtHost

  ✗ Azure Network Security Group allows public access [Medium Severity] [SNYK-CC-TF-33] in Network
    introduced by resources[2] > properties > securityRules[1] > properties > sourceAddressPrefix

  ✗ Azure Network Security Rule allows public access [Medium Severity] [SNYK-CC-TF-35] in Network
    introduced by resources[2] > properties > securityRules[0] > properties > sourceAddressPrefix

  ✗ Azure Network Security Group allows public access [Medium Severity] [SNYK-CC-TF-33] in Network
    introduced by resources[2] > properties > securityRules[0] > properties > sourceAddressPrefix

  ✗ SAS token can be used over insecure HTTP [Medium Severity] [SNYK-CC-TF-244] in Storage
    introduced by resources[0] > properties > supportsHttpsTrafficOnly

  ✗ Virtual Network DDoS protection plan disabled [Low Severity] [SNYK-CC-AZURE-516] in Network
    introduced by resources[3] > properties > enableDdosProtection


Organization:      pas.apicella-41p
Type:              ARM
Target file:       lamp-app.json
Project name:      arm-templates
Open source:       no
Project path:      lamp-app.json

Tested lamp-app.json for known issues, found 8 issues

-------------------------------------------------------

Testing multi-tier-service-networking.json...


Infrastructure as code issues:
  ✗ Hardcoded admin password in VM configuration [High Severity] [SNYK-CC-TF-263] in Compute
    introduced by resources[12] > properties > osProfile > adminPassword

  ✗ Hardcoded admin password in VM configuration [High Severity] [SNYK-CC-TF-263] in Compute
    introduced by resources[14] > properties > osProfile > adminPassword

  ✗ WAF prevention mode not enabled [Medium Severity] [SNYK-CC-AZURE-611] in Network
    introduced by resources[7] > properties > webApplicationFirewallConfiguration > firewallMode

  ✗ Azure Network Security Group allows public access [Medium Severity] [SNYK-CC-TF-33] in Network
    introduced by resources[5] > properties > securityRules[1] > properties > sourceAddressPrefix

  ✗ Azure Network Security Group allows public access [Medium Severity] [SNYK-CC-TF-33] in Network
    introduced by resources[4] > properties > securityRules[1] > properties > sourceAddressPrefix

  ✗ Azure Network Security Group allows public access [Medium Severity] [SNYK-CC-TF-33] in Network
    introduced by resources[4] > properties > securityRules[0] > properties > sourceAddressPrefix

  ✗ WAF not enabled on application gateway [Medium Severity] [SNYK-CC-AZURE-474] in Network
    introduced by resources[7] > properties > webApplicationFirewallConfiguration > enabled

  ✗ SAS token can be used over insecure HTTP [Medium Severity] [SNYK-CC-TF-244] in Storage
    introduced by resources[0] > properties > supportsHttpsTrafficOnly

  ✗ Virtual Network DDoS protection plan disabled [Low Severity] [SNYK-CC-AZURE-516] in Network
    introduced by resources[3] > properties > enableDdosProtection


Organization:      pas.apicella-41p
Type:              ARM
Target file:       multi-tier-service-networking.json
Project name:      arm-templates
Open source:       no
Project path:      multi-tier-service-networking.json

Tested multi-tier-service-networking.json for known issues, found 9 issues

-------------------------------------------------------

Testing vm-32-data-disks-high-iops.json...


Infrastructure as code issues:
  ✗ Hardcoded admin password in VM configuration [High Severity] [SNYK-CC-TF-263] in Compute
    introduced by resources[4] > properties > osProfile > adminPassword

  ✗ Azure Network Security Rule allows public access [Medium Severity] [SNYK-CC-TF-35] in Network
    introduced by resources[1] > properties > securityRules[0] > properties > sourceAddressPrefix

  ✗ Azure Network Security Group allows public access [Medium Severity] [SNYK-CC-TF-33] in Network
    introduced by resources[1] > properties > securityRules[0] > properties > sourceAddressPrefix

  ✗ Virtual Network DDoS protection plan disabled [Low Severity] [SNYK-CC-AZURE-516] in Network
    introduced by resources[2] > properties > enableDdosProtection


Organization:      pas.apicella-41p
Type:              ARM
Target file:       vm-32-data-disks-high-iops.json
Project name:      arm-templates
Open source:       no
Project path:      vm-32-data-disks-high-iops.json

Tested vm-32-data-disks-high-iops.json for known issues, found 4 issues


Tested 3 projects, 3 contained issues.
```

To view all the ARM security rules use the following link

https://snyk.io/security-rules/tags/ARM

![alt tag](https://i.ibb.co/FBJCSbc/snyk-azure-workshop-17.png)

_Note: At the time of this workshop creation ARM template scanning in Snyk App was not available, but it will be at some point in the future_

<!---**Step 4 Convert a Bicep file and Scan the ARM template**

xx

**Step 5 Scan a Terraform File**

xxx

**Step 6 Scan a Terraform Plan File**

xx

**Step 7 Detect Drift**

xx

**Step 8 Review ARM and Terraform Rules for Azure**

--->

## Lab 6: Securing your Pipeline

**Step 1: Behind the Scenes**

The Snyk CLI drives CI integrations under the covers. The powerful CLI enables the flexibility to simply monitor or to break a build based on severity level or fixability. This flexibility enables a step by step on ramp to securing your SDLC while allowing developers to continue to work at pace.

> snyk test --severity-threshold=critical --fail-on-patchable

```bash
Snyk Test results
```

**Step 2: Further Flexibility with Policy - DEMO**

DEMO

**Step 3: Set up an Azure Pipeline**

Return to the Azure DevOps UI. From the project containing your NAME repo, navigate to Pipelines and create a New Pipeline.

Asked "Where is your code?", choose Azure Repos Git. Select the NAME repository.

-----

Create Yaml Pipeline




## Part 3: ...and Back to Code.

## Lab 7: Detecting Issues in Production

DEMO - Kubernetes Monitoring

## Bonus Labs

## Lab 8: Scan Your ACR Registry

**Step 1 Import a DockerHub repository container image to ACR**

In order to import a container image from DockerHub we will need a container registry on Azure. Using the following link create a container registry with a unique name I used "**snykazureregistry**" but you will need to use a different name here. If you already have a container registry you can simply use that and skip this but please make sure you use your registry name in the commands that follow.

https://docs.microsoft.com/en-au/azure/container-registry/container-registry-get-started-portal

![alt tag](https://i.ibb.co/FWhqznF/snyk-azure-workshop-2.png)

_Note: You must have docker desktop running locally before you start these steps
Docker Desktop running locally - https://docker.com/products/docker-desktop_

* Login to the container registry as shown below

**Command**

> az acr login --name snykazureregistry

```shell
❯ az acr login --name snykazureregistry
Login Succeeded
```

* Import the image as shown below. You will need to supply your docker username/password to access the public image ""

* Import the following container into your ACR as follows, please be sure to use the correct name of your container registry if you did not use "**snykazureregistry**". You will also need to supply your Dockerhub username and password

1. DOCKERHUB_USER
2. DOCKERHUB_PASSWORD

**Command**

> az acr import --name snykazureregistry --source docker.io/pasapples/springbootemployee:multi-stage-add-layers --image springbootemployee:multi-stage-add-layers  --username DOCKERHUB_USER --password DOCKERHUB_PASSWORD

```shell
❯ az acr import --name snykazureregistry --source docker.io/pasapples/springbootemployee:multi-stage-add-layers --image springbootemployee:multi-stage-add-layers  --username DOCKERHUB_USER --password DOCKERHUB_PASSWORD
```

* If all went well the previous command should show the image in your container registry as shown below

![alt tag](https://i.ibb.co/tYRvcSQ/snyk-azure-workshop-3.png)

If you have any trouble this guide explains how this command works

https://docs.microsoft.com/en-us/azure/container-registry/container-registry-import-images?tabs=azure-cli

## Step 2 Setup ACR Integration

Snyk integrates with Microsoft Azure Container Registry (ACR) to enable you to import your projects and monitor your containers for vulnerabilities, as is fully described in our Container vulnerability management documentation. Snyk tests the projects you’ve imported for any known security vulnerabilities found, testing at a frequency you control.

* Login to http://app.snyk.io Sign up if you haven't already.
* Navigating to Integrations -> Container Registries -> ACR
* Enter in your ACR connection details as shown below

![alt tag](https://i.ibb.co/TcCf2Y8/snyk-azure-workshop-12.png)

By enabling an admin user you can easily connect to the registry using a username/password. For alternative ways of connecting see the following link
https://docs.microsoft.com/en-us/azure/container-registry/container-registry-auth-service-principal

![alt tag](https://i.ibb.co/qDNPsMd/snyk-azure-workshop-11.png)

More Information

https://docs.snyk.io/products/snyk-container/image-scanning-library/acr-image-scanning/configure-integration-for-acr

* If successful you should see the following

![alt tag](https://i.ibb.co/4TPGkhh/snyk-azure-workshop-13.png)

* Click on "**Add your ACR images to Snyk**"
* Import "**springbootemployee**" image using the only tag available
* Once complete you should see the following. This may take a few minutes and ignore any warnings you may see

![alt tag](https://i.ibb.co/LzktTsM/snyk-azure-workshop-14.png)

* Click on "**multi-stage-add-layers**" to view the base image vulnerabilities
* Notice how we are given multiple different base images we can upgrade to, so we can completely remove all vulnerabilities as shown below 

![alt tag](https://i.ibb.co/C89xqrk/snyk-azure-workshop-15.png)

## Lab 9 Monitor Kubernetes with Snyk


## Conclusion

For this workshop we covered the following Azure integrations

1. Azure Repos
2. Azure Container Registry (ACR)
3. Snyk IDE plugin for VS Code
4. Snyk CLI scan for ARM misconfiguration

Having said that Snyk can do even more on Azure including the following which we did not cover, feel free to check out how we can use those integrations using the links below

1. Azure Pipelines Integration - https://docs.snyk.io/features/integrations/ci-cd-integrations/azure-pipelines-integration
2. Azure Kubernetes Integration - https://docs.snyk.io/products/snyk-container/image-scanning-library/kubernetes-workload-and-image-scanning/kubernetes-integration-overview 
3. Azure Functions - https://docs.snyk.io/features/integrations/serverless-integrations/azure-functions-overview

![alt tag](https://i.ibb.co/pwyfvVJ/snyk-azure-workshop-18.png)

Thanks for attending and completing this workshop

![alt tag](https://i.ibb.co/7tnp1B6/snyk-logo.png)

<hr />
By Pas Apicella [pas at snyk.io] and Tess Davis [tess at snyk.io] - Solution Engineers at Snyk APJ
# Code to Cloud and Back to Code - Securing Azure

Available in the Azure Marketplace, Snyk helps developers build their Azure workloads securely

In this **hands-on** workshop we will achieve the following:

* [Lab 1: Scanning from Source Control](#Lab-1-Scanning-from-Source-Control)
* [Lab 2: Secure as you Code - IDE](#Lab-2-Secure-as-you-Code---IDE)
* [Lab 3: Secure as you Code - CLI](#Lab-3-Secure-as-you-Code---CLI)
* [Lab 4: Secure IAC](#Lab-4-Secure-IAC)
* [Lab 5: Securing your Pipeline](#Lab-5-Securing-your-Pipeline)
* [Lab 6: Secure Containers from Azure Container Registry (ACR)](#Lab-6-Secure-Containers-from-Azure-Container-Registry-(ACR))

## Prerequisites (15 mins)

* Microsoft Azure Subscription - https://azure.microsoft.com/en-au/free/
* Azure CLI - https://docs.microsoft.com/en-us/cli/azure/install-azure-cli
* snyk CLI - https://support.snyk.io/hc/en-us/articles/360003812538-Install-the-Snyk-CLI
* git CLI - https://git-scm.com/downloads
* Registered account on Snyk App - http://app.snyk.io
* Docker Hub Account - http://hub.docker.com
* Docker Desktop running locally - https://docker.com/products/docker-desktop
* Install NPM:https://docs.npmjs.com/downloading-and-installing-node-js-and-npm

# Workshop Steps
If you don't have the Azure CLI installed, install it now as follows. This may take 15-20 minutes in the background so continue with Lab 1 using the UI method if the CLI is not yet installed. You will use the CLI in later steps.

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

**Step 1 - Import a GitHub repository to Azure repos (5 mins)**

If you already have the Azure CLI installed and are logged in, you may use the CLI to create your new repository in Azure Repos. Instructions [here](https://github.com/tess-snyk/snyk-azure-workshop/blob/wellington_cns/additional_instructions/Azure_CLI.md).

Else, we will use the UI as follows:

**UI Method:**

![alt tag](https://i.ibb.co/jgy4ytt/snyk-azure-workshop-10.png)

**Import Parameters**

1. git-source-url = https://github.com/tess-snyk/juice-shop
2. project = name of the empty azure repos project that you created above **snyk-azure-project**
3. org = Your Azure DevOps organization URL https://dev.azure.com/**<YOUR_ORG_NAME_HERE>**
4. repository = Use the same name as project above

**Import as follows**

Within your project, under Repos, choose to Import code:
![alt tag](https://i.ibb.co/3WqTBQz/Import.png)

Import the https://github.com/tess-snyk/juice-shop repository from github:
![alt tag](https://i.ibb.co/PMZ8Wwr/Name-Import.png)

Confirm import success; once complete the following should exist in your Azure DevOps ORG 

![alt tag](https://i.ibb.co/j5RhxFG/AR-Success.png)

---

**Step 2 Setup Azure Repos Integration (15 mins)**

Snyk integrates with Microsoft Azure Repos to enable you to import your projects and monitor the source code for your repositories. Snyk tests the projects you’ve imported for any known security vulnerabilities found in the application’s dependencies, testing at a frequency you control.

* Login to http://app.snyk.io Sign up if you haven't already.
* Navigating to Integrations -> Source Control -> Azure Repos
* Enter in your Azure DevOps organization name as shown below and click Next

![alt tag](https://i.ibb.co/PFD4NWT/snyk-azure-workshop-4.png)

* Create a PAT in Azure DevOps the instructions shown to the right are all that is required here, do take note of the requirements for permissions of the PAT given on the right hand pane. Once done click Save

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

---

**Step 3 Explore Issues in Open Source Vulnerabilities (10 mins)**

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

**Step 4 Explore Proprietary Code Issues (10 mins)**

Navigate back to the Projets view and expand  **snyk-azure-project** project. Click on the **"Code analysis"** Snyk Code project.

On the top issue **Cross-site Scripting (XSS)** note that the issue type is select "Full Details".

![alt tag](https://i.ibb.co/9NhHkfT/Code-Issue.png)

Snyk Code performs Semantic Code Analysis on your proprietary code. This means that it reads the code with intelligence to make a high fidelity estimate of how the code will function. 

![alt tag](https://i.ibb.co/GcMm9CG/Data-Flow.png)

The Data Flow Analysis shows where potentially tainted data enters the application; 'Source'. The data flow is tracked at each step accross multiple files where the data is manipulated in the code. Each of these steps is a potential location to sanitise the data. The location where the data is executed is the 'Sink'. Scroll down on the left as shown in the image above to see the Sink.

If the data has not been sanitised by the time it reaches a Sink, your code may be exploited. The type of exploit possible defines the issue type raised. In this case, a **Cross Site Scripting (XSS) attack.**

![alt tag](https://i.ibb.co/THb8Mqb/Fix-analysis.png)

Click "Fix Analysis" to find out about how to avoid and fix Cross Site Scripting (XSS) vulnerabilities. Snyk will provide three example fixes from Open Source to point you in the right direction.

Imagine this issue was detected in an application back end and you want to come back to review it in 90 days after fixing some higher priority vulnerabilities. Click 'ignore' and ignore the issue for 90 days.

![alt tag](https://i.ibb.co/6tPM7tk/fix-analysis-2.png)

Close the issue and view the ignored issues using the filter on the left hand side of the project view.

![alt tag](https://i.ibb.co/KbHfQMV/Filter-ignore.png)

**OPTIONAL - Step 5 'Stop the Bleeding' (20 mins)**

The first thing many organisations do when they start to use Snyk is monitor to capture a baseline of existing issues. The next step is to stop new issues from being introduced.

You will need to have npm available to complete this step: https://docs.npmjs.com/downloading-and-installing-node-js-and-npm

To complete this step, you will need to install git if you have not already done so. https://git-scm.com/book/en/v2/Getting-Started-Installing-Git

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

> npm install

Check that both the manifest (package.json) an lock file (package-lock.json) are updated:

> git status

Add them to git with wildcard matching to track the changes you would like to save to your branch.

> git add package*

Save your changes to your branch locally.

> git commit -m "adding tinymce v4.1.0"

Push your local changes to your branch on Azure Repos

> git push origin add_reports

Go to Azure Repos and your **snyk-azure-project** repo. **!!!Next to "<>Code" you'll see "Pull requests", Click to see the PRs. Select the big green button "New pull request" and on the next screen use the drop downs to ensure proper settings:**

*	For the right drop down select your branch. (Note your compare will be “compare: add_reports”)

If that looks okay you can open a PR with some basic details and once it opens you should see the Snyk PR checks similar to what is seen below. Note you will NOT merge these, the goal of this PR is to show the fancy license/code/security checks!

**OPTIONAL - Step 6 Sec and Dev Collaboration**

Not only is Snyk able to clearly identify the vulnerable library but if it can be fixed we will provide the ability to create a Pull Request to actually fix the issue. This can allow security teams to collaborate with Developers to prioritise 'quick wins' within the tools Developers already use as part of their day to day work.

Navigate back to Projects view and back into the final package.json project.

On the first issue **sequelize - SQL Injection** go ahead and click on "**Fix this vulnerability**" to see how Snyk creates a PR directly on the Azure Repo repository we imported.

![alt tag](https://i.ibb.co/TrV12L5/PR-Fix-Vuln.png)

Here is what a PR on Azure Repos would look like if you upgraded "**sequelize from 5.22.5 to 6.19.1**"

![alt tag](https://i.ibb.co/c8kM2xB/PR-in-Azure-Dev-Ops.png)

For some issues without an easy upgrade path e.g. where the dependency is not being maintained or it is a proprietary code issue, a ticket raised from the Snyk UI into a tool such as Jira is a better fit.

---

## Lab 2: Secure as you Code - IDE

**Step 1 Using VS Code to Secure your code as you develop**

For this lab you will need your IDE of choice installed. The lab guide will use Visual Studio Code but you may use Visual Studio, a Jetbrains IDE or Eclipse as you prefer. Supported IDEs here: https://snyk.io/ide-plugins/

IDE integrations use Snyk’s fast analysis and response, allowing you to spot an issue, understand and learn more about it, and fix it, as you write the code before you check it in. So you can find possible security flaws in your code as you write it, on a line-by-line basis. This helps to prevent seucrity technical debt from entering the value stream and saves developer time re-working code once it is in a later stage.

* You will need a local copy of the code from your **snyk-azure-project** repo. You should have this available from Lab 1 Step 3. Otherwise, you can download a copy in a zip file as below:

![alt tag](https://i.ibb.co/BTQzfCs/ADO-Download-Repo.png)

* Install the VS Code Snyk plugin using the link below or search the Visual Studio Code marketplace within the IDE.

https://docs.snyk.io/features/integrations/ide-tools/visual-studio-code-extension-for-snyk-code

* Open your **snyk-azure-project** folder in Visual Studio Code and Open your package.json file to view the dependencies

* Open the Snyk Icon on the left hand sidebar, it should auto run the test for you if not use the play buttons

* Expand **"OPEN SOURCE SECURITY"** and click on "sqlite3@5.0.2 - Denial of Service (DoS). Can you find the dependency in the package.json file and follow the upgrade advice?

![alt tag](https://i.ibb.co/SRNb7P6/Open-Source-IDE.png)

* Expand the **"CODE SECURITY"** tab and then expand "likeProductReviews.js". Click on the second vulnerability "Unsanitized input from the HTTP request body...."

![alt tag](https://i.ibb.co/tL2ChqZ/Code-IDE.png)

* On the *"Snyk Code Vulnerability"* tab, click **[:13]**. This will take you to the file and the line of code where the potentially tainted data enters the application

* Click through the subsequent numbers to be taken to each line of code where the data is manipulated. These are missed opportunitites to sanitise the data before it flows into the "Sink" at line **[:25]** where it is executed as a NoSQL query.

* To learn more about SQL Injection vulnerabilities and ways to mitigate them, follow the link **Learn about this vulnerability** to be taken to Snyk Learn

![alt tag](https://i.ibb.co/RH2Q89Y/Snyk-Learn.png)

You will land on the Javascript tutorial about SQL injections because the issue was found in a javascript file. If you are programming in a different language you will be taken to a page relevant to that language.

* Explore the lessons available in Snyk Learn

**OPTIONAL - Step 2 Learning about Application Security Vulnerabilities and How to Mitigate them**

* If you have time, please follow the tutorial for SQL Injection in your language of choice, or you can complete this later at home

---

## Lab 3: Secure as you Code - CLI

**Step 1 Snyk CLI Set Up**

Before we get started please make sure you have setup the Snyk CLI. There are various install options as per the links below. Using the prebuilt binaries means you don't have to install NPM to install the Snyk CLI.

1. Install Page - https://support.snyk.io/hc/en-us/articles/360003812538-Install-the-Snyk-CLI
1. Prebuilt Binaries - https://github.com/snyk/snyk/releases

_Note: Make sure you have the following version installed or later_ 1.881.0

**Command**

> snyk --version

```bash
❯ snyk --version
1.881.0
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
https://snyk.io/login?token=**<YOUR_TOKEN>**

Your account has been authenticated. Snyk is now ready to be used.
```

_Note: If you are having trouble authenticating via a browser with the Snyk App you can setup authentication using the API token as shown below
[Authenticate using your API token](https://support.snyk.io/hc/en-us/articles/360004008258-Authenticate-the-CLI-with-your-account#UUID-4f46843c-174d-f448-cadf-893cfd7dd858_section-idm4557419555668831541902780562)_

**Step 2 Scanning for Open Source Dependencies in the CLI**

In your terminal, change directory to the local code repository that you cloned in Lab 1, Step 3, **snyk-azure-project**.

First, scan the proprietary code in your local repository.

**Command**

> snyk code test

```bash

Testing /demo_code/snyk-azure-project...

 ✗ [Low] Cleartext Transmission of Sensitive Information 
     Path: test/api/productReviewApiSpec.js, line 9 
     Info: http (used in require) is an insecure protocol and should not be used in new code.

 ✗ [Low] Cleartext Transmission of Sensitive Information 
     Path: test/e2eSubfolder.js, line 7 
     Info: http (used in require) is an insecure protocol and should not be used in new code.

 ✗ [Low] Use of Hardcoded Credentials 
     Path: test/api/chatBotSpec.js, line 71 
     Info: Do not hardcode credentials in code. Found hardcoded credential used in login.
....

```

Now, run a test of the Open Source libraries.

**Command**

> snyk test

```bash
Testing /demo_code/snyk-azure-project...

Tested 913 dependencies for known issues, found 54 issues, 122 vulnerable paths.


Issues to fix by upgrading:

  Upgrade express-jwt@0.1.3 to express-jwt@6.0.0 to fix
  ✗ Regular Expression Denial of Service (ReDoS) [Low Severity][https://snyk.io/vuln/npm:moment:20170905] in moment@2.0.0
    introduced by express-jwt@0.1.3 > jsonwebtoken@0.1.0 > moment@2.0.0
  ✗ Regular Expression Denial of Service (ReDoS) [Medium Severity][https://snyk.io/vuln/npm:moment:20160126] in moment@2.0.0
    introduced by express-jwt@0.1.3 > jsonwebtoken@0.1.0 > moment@2.0.0
  ✗ Regular Expression Denial of Service (ReDoS) [Medium Severity][https://snyk.io/vuln/npm:moment:20161019] in moment@2.0.0
    introduced by express-jwt@0.1.3 > jsonwebtoken@0.1.0 > moment@2.0.0
  ✗ Authorization Bypass [High Severity][https://snyk.io/vuln/SNYK-JS-EXPRESSJWT-575022] in express-jwt@0.1.3
    introduced by express-jwt@0.1.3
  ✗ Uninitialized Memory Exposure [High Severity][https://snyk.io/vuln/npm:base64url:20180511] in base64url@0.0.6
    introduced by jsonwebtoken@0.4.0 > jws@0.2.6 > base64url@0.0.6 and 3 other path(s)
    .......
```

The results of the CLI scan can be sent to the Snyk UI to monitor.

**Command**

> snyk monitor

```bash
Monitoring /demo_code/snyk-azure-project (snyk-azure-project)...

Explore this snapshot at https://app.snyk.io/org/tess.davis/project/**YOUR_PROJECT_URL**

Tip: Detected multiple supported manifests (8), use --all-projects to scan all of them at once.

Notifications about newly disclosed issues related to these dependencies will be emailed to you.

```

* Return to the Snyk UI in Project view. Use the filters on the left to show monitored CLI / CI results. The step you just performed in the CLI to monitor created this project.

![alt tag](https://i.ibb.co/p2zdZtt/CLI-Monitor-UI.png)

The Snyk CLI drives local scans and makes for powerful CI integrations with Snyk. You may choose to use it just to monitor as we did in the last step or to set thresholds to break a build or define local or shared ignores. See the CLI cheat sheet to get started: https://snyk.io/blog/snyk-cli-cheat-sheet/

We will continue to use the Snyk CLI in lab 4, lab 5 and lab 6.

---

## Lab 4: Secure IAC

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

 ...

```

To view all the of the ARM security rules for Azure use the following link

https://snyk.io/security-rules/tags/ARM

![alt tag](https://i.ibb.co/FBJCSbc/snyk-azure-workshop-17.png)

_Note: At the time of this workshop creation ARM template scanning in Snyk App was not available, but it will be at some point in the future_

*BONUS LAB* for Terraform - follow the steps in the lab guide linked below to run a CLI scan on Terraform instead of ARM. Note the Terraform files in the below lab are for AWS rather than Azure configuration. If you have time you can also follow the steps in the guide to scan a plan file in the CLI, in pipline build and this is now also available in Terraform Cloud: https://github.com/tess-snyk/terraform-goof/tree/master

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

---

## Lab 5: Securing your Pipeline

**Step 1: Behind the Scenes**

The Snyk CLI drives CI integrations under the covers. The powerful CLI enables the flexibility to simply monitor or to break a build based on severity level or fixability. This flexibility enables a step by step on ramp to securing your SDLC while allowing developers to continue to work at pace.

> snyk test --severity-threshold=critical --fail-on-patchable

Additional examples of CLI commands for your CI Pipeline: https://docs.snyk.io/integrations/ci-cd-integrations#cli-examples-in-a-build-pipeline

**Step 2: Further Flexibility with Policy - DEMO**

Use Snyk Policies in combination with CI workflows to monitor or even block builds based on thresholds and other criteria that make sense for your team: https://snyk.io/blog/snyk-security-policies/#:~:text=A%20Snyk%20security%20policy%20contains,tweaking%20the%20severity%20for%20vulnerabilities.

Snyk Policies require a paid Snyk license so we will cover this capability in the workshop with a demo.

**DEMO**

**Step 3: Set up an Azure DevOps Pipeline for an Open Source Vulnerability Scan**

WARNING - may require a higher tier of Azure DevOps membership to run parallel jobs. If so, you will be able to complete 90% of the lab steps and will need to raise a ticket with Microsoft to allow free parallel jobs [here](https://forms.office.com/pages/responsepage.aspx?id=v4j5cvGGr0GRqy180BHbR63mUWPlq7NEsFZhkyH8jChUMlM3QzdDMFZOMkVBWU5BWFM3SDI2QlRBSC4u).

Snyk provides a plugin for Azure DevOps Pipelines to simplyfy adding security to your CI. In this step we will set up a simple open source scan using the native Snyk task.

Before getting started, please remove any existing pipelines that were cloned over from the github repo in step 1. You will need to go to your **snyk-azure-workshop** repository and find and delete any pipeline files e.g.

![alt tag](https://i.ibb.co/dKn0d88/Screen-Shot-2022-08-04-at-11-06-37-AM.png)

Please follow the steps [here](https://docs.snyk.io/integrations/ci-cd-integrations/azure-pipelines-integration#install-the-snyk-extension-for-your-azure-pipelines) to set up the [Snyk Extension for Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=Snyk.snyk-security-scan).

Once saving your Snyk Service connection you should see the below:

![alt tag](https://i.ibb.co/JkppsBj/Service-connection-success.png)

Now navigate back to Pipelines and create a pipeline:

![alt tag](https://i.ibb.co/tKKfZhQ/Azure-Pipeline.png)
* Asked "Where is your code?", choose Azure Repos Git and select the **snyk-azure-project** repository.

![alt tag](https://i.ibb.co/ZMD3bSF/code-where.png)

* At the "Configure your pipeline" step, choose Node.js as the build template

![alt tag](https://i.ibb.co/rvn4Zmg/choose-node.png)

An empty node.js pipeline script is created. 

* Use the task side bar on the right to find the Snyk extension

![alt tag](https://i.ibb.co/vv0XHNV/Snyk-Extension.png)

* Ensure to select the token that you created

![alt tag](https://i.ibb.co/bH9Z3nk/extension-settings.png)

Explore and choose settings including:
  * Drop down to monitor Application (Open Source) or Container
  * Set threshold to break the build
  * Choose to send results to the Snyk UI with Monitor and set a specific project name for the SNyk UI

* Note Advanced Settings which allows additional CLI arguments. We won't add any additional arguments now, however you could add arguments to change the scan type e.g.

> --code

would change the scan type and report to proprietary code

> --iac

would change the scan type to IaC

* Ensure the Yaml script is on a new line and then click "Add"

![alt tag](https://i.ibb.co/xjTJ9Bp/Add-task.png)

* Note the new task you have added to the yaml script using the extension

![alt tag](https://i.ibb.co/fYgPNjd/New-task.png)

* To save time the build step from the pipeline as the scan only requires dependencies installed and not a full app build

**Remove**

```
  20  npm run build
  21  displayName: 'npm install and build'

```

Replace

```
  20  displayName: 'npm install'

```
* Change the name of the pipeline to something unique to your organisation and click "save and run"

![alt tag](https://i.ibb.co/g9n2xFq/name-and-save-and-run.png)

If you recieve the below error you will not be able to continue with running the pipeline until you follow the steps given in the error message and await a response from Microsoft.

![alt tag](https://i.ibb.co/RzKXqqY/Parallelism.png)

During the live workshop we will demonstrate the output of the pipeline for those who recieve this error.

**DEMO Pipeline Results**

If the pipeline runs, it should be blocked by Snyk due to the issues detected as shown below:

![alt tag](https://i.ibb.co/CH9Mgch/Failed-with-Issues.png)

You will be able to view an html report in context of your Pipeline on the "Reports" tab and since "Monitor" was ticked during pipeline set up you can also review the results in the Snyk UI.

![alt tag](https://i.ibb.co/KW26t7B/Embedded-html.png)


**Optional Step 4 Configure a Security Audit Pipeline**

WARNING - may require a higher tier of Azure DevOps membership to run parallel jobs. If so, you will be able to complete 90% of the lab steps and will need to raise a ticket with Microsoft to allow free parallel jobs [here](https://forms.office.com/pages/responsepage.aspx?id=v4j5cvGGr0GRqy180BHbR63mUWPlq7NEsFZhkyH8jChUMlM3QzdDMFZOMkVBWU5BWFM3SDI2QlRBSC4u).

First we will build a pipeline for the use case of auditing a build across all four scan types (Code, Open Source, Container, IAC). This will produce a human-readable report covering the end to end cloud native application in a single pipeline.

![alt tag](https://i.ibb.co/tKKfZhQ/Azure-Pipeline.png)

* Asked "Where is your code?", choose Azure Repos Git and select the **snyk-azure-project** repository.

![alt tag](https://i.ibb.co/Pw22M1G/Pipeline2.png)

* At the "Configure your pipeline" step, choose Node.js as the build template

![alt tag](https://i.ibb.co/YXfMG4t/Nodejs.png)

An empty node.js pipeline script is created. 

* Follow the below link to the Azure_Sec_Audit repository, select and copy all of the code from the audit_pipeline.yaml file: https://github.com/tess-snyk/Azure_Sec_Audit/blob/main/audit_pipeline.yamlSave the pipeline but don't run it yet.

Note line 25 of the yaml script:

```bash
      snyk auth $(SNYK_TOKEN)
    
```
* Next set up a security token for the pipeline. We will use this to connect to Snyk.

  1. In your Snyk UI, General settings copy out your Auth Token to the clipboard.

  ![alt tag](https://i.ibb.co/S7M93Pm/CLI-Token-2.png)

  1. Your Snyk security token must be [set as a secret variable in the pipeline](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/set-secret-variables?view=azure-devops&tabs=yaml%2Cbash#secret-variable-in-the-ui)

  * On your pipeline page, select Edit
  * Locate the Variables for this pipeline.

    ![alt tag](https://i.ibb.co/ZVzvGpM/Find-Variables.png)

  * Add a variable called SNYK_TOKEN with the value of the Snyk auth token you copied

  ![alt tag](https://i.ibb.co/XC1FmTh/Set-variable.png)

  Select the Secret lock icon to store the variable in an encrypted manner.
  Click OK and then Save.

  Save and the pipeline.

  2. You will need the Azure Pipelines html extention to generate a human readable report of each of the scan types; code, open source, containers and IAC. Install the extension now: https://marketplace.visualstudio.com/items?itemName=JakubRumpca.azure-pipelines-html-report

* Save and run the pipeline

If you recieve the below error you will not be able to continue with running the pipeline until you follow the steps given in the error message and await a response from Microsoft.

![alt tag](https://i.ibb.co/jHtb2Z3/Screen-Shot-2022-08-03-at-9-06-42-PM.png)

**DEMO Pipeline Results**

After the pipeline runs, the results of each of the four scan types can be viewed in the "HTML Viewer" tab as shown below:

![alt tag](https://i.ibb.co/McmLCBQ/Screen-Shot-2022-08-03-at-9-22-51-PM.png)

---

## Lab 6 Secure Containers from Azure Container Registry (ACR)

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

**Step 2 Setup ACR Integration**

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

---

## Conclusion

For this workshop we covered the following Azure integrations

1. Azure Repos
2. Snyk IDE plugin for VS Code
3. Snyk CLI scan for code and open source
4. Snyk CLI scan for ICA - ARM misconfigurations
5. Pipeline scanning in Azure Pipelines
6. Container scanning in ACR

Snyk can do even more on Azure including the following which we did not cover, feel free to check out how we can use those integrations using the links below

1. Azure Kubernetes Integration - https://docs.snyk.io/products/snyk-container/image-scanning-library/kubernetes-workload-and-image-scanning/kubernetes-integration-overview 
2. Azure Functions - https://docs.snyk.io/features/integrations/serverless-integrations/azure-functions-overview

![alt tag](https://i.ibb.co/pwyfvVJ/snyk-azure-workshop-18.png)

Thanks for attending and completing this workshop

![alt tag](https://i.ibb.co/7tnp1B6/snyk-logo.png)

<hr />
By Pas Apicella [pas at snyk.io] and Tess Davis [tess at snyk.io] - Solution Engineers at Snyk APJ

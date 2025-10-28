---

copyright:
  years: 2022, 2025
lastupdated: "2025-10-28"

keywords: tekton, pipeline, toolchain, CD, CI, CC, automate, automation, continuous delivery, continuous integration, continuous compliance, devsecops tutorial, devsecops, DevOps, shift-left, shift left, secure DevOps, IBM Cloud, infrastructure-as-code, terraform, schematics

subcollection: devsecops

content-type: tutorial
services: schematics, ContinuousDelivery
account-plan: paid
completion-time: 1h

---

{{site.data.keyword.attribute-definition-list}}

# Set up a CD toolchain for Infrastructure as Code
{: #devsecops-tutorial-iac-cd}
{: toc-content-type="tutorial"}
{: toc-services="schematics, ContinuousDelivery"}
{: toc-completion-time="1h"}

This tutorial is part 3 of a 3-part tutorial series where you learn {{site.data.keyword.cloud}} DevSecOps best practices by using a complete reference implementation that is available as a service and powered by {{site.data.keyword.contdelivery_full}}. In part 3 of this tutorial series, you use the toolchain template for continuous deployment (CD) with security and compliance-related best practices in DevSecOps.
{: shortdesc}

## Before you begin
{: #devsecops-tutorial-iac-cd-prereqs}

Before you begin part 3 of this tutorial series, ensure that you complete the following prerequisites:
1. Complete [Part 1: Set up prerequisites](/docs/devsecops?topic=devsecops-devsecops-tutorial-iac).
1. Complete [Part 2: Set up a CI toolchain for Infrastructure as Code (Terraform)](/docs/devsecops?topic=devsecops-devsecops-tutorial-iac-ci).
1. View the [Getting started with DevSecOps in IBM Cloud - Part 2](https://www.kaltura.com/p/1773841/sp/177384100/embedIframeJs/uiconf_id/27941801/partner_id/1773841?iframeembed=true&entry_id=1_ceb64gbr){: video output="iframe" data-script="none" id="mediacenterplayer" frameborder="0" width="560" height="315" allowfullscreen webkitallowfullscreen mozAllowFullScreen} video.

## CD - Deploy a secure app with DevSecOps practices
{: #devsecops-tutorial-iac-cd-tekton-pipeline}

The DevSecOps CD toolchain contains only one pipeline for continuous deployment. It implements the following best practices:

* Change Management automation to help developers, approvers, and auditors track deployments from the lens of compliance.
* Creates an evidence summary from the evidence that is collected in the CI pipeline.
* Creates a change request in {{site.data.keyword.gitrepos}} based change management repository and adds deployment evidence to it.
* Uses the inventory repository to promote built artifacts to deployment environments like staging and prod.
* Checks the CR, and auto approves if all checks pass.
* If CR is approved, or emergency, the toolchain deploys the image from the inventory to production.

## Guided setup overview for the CD toolchain
{: #tutorial-iac-cd-toolchain-guided-setup}

Any of the methods in this tutorial takes you to the guided setup experience. You are guided through the toolchain setup process, and you are presented with the recommended configuration options that are needed to create your toolchain.

A progress indicator shows the steps to complete the configuration. You can use the progress indicator to navigate to a previous step. The configuration options for the current step are displayed in the main area of the page.

![DevSecOps Continuous Deployment toolchain welcome page](images/devsecops-cd-welcome.png){: caption="Figure 1. DevSecOps Continuous Deployment toolchain welcome page" caption-side="bottom"}

To advance to the next step, click **Continue**. You can advance to the next step only when the configuration for the current step is complete and valid. You can navigate to the previous step by clicking **Back**.

Some steps include a **Switch to advanced configuration** toggle. These steps by default present you with the minimum configuration needed. However, advanced users that need finer grained control can click the **Switch to advanced configuration** toggle to reveal all options for the underlying integration.

![DevSecOps Advanced configuration toggle](images/devsecops-advanced-options-toggle.png){: caption="Figure 2. DevSecOps Advanced configuration toggle" caption-side="bottom"}

When all the steps are successfully completed, you create the toolchain by clicking **Create** on the **Summary** step.

You can always go back to previous steps in the guided installer. The toolchain installer retains all the configuration settings from the successive steps.
{: tip}

## Start the CD toolchain setup
{: #devsecops-tutorial-iac-cd-create-options}
{: step}

Start the CD toolchain configuration by using one of the following options:

* Click **Create toolchain**.

   [![Create toolchain](images/create_toolchain_button.png "Create toolchain")](https://cloud.ibm.com/devops/setup/deploy?repository=https%3A%2F%2Fus-south.git.cloud.ibm.com%2Fopen-toolchain%2Fcompliance-cd-toolchain&env_id=ibm:yp:us-south){: external}

* From the {{site.data.keyword.cloud_notm}} console, click **Menu** ![Menu icon](../icons/icon_hamburger.svg) and select **DevOps**. On the **Toolchains** page, click **Create toolchain**. On the **Create a Toolchain** page, click **CD-Develop with DevSecOps practices**.

## Set up the CD toolchain name and region
{: #devsecops-tutorial-iac-cd-name-region}
{: step}

Review the default information for the toolchain settings. The toolchain's name identifies it in {{site.data.keyword.cloud_notm}}. Make sure that the toolchain's name is unique within your toolchains for the same region and resource group in {{site.data.keyword.cloud_notm}}.

Choose the associated CI toolchain in the dropdown to copy some of the CI configuration to aid in the setup of this CD toolchain.

![DevSecOps associated CI toolchain](images/devsecops-associated-ci-toolchain.png){: caption="Figure 3. DevSecOps associated CI toolchain" caption-side="bottom"}

**Warning**: This action might overwrite some of the values that you already entered.

## Set up CD tool integrations
{: #devsecops-tutorial-iac-cd-integrations}
{: step}

If you already linked to an existing CI toolchain in the **Welcome** step, the names of application-related repositories that are used in that toolchain are prefilled. Review the repository URLs, and then continue to the next steps.
{: tip}

### Application-related repositories
{: #devsecops-tutorial-iac-cd-application}

If you want to set up your CD toolchain from scratch, configure these repositories during CI toolchain creation, and then use or link to them here.

To fetch the respective URL, go to the **CI Pipeline**, select the respective tool card. Right-click the **tool** card, and select **Copy link address**.

* **Inventory**: The inventory repository records details of artifacts that are built by the CI and CD pipelines. For example, `https://<region>.git.cloud.ibm.com/myorg/my-compliance-ci-inventory`
* **Issues**: The issues repository records issues that are found while the CI pipeline is running. For example, `https://<region>.git.cloud.ibm.com/myorg/my-compliance-ci-issues`.
* **Evidence**: All raw compliance evidence that belongs to the application is collected here. For example, `https://<region>.git.cloud.ibm.com/myorg/my-compliance-ci-evidence`.

The toolchain currently supports linking only to existing IBM-hosted {{site.data.keyword.gitrepos}} repositories.
{: note}

### Inventory
{: #devsecops-tutorial-iac-cd-inventory}

**Repository URL**: URL of the inventory repository that is configured in your CI toolchain as captured in the previous step.

### Issues
{: #devsecops-tutorial-iac-cd-issues}

**Repository URL**: URL of the issues repository that is configured in your CI toolchain as captured in the previous step.

### Pipeline Configuration
{: #devsecops-tutorial-iac-cd-pipeline-config}

The pipeline configuration repository contains YAML files and scripts that are needed for deployment, testing, and other custom tasks.

For this tutorial, the pipeline configuration repository is the same as the one defined for the DevSecOps CI for Infrastructure as Code (IaC). Select *Use existing deployment configuration repository* and select the repository that is used for the pipeline configuration of the CI Toolchain.

For more information about Git repos, see [Configuring your {{site.data.keyword.gitrepos}}](/docs/devsecops?topic=devsecops-cd-devsecops-config-github). For more information about customizable scripts, see [Custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize).

### Secrets
{: ##devsecops-tutorial-iac-cd-secrets}

Several tools in this toolchain, and possibly in your customizable scripts, require secrets to access privileged resources. An {{site.data.keyword.cloud_notm}} API key is an example of such a secret. Store these secrets securely in an IBM-recommended secrets management tool, such as [IBM Key Protect for IBM Cloud](https://www.ibm.com/products/key-protect), [{{site.data.keyword.secrets-manager_full}}](https://www.ibm.com/products/secrets-manager){: external}, or [HashiCorp Vault](https://developer.hashicorp.com/vault){: external}. The secrets management tool can be integrated into the toolchain so that you can easily reference the secrets in your Tekton pipeline.

This tutorial uses {{site.data.keyword.secrets-manager_full}} as the vault for secrets.

### Evidence Storage
{: #devsecops-tutorial-iac-cd-evidence-storage}

The evidence repository stores evidence for all the tasks that the pipeline runs. This evidence is collected by the pipeline during the pipeline run.

Select an existing evidence repository, preferably the evidence repository that was created when you configured the continuous integration (CI) toolchain.

It is a best practice to use a Cloud Object Storage bucket as an evidence locker to store all the evidence and artifacts that are collected during the pipeline run.

For more information about evidence storage, see [Evidence](/docs/devsecops?topic=devsecops-devsecops-evidence).

### Evidence
{: #devsecops-tutorial-iac-cd-evidence}

**Repository URL**: URL of the evidence locker repository that is configured in your CI Toolchain as captured in the previous step.

### Cloud {{site.data.keyword.cos_short}} bucket
{: #devsecops-tutorial-iac-cd-cos-bucket}

To use this feature, you must have a Cloud {{site.data.keyword.cos_short}} instance and a bucket. Click [Using Cloud {{site.data.keyword.cos_short}} buckets as an evidence locker](/docs/devsecops?topic=devsecops-cd-devsecops-cos-bucket-evidence) to create a Cloud {{site.data.keyword.cos_short}} instance. For more information on configuring a bucket that can act as a compliance evidence locker, see [Configuring Cloud {{site.data.keyword.cos_short}} for storing evidence](/docs/devsecops?topic=devsecops-cd-devsecops-cos-config).
{: note}

* Cloud {{site.data.keyword.cos_short}} instance, Bucket name, and Cloud {{site.data.keyword.cos_short}} endpoint fields are automatically populated.
* Enter the Service ID API key
   * Preferred: An existing key can be imported from a secrets vault by clicking the key icon.
   * An existing key can be copy and pasted.

The endpoint field is optional. Select or provide the endpoint during the setup of the toolchain or during the pipeline run.
{: note}

### Deployment target
{: #devsecops-tutorial-iac-cd-target}

For the DevSecOps for Terraform use case, choose the **Custom** deployment option.

### Custom target
{: #devsecops-tutorial-iac-cd-custom-target}

This option requires the {{site.data.keyword.cloud_notm}} API key that is used within deployment scripts of the pipeline.
The deployment scripts provided use the Schematics to upload the Terraform configuration artifact to the Schematics workspace and use Schematics CLI `plan` and `apply` commands to deliver the Terraform configuration on {{site.data.keyword.cloud_notm}}.

### IBM Cloud API Key
{: #devsecops-tutorial-iac-cd-api-key}

The API key interacts with the {{site.data.keyword.cloud_notm}} CLI tool in several tasks:

* Preferred: Import an existing key from a secrets vault by clicking the **key** icon.
* Copy and paste an existing key.
* Create a key by clicking the **New +**.

Save the newly generated API key to a secrets vault.
{: tip}

### Change request management
{: #devsecops-tutorial-iac-cd-change-request}

Use an {{site.data.keyword.cloud_notm}}-hosted {{site.data.keyword.gitrepos}} repository to manage change requests. For more information, see [Automating change management](/docs/devsecops?topic=devsecops-cd-devsecops-change-management).

### DevOps insights toolchain
{: #devsecops-tutorial-iac-cd-insights}

Link an existing DevOps insights instance from another toolchain to this pipeline so that all the build, deploy, and test records from the CI and CD toolchain pipelines are collected in the same place.

The CD toolchain can publish the deployment records to an existing DevOps insights instance. To enable this feature, provide the ID of the toolchain that contains the existing DevOps Insights instance by selecting it in the DevOps insights toolchain ID drop-down.

### Optional tools
{: #devsecops-tutorial-iac-cd-optional-tools}

#### Slack
{: #devsecops-tutorial-iac-cd-slack}

Configure the [Slack Tool](/docs/ContinuousDelivery?topic=ContinuousDelivery-slack) to receive notifications about your PR/CI Pipeline events:

* [Slack webhook](https://docs.slack.dev/messaging/sending-messages-using-incoming-webhooks/){: external}
* Preferred: An existing webhook can be imported from a secrets vault by clicking the **key** icon.
* Copy and paste an existing webhook.
* Slack channel: post a message to an existing Slack.
* Slack team name: the phrase before *.slack.com* in our team URL. For example, if your team URL is `https://team.slack.com`, the team name is *team*.
* Automated Slack Notifications: Customize your selection by choosing the events for which you want to receive notifications.

You can add the Slack Tool after toolchain creation.
{: note}

After you create your toolchain, toggle sending notifications with the `slack-notifications` environment property in your CD pipeline (0 = off, 1 = on)

#### Security and Compliance
{: #devsecops-tutorial-iac-cd-scc}

To integrate the toolchain with {{site.data.keyword.compliance_short}}, you must provide the following information:

* A name and the evidence locker repository name for the {{site.data.keyword.compliance_short}} data collector.
* The evidence namespace for the type of the toolchain, which is either **Continuous Deployment** or **Continuous Compliance**.

Use the {{site.data.keyword.cloud_notm}} Framework for Financial Services profile for DevSecOps toolchains.
{: tip}

Read more about the [Security and Compliance Center](https://cloud.ibm.com/security-compliance/overview){: external} and the [tool integration](/docs/devsecops?topic=devsecops-scc) configuration process.

## Create the CD toolchain
{: #devsecops-tutorial-iac-cd-summary}
{: step}

On the **Summary** page, click **Create**, and wait for the toolchain to be created.

Configure the individual toolchain integrations after the pipeline is created.
{: tip}

![DevSecOps CD Toolchain Created](images/devsecops-cd-terraform-toolchain-created.png){: caption="Figure 4. DevSecOps CD Toolchain Created" caption-side="bottom"}

## Explore the CD toolchain
{: #devsecops-tutorial-iac-cd-explore}
{: step}

Now that the CD Toolchain is created, click the **cd-pipeline** card to open and run the Promotion Pipeline.

### Run the Promotion Pipeline
{: #devsecops-tutorial-iac-cd-promotion-pipeline-run}

Make sure that the [CI Pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-ci-pipeline) ran successfully before you run the Promotion Pipeline.
{: note}

The Promotion Pipeline creates a pull request with the content of the inventory on the Inventory Source Environment (for example: master) branch targeting the Inventory Target Environment branch (for example: staging or prod).

* On the **cd-pipeline Dashboard**, click **Run pipeline**, and select the **Manual Promotion Trigger**. Click **Run** to trigger the pipeline.
* Wait for the Promotion Pipeline run to complete and check execution log.
* After the Promotion Pipeline finishes successfully, the `promote` Task log provides a link to the pull request in the Inventory Repository. The pull request name is of the format `promote <Inventory Source Environment> to <Inventory Target Environment>`.

1. Open the pull request in your browser with the link provided in the log. Complete the details in the following sections:

   * **Priority**: (mandatory) One of Critical, High, Moderate, Low, Planning.
   * **Change Request assignee**: (mandatory) email-id of the assignee.
   * **Additional Description**: Description of the changes in the application.
   * **Purpose**: Purpose of the changes that are made to the application.
   * **Explanation of Impact**: Impact of the change to the application behavior or environment.
   * **Backout Plan**: Steps to back out if there is a deployment failure.
1. Complete the fields in the **Pull Request** and click **save**.
1. Add the `EMERGENCY` label to your PR if any compliance checks in CI failed and you want to [continue with deployment](/docs/devsecops?topic=devsecops-cd-devsecops-approve-cr#cd-devsecops-emergency-label)
1. Merge the pull request from {{site.data.keyword.gitrepos}}.

The details of the pull request are used by the CD Pipeline to create a Change Request in Change Request Management repository during the CD pipeline run.

### Configure the CD Pipeline
{: #devsecops-tutorial-iac-cd-tc-config}

#### Terraform variables
{: #devsecops-tutorial-iac-terraform}

For this tutorial, specify the key protect instance name to be created on production environment.

To do so, select **Environment Properties** in the cd-pipeline and click **Add**.

![CD Pipeline configuration](images/devsecops-cd-terraform-toolchain-environment-properties.png){: caption="Figure 5. CD pipeline configuration" caption-side="bottom"}

Add a property of type `Text Value` and name `TF_VAR_key_protect_instance`.
Set a meaningful value that indicates the delivery to production environment such as `key-protect-production-202210191453`.
This environment property's value is injected as the value for `key_protect_instance` terraform configuration variable.

You must also configure **IBM Cloud provider for Terraform** with the appropriate `ibmcloud_api_key` that authenticates with the {{site.data.keyword.cloud_notm}} as described in [Configuring the IBM Cloud Provider plug-in](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-provider-reference#provider-parameter-ov){: external}

Add a property of type `Secure Value` and name `TF_VAR_ibmcloud_api_key`.
Set the value to the api key to use for {{site.data.keyword.cloud_notm}} resources creation.

The sample used in this tutorial expects a `resource_group` to create the KeyProtect instance. The default `resource_group` name is `Default`. If this is not appropriate for your account, replace the default `resource_group` variable in the `variables.tf` file or specify the `resource_group` variable value to be used by setting the `TF_VAR_resource_group` environment property to the appropriate resource group name.
{: note}

#### Pipeline Configuration branch
{: #devsecops-tutorial-iac-cd-pipe-cfg}

The branch of the pipeline configuration repository is defined as `master` in the CD Pipeline environment properties by default. Change the branch to `main` for this tutorial.

To do so, select **Environment Properties** in the cd-pipeline and click `edit` to change the value to `main`

![CD Pipeline configuration branch](images/devsecops-cd-terraform-toolchain-pipeline-config-branch.png){: caption="Figure 6. CD Pipeline configuration branch" caption-side="bottom"}

### Run the CD pipeline
{: #devsecops-tutorial-iac-cd-toolchain-pipe-run}

Start a CD pipeline in one of the following ways:

* Preferred: trigger the CD pipeline manually.
* Optional: automatically after every `Merge` action in the Inventory Repository. A {{site.data.keyword.gitrepos}} trigger is set up to trigger automatic CD Pipeline, but is disabled by default.

You can also trigger the CD pipeline manually anytime, but if there are no changes since the last successful deployment, the CD pipeline aborts early because there is nothing to deploy. You can add and use [`force-redeploy` variable](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm#cd-parameters) to rerun the CD with no code changes.
{: tip}

A successful CD pipeline run looks like this:

![DevSecOps CD Pipeline Successful](images/devsecops-cd-explore-pipeline-success.png){: caption="Figure 7. DevSecOps CD Pipeline Successful" caption-side="bottom"}

After a successful CD pipeline run, you can find the KeyProtect instance service in the resource list.

![KeyProtect service instance created](images/devsecops-cd-terraform-toolchain-keyprotect-created.png){: caption="Figure 8. KeyProtect service instance created" caption-side="bottom"}

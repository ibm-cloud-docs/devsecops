---

copyright:
  years: 2021
lastupdated: "2021-09-15"

keywords: DevSecOps, CD, compliance, secure toolchain, IBM Cloud

subcollection: devsecops

---

{:shortdesc: .shortdesc}
{:table: .aria-labeledby="caption"}
{:external: target="_blank" .external}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:video: .video}
{:download: .download}
{:help: data-hd-content-type='help'}
{:support: data-reuse='support'}

# Setting up Tekton continuous delivery pipelines with compliance
{: #cd-devsecops-tekton-cd-compliance}

## Before you begin
{: #cd-devsecops-tekton-cd-prereq}

* [Create a Kubernetes cluster](/docs/containers?topic=containers-getting-started) on IBM Cloud Kubernetes Service to deploy your application.
* [Create toolchain secrets](/docs/devsecops?topic=devsecops-cd-devsecops-toolchains-secrets) to access different integrations and secure them.
* [Compliance CI toolchain](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-ci-toolchain){: external} configured with Devops Insight Integrations.
* Optional. [COS Bucket](/docs/devsecops?topic=devsecops-cd-devsecops-cos-config) as Compliance Evidence Locker to store pipeline run evidences.
* [Validate recommended IAM permissions](/docs/devsecops?topic=devsecops-cd-devsecops-iam-permissions) are assigned to corresponding integrations.

## Guided setup overview
{: #cd-devsecops-tekton-cd-guided}

Either of the two methods described in this tutorial takes you to the guided setup experience. This setup guides you through the toolchain setup process in a logical order while presenting the recommended configuration options that are required to create your toolchain. You can also view the following video tutorial that demonstrates the setup process:

![Getting Started with DevSecOps in IBM Cloud - Part 2 - Continuous Delivery Toolchain](https://video.ibm.com/embed/channel/23944579/video/Getting-Started-with-DevSecOps-2){: video output="iframe" data-script="none" id="watsonmediaplayer" width="560" height="315" scrolling="no" allowfullscreen webkitallowfullscreen mozAllowFullScreen frameborder="0" style="border: 0 none transparent;"}

A progress indicator is presented in the left margin showing the steps needed to complete the configuration and allowing navigation to a previous step with a mouse click. The configuration options for the current step are presented to the right of the progress indicator in the main area of the page.

| ![Guided setup](images/devsecops_set-up_cd_guided_experience.png) |
| :--: |

To advance to the next step click the **Continue** button at the bottom of the current step. You can only advance to the next step when the configuration for the current step is complete and valid. You can navigate to the previous step by clicking the **Back** button.

Some steps may have an **Advanced Options** toggle at the top of the page. These steps by default present you with the minimum recommended configuration needed. However, advanced users that need finer grained control can click the **Advanced Options** toggle to reveal all options for the underlying integration.

| ![Advanced options](images/devsecops_set-up_advancd_option.png) |
| :--: |

After you successfully complete all of the steps, click **Create** to create the toolchain.

You can return to previous steps in the guided installer. The toolchain installer retains all of the configurations that you completed in the successive stages.
{: tip}

## Start the CD toolchain setup
{: #cd-devsecops-tekton-cd-setup}

Start the CD toolchain configuration by using one of the following options:

* Click the following **Create toolchain** button.

   [![Create toolchain](images/create_toolchain_button.png "Create toolchain")](https://cloud.ibm.com/devops/setup/deploy?repository=https://us-south.git.cloud.ibm.com/open-toolchain/compliance-cd-toolchain&env_id=ibm:yp:us-south){: external}

* From the {{site.data.keyword.cloud_notm}} console, click the **Menu** icon ![Menu icon](../icons/icon_hamburger.svg) and select **DevOps**. On the Toolchains page, click **Create toolchain**. On the Create a Toolchain page, click **CD-Develop with DevSecOps practices**.

## Set up the toolchain name and region
{: #cd-devsecops-tekton-cd-name-region}

| ![Toolchain name](images/devsecops_set-up_cd_welcome_page.png) |
| :--: |

Review the default information for the toolchain settings. The toolchain's name identifies it in IBM Cloud. Make sure that the toolchain's name is unique within your toolchains for the same region and resource group in IBM Cloud.

The toolchain region can differ from the cluster and registry region.
{: tip}

## Set up the tool integrations
{: #cd-devsecops-tekton-cd-tool-integrations}

This section covers the setup of the various tool and service integrations that the CD toolchain uses. Please note, that this section may or may not follow the order in which the IBM Cloud installer wishes you to setup these tools.

If you have used [CI Pipeline Toolchain Template](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-ci-toolchain){: external} to set up your CI process, please refer to your CI toolchain and copy the names of below repositories used in that toolchain.

If you wish to set up your CI toolchain from scratch, configure these repositories during CI Toolchain creation and then use them here.

### Application Related Repositories
{: #cd-devsecops-app-repos}

- **Inventory:** Change management is tracked in this repository. Each successful CI Pipeline Run CD pipeline creates a new branch named as the created CR number, and merges it to master after deployment is concluded.
    E.g.: `https://<region>.git.cloud.ibm.com/myorg/my-compliance-ci-inventory`

- **Issues:** Issues about incidents that happen during the build and deployment process are stored here.
    E.g.: `https://<region>.git.cloud.ibm.com/myorg/my-compliance-ci-issues`

- **Evidence:** All raw compliance evidence that belongs to the application is collected here.
    E.g.: `https://<region>.git.cloud.ibm.com/myorg/my-compliance-ci-evidence`

- **Toolchain:** The tekton pipeline defintions (pipeline(s), triggers, listeners, etc.) are stored in this repo.
    E.g.: `https://<region>.git.cloud.ibm.com/myorg/my-compliance-ci-toolchain`

After you capture the names of repos from continous integration toolchain, proceed with the `Guided Setup` to begin continuous delivery toolchain creation. During the setup process, for each repository you can either provide the URL to an existing IBM hosted Git Repos and Issue Tracking (GRIT) repository created for your CI Toolchain or choose to create a new repository . Currently, the toolchain supports creating only GRIT repositories. Future releases will provide support to create repositories with GitHub, GitHub Enterprise (GHE) and other SCM Providers.

### Inventory
{: #cd-devsecops-inventory}

 The default behavior of the toolchain is to `Use existing inventory` to link an existing Inventory Repository for the toolchain. As noted earlier, the toolchain currently supports linking only to existing GRIT repositories.

 - **Repository URL**: URL of the Inventory Repository configured in your CI Toolchain as captured in step above.

| ![Inventory repository](images/devsecops_set-up_cd_inventory_repo.png) |
| :--: |

### Issues
{: #cd-devsecops-issues}

 The default behavior of the toolchain is to `Use existing issues repository` to link an existing Issues Repository for the toolchain. As noted earlier, the toolchain currently supports linking only to existing GRIT repositories.

 - **Repository URL**: URL of the Issues Repository configured in your CI Toolchain as captured in step above.

| ![Issues repository](images/devsecops_set-up_cd_issues_repo.png) |
| :--: |

### Pipeline Configuration
{: #cd-devsecops-pipeline-config}

The repository contains custom scripts to carry out pipeline tasks in the CD Pipeline (`.pipeline-config.yaml`). Refer to [hello-compliance-deployment](https://us-south.git.cloud.ibm.com/open-toolchain/hello-compliance-deployment) sample repository that contains some default configuration and scripts.

By default, the setup `Clone deployment configuration` from the sample repository. Once the repository is cloned, you can customize configurations and scripts for pipeline runs. 

- **New repository name**: Name of the IBM hosted GRIT Repository created by the toolchain as your deployment configuration repository. The region of the repository will remain the same as that of the toolchain. Please choose a unique name for the new repository.

If you have a deployment configuration repo from an exising continuous delivery toolchain, select the **Advanced Options** to configure the same settings for this pipeline.

| ![Pipeline Config Repository](images/devsecops_set-up_cd_Pipeline_config.png) |
| :--: |

### Secrets
{: #cd-devsecops-secrets}

Several tools in this toolchain require secrets to access privileged resources. An IBM Cloud API key is an example of such a secret. All secrets should be stored securely in a secrets vault and then referenced as required by the toolchain. The **Secrets** step allows you to specify which secret vault integrations will be added to your toolchain. Use the provided toggles to add or remove the vault integrations that you require. These can be configured in subsequent steps however you should familiarize yourself with the concepts in the [Protecting your sensitive data in Continuous Delivery](/docs/ContinuousDelivery?topic=ContinuousDelivery-cd_data_security#cd_secure_credentials) documentation as this provides important information about preconfiguring your vault providers and integrations appropriately.

| ![Choose secrets providers](images/devsecops_set-up_secrets_selection.png) |
| :--: |

### IBM Key Protect
{: #cd-devsecops-key-protect}

Use [Key Protect](https://cloud.ibm.com/catalog/services/key-protect){: external} to securely store and apply secrets like API keys, Image Signature or HashiCorp credentials that are part of your toolchain. It's recommended that you create a Key Protect Service Instance before proceeding further. Incase you have already created a Key Protect Service Instance as prerequisite, you can link the same in this step.

| ![Key Protect](images/devsecops_set-up_key_protect_mgr.png) |
| :--: |

- **Name**: Name of Key Protect instance created by the toolchain. This key protect instance can be accessed by this name during various stages of the toolchan setup.
- **Region**: Region in which the Key Protect service resides.
- **Resource Group**: Resource Group that the Key Protect service belongs.
- **Service name**: Key Protect service name.

To comply with best practices for using Hashicorp Vault, this template includes a Key Protect tool integration to securely manage the HashiCorp `Role ID` and `Secret ID`. By storing these HashiCorp secrets in Key Protect as a prerequisite for users to create toolchains, you protect access to HashiCorp Vault, which is the default secrets repo for most consumers.

### IBM Secrets Manager
{: #cd-devsecops-secrets-manager}

Use [Secrets Manager](https://cloud.ibm.com/catalog/services/secrets-manager){: external} to securely store and apply secrets like API keys, Image Signature or Hashicorp credentials that are part of your toolchain. It's recommended that you create a Secrets Manager Service Instance before proceeding further. Incase you have already created a Secrets Manager Service Instance as prerequisite, you can link the same in this step.

| ![Secrets Manager](images/devsecops_set-up_secrets_manager.png) |
| :--: |

- **Name**: Name of Secrets Manager instance created by the toolchain. This Secrets Manager instance can be accessed by this name during various stages of the toolchan setup.
- **Region**: Region in which the Secrets Manager service resides.
- **Resource Group**: Resource Group that the Secrets Manager service belongs.
- **Service name**: Secrets Manager service name.

### Hashicorp Vault
{: #cd-devsecops-vault}

Use HashiCorp Vault to securely store secrets that are needed by your toolchain. Examples of secrets are API keys, user passwords or any other tokens that enable access to sensitive information. Your toolchain stores references to the HashiCorp secrets, not the literal secret values, which enables advanced capabilities like secret rotation.

| ![HashiCorp Vault](images/devsecops_set-up_hashicorp.png) |
| :--: |

 - **Name**: A name for this tool integration. This name will be displayed in the toolchain.
 - **Server URL**: The server URL for your HashiCorp Vault Instance. (For example, `https://192.168.0.100:8200`)
 - **Integration URL**: The URL that you want to navigate to when you click the HashiCorp Vault Integration tile.
 - **Secrets Path**: The mount path where your secrets are stored in your HashiCorp Vault Instance.
 - **Authentication Method**: The Authentication method for your HashiCorp Vault Instance.
 - **Role ID:** Your team's [AppRole Role ID](https://www.hashicorp.com/blog/authenticating-applications-with-vault-approle){: external}.
 - **Secret ID:** Your team's [Secret ID](https://www.hashicorp.com/blog/authenticating-applications-with-vault-approle){: external}.

You can use the `AppRole` authentication method to read secret values.
{: tip}

### Evidence Storage
{: #cd-devsecops-evidence-storage}

All raw compliance evidence that belongs to the application is collected in this repository. This repository option should only be used for evaluation purpose. 

The default behavior of the toolchain is to `Use existing evidence locker`. The `Repository URL` field can be set to the Evidence Repository URL that was created/used for CI Toolchain. In case you with to create new Evidence Locker for the toolchain, you may choose `Create new evidence locker repository` that creates a new repository as IBM hosted GRIT Repository.

However, it is recommended to collect and store all the evidences in a COS bucket which can be configured as described below.

| ![Pipeline Config Repository](images/devsecops_set-up_evidence_storage.png) |
| :--: |

### Evidence
{: #cd-devsecops-evidence}

- **Repository URL**: URL of the Evidence Locker Repository configured in your CI Toolchain as captured in step above.

### COS bucket
{: #cd-devsecops-cos-bucket}

| ![COS bucket toggle](images/devsecops_set-up_cos-toggle.png) |
| :--: |

Cloud Object Storage is used to store the evidences and artifacts generated by the Compliance Pipelines. If you want to use this feature, you must have a Cloud Object Storage instance and a Bucket. For more information, follow the steps [here](/docs/devsecops?topic=devsecops-cd-devsecops-cos-config).

You can set any type of Cloud Object Storage bucket as a locker, even without a retention policy. The pipeline does not currently check or enforce settings.
{: tip}

For help, see the [Cloud Object Storage documentation](/docs/cloud-object-storage?topic=cloud-object-storage-getting-started-cloud-object-storage).

You need to provide the following information for the Pipelines to reach the aforementioned bucket:
- Cloud Object Storage endpoint
- Bucket name
- Service API key

You can set up the COS locker later, by providing the necessary `cos-bucket-name` and `cos-endpoint`.

To get the **Cloud Object Storage Endpoint**, please visit your COS Instance's page and select the _'Endpoints'_ section in the menu. You will need to copy the Public Endpoint matching the Bucket's _region_ and _resiliency_.

| ![COS Endpoint](images/cos-endpoint-menu.png) |
| :--: |

If you decide not to use Cloud Object Storage as an evidence locker, you can also set the required values after the creation of the toolchain by setting the `cos-bucket-name`, `cos-endpoint` environment variables in the CI Pipeline.

### Tekton pipeline
{: #cd-devsecops-tekton-pipeline}

The toolchain comes with an integrated Tekton pipeline to automate continuous build, test and deploy of the application to development cluster. This repository contains Tekton resources defined in YAML files that carry out the pipeline tasks. Tekton definitions can be changed also once the toolchain is created. The default behavior of the toolchain is to `Use existing repository`. The `Repository URL` field can be set to the Tekton Repository URL that was created/used for CI Toolchain.

- **Repository URL**: URL of the Tekton Pipeline Definition Repository configured in your CI Toolchain as captured in step above.

The Pipeline Repository contains configurations for both CI and CD Toolchains and hence can be kept common across the two toolchains. Incase you want to use seperate pipeline definitions for CD Toolchain Tekton definitions use the `Advanced Options` toggle button to clone the repository.

| ![Tekton Pipeline Repository](images/devsecops_set-up_tekton_pipeline.png) |
| :--: |

### Deploy
{: #cd-devsecops-deploy}

Configure the target Kubernetes cluster where the application will be deployed. Choose the target kubernetes cluster that you intend to use as the production or stage environment. The toolchain deploys the build application image to the cluster and runs the acceptance-test and other compliance checks.

- **IBM Cloud API Key:**
The API key is used to interact with the `ibmcloud` CLI tool in several tasks. In case you have already created a cluster, an API to access the cluster and stored the key in a secure vault (any of Key Protect, Secrets Manager or HashiCorp Vault), as prerequisite you can use the same in this step.

    - Option-1: An existing key can be imported from an existing Secret Provider intance created as prerequisites (Key Protect Instance, Secret Manager Instance or HashiCorp Vault) by clicking the key icon (Recommended)
    - Option-2: An existing key can be copy & pasted (Not Recommended)
    - Option-3: A new key can be created from here by clicking the `New +` button. Generate a new api-key if you don’t have one or copy an existing key to the field.The newly generated API key can be immediately saved to an existing Key Protect instance

Click on the `Key` Icon to use an existing key from your Secret Provider.

- **Provider**: The Secret Provider which stores your API Key to access the cluster, as linked to your toolchain earlier. It can be a Key Protect Instace, Secret Manager Instance or Hashicorp Vault Instance.
- **Resource Group**: Resource Group that the Secrets Manager Provider belongs.
- **Secret name**: Name/Alias of the secret i.e. API Key.

Once the API Key field is filled, the registry and cluster related fields will be filled automatically.

| ![Delivery Pipeline](images/devsecops_set-up_cd_deployment-target.png) |
| :--: |

### Inventory target and source branches
{: #cd-devsecops-inventory-branches}

- **Inventory Source Environment:** The environment from where you want to promote the application
Default: `master`

- **Inventory Target Environment:** The environment to where you want to deploy the application
Default: `prod`

- **Target Region:** The target region to where the application is deployed(Optional)

- **Emergency Label for change request PRs and issues:** Label on Change Request to be used for Emergency deployment

### Change Request
{: #cd-devsecops-change-request}

You can select IBM Cloud hosted GRIT (Git Issues and Issue Tracking) repository to manage Change Requests.

| ![Change Request](images/devsecops_set-up_cd_change_request.png) |
| :--: |

### Git Repo and Issue Tracking
{: #cd-devsecops-grit}

- **New repository name:** Name of the GRIT Repository to be used for Change Request Management.

The default behavior of the toolchain is to `Use default GRIT change request management repo` that creates a new Change Request Management Repository as IBM hosted GRIT Repository. Please choose a unique name for the new repository.

In case you have an existing Change Request repository from an exising CD Toolchain, use the `Advanced Options` toggle to configure the same for this pipeline.

| ![GRIT](images/devsecops_set-up_cd_change_request_mgmt.png) |
| :--: |

### Optional tools
{: #cd-devsecops-optional-tools}

#### Slack
{: #cd-devsecops-slack}

If you want to receive notifications about your CD Pipeline events, you can configure the Slack Tool during the setup from the toolchain template, or you can add the Slack Tool later.

In order for a Slack channel to receive notifications from your tools, you need a Slack webhook URL. To get a webhook URL, see the Incoming Webhooks section of the [Slack API website](https://api.slack.com/messaging/webhooks).

| ![Slack Tool](images/devsecops_set-up_slack.png) |
| :--: |

After creating your toolchain, you can toggle sending notifications with the `slack-notifications` Environment Property in your CD Pipeline (0 = off, 1 = on):

| ![Slack Tool Toggle](images/devsecops_set-up_slack-toggle.png) |
| :--: |

#### Common DevOps Insights Toolchain
{: #cd-devsecops-insights-toolchain}

DevOps Insights can optionally be included in the created toolchain and after each compliance check evidence is published into it. The toolchain can use an existing DevOps Insights instance, to publish the deployment records to insights. You can link DevOps Insights integration from CI toolchain by providing the Integration ID to consolidate all the deployment data in single DevOps Insight Instance.

| ![DOI Toolchain ID](images/devsecops_set-up_devops_insight.png) |
| :--: |

You can copy the Toolchain ID from the URL of your toolchain.
A toolchain's URL follows this pattern: `https://cloud.ibm.com/devops/toolchains/<toolchain-ID>?env_id=ibm:yp:us-south`

For example, if the URL is: `https://cloud.ibm.com/devops/toolchains/aaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee?env_id=ibm:yp:us-south`, then the toolchain's ID is: `aaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee`.

Make sure to only include the ID, not the full URL.
{: important}

You can also set a target environment for the DOI interactions, this parameter is optional. If you provide this parameter, it will be used instead of the target environment from the inventory.

#### DevOps Insights
{: #cd-devsecops-devops-insights}

Use this option if you wish to create a new instance of DevOps Insights to be used for the toolchain. There is no configuration required and toolchain will create a new instance of Devops Insight if this option is selected. The CD pipeline will automatically use the insights instance included in the toolchain.

### Security and Compliance
{: #cd-devsecops-sec}

To integrate the toolchain with the Security and Compliance Service, you need to provide a project name and the evidence locker repository name for the Security and Compliance data collector.

| ![Security and Compliance without Trigger](images/devsecops_set-up_cd_SCC.png) |
| :--: |

You can also configure the Security and Compliance integration to trigger a validation after a deployment:

| ![Security and Compliance with Trigger](images/devsecops_set-up_cd_SCC_enabled.png) |
| :--: |

Read more about the Security and Compliance Center [here](https://cloud.ibm.com/security-compliance/overview){: external}.

### Private Worker
{: #cd-devsecops-private-worker}

| ![Private Worker](images/devsecops_set-up_cd_private_worker.png) |
| :--: |

The Delivery Pipeline Private Worker tool integration connects with one or more private workers that are capable of running Delivery Pipeline workloads in isolation. For more information, see [Working with Private Workers](/docs/ContinuousDelivery?topic=ContinuousDelivery-private-workers).

## Create the CD toolchain
{: #cd-devsecops-tekton-cd-create}

- Click the create button at the bottom of the page, and wait for the toolchain to be created.

| ![Ready toolchain](images/devsecops_set-up_cd_Summary.png) |
| :--: |

| ![Explore toolchain](images/devsecops_cd_toolchain_created.png) |
| :--: |

You can configure the individual toolchain integrations after the pipeline is created.
{: tip}

## Run the CD pipeline
{: #cd-devsecops-tekton-cd-run-pipeline}

**Run Promotion Pipeline**

- Make sure CI Pipeline ran successfully before running the `Promotion Pipeline`.
- The Promotion Pipeline creates a `Pull Request` with the content of the inventory on the Inventory Source Environment (eg: `master`) branch targeting the Inventory Target Environment branch (eg: `staging` or `prod`). An intermediary branch for the PR is created which can be discarded after the PR has been merged. 

| ![Running the promotion pipeline](images/run-promotion-pipeline.png) |
| :--: |

- Once the `Promotion Pipeline` finishes successfully, the `promote` Task should provide you a link to the aforementioned `Pull Request` in the Inventory Repository. The pull request name is of the format `promote <Inventory Source Environment> to <Inventory Target Environment> `
- Open the `Pull Request` in your browser with the link provided in the log. Fill details in appropriate sections as below:
    - **Priority**: (mandatory) One of Critical, High, Moderate, Low, Planning
    - **Change Request assignee**: (mandatory) email-id of the assignee
    - **Additional Description**: Description about the changes in the application 
    - **Purpose**: Purpose of the changes made to the application
    - **Explanation of Impact**: Impact of the change to the application behavior or environment
    - **Backout Plan**: Steps to backout incase of deployment failure

- Complete the fields in the `Pull Request` and save. 
- Merge the `Pull Request` from the GRIT.

The details of the `Pull Request` will be used by the CD Pipeline to create a Change Request in Change Request Managment repository during the CD Pipeline Run.

**Run CD Pipeline**

There are two ways to start a CD pipeline:

1. Trigger the CD pipeline manually.
2. Automtically after every `Merge` action in Inventory Repository.

After the merge you have to manually trigger the run of `CD Pipeline`. There is a GRIT Trigger set up to trigger automatic CD Pipeline, but is disabled by default and can be enabled after the first promotion.

You can also trigger the `CD Pipeline` manually anytime, but if there are no changes since the last successful deployment, the CD pipeline does not deploy anything new.
{: tip}

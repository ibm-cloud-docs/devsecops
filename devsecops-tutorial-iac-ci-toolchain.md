---

copyright:
  years: 2023, 2025
lastupdated: "2025-04-14"

keywords: tekton, pipeline, toolchain, cd, ci, cc, automate, automation, continuous delivery, continuous integration, continuous compliance, devsecops tutorial, devsecops, devops, shift-left, shift left, secure devops, infrastructure-as-code, terraform, schematics

subcollection: devsecops

content-type: tutorial
services: schematics, ContinuousDelivery
account-plan: paid
completion-time: 1h

---

{{site.data.keyword.attribute-definition-list}}

# Setting up a CI toolchain using Terraform
{: #devsecops-tutorial-iac-ci}
{: toc-content-type="tutorial"}
{: toc-services="schematics, ContinuousDelivery"}
{: toc-completion-time="1h"}

With this tutorial, you can use the toolchain template for continuous integration (CI) with the {{site.data.keyword.compliance_short}} related practices in DevSecOps for Infrastructure as Code (IaC) Terraform. It is preconfigured for continuous deployment (CD) with inventory integration, change management with {{site.data.keyword.gitrepos}}, evidence collection, and deployment of the infrastructure to {{site.data.keyword.cloud_notm}}.
{: shortdesc}

## Before you begin
{: #devsecops-tutorial-iac-ci-prereqs}

Complete the following steps before you begin this tutorial.

1. Complete [Setting up the prerequisites](/docs/devsecops?topic=devsecops-devsecops-tutorial-iac). For more information, see the [importance of CI for IaC](/docs-draft/devsecops-working?topic=devsecops-working-practices-ci-toolchain-iac).
2. View the [Getting started with DevSecOps in the {{site.data.keyword.cloud_notm}} - Part 1](https://www.kaltura.com/p/1773841/sp/177384100/embedIframeJs/uiconf_id/27941801/partner_id/1773841?iframeembed=true&entry_id=1_8v84r6wl){: video output="iframe" data-script="none" id="mediacenterplayer" frameborder="0" width="560" height="315" allowfullscreen webkitallowfullscreen mozAllowFullScreen} video.

## Start the CI toolchain setup
{: #devsecops-tutorial-iac-ci-setup}
{: step}

The {{site.data.keyword.contdelivery_short}} service provides templates that guide you through the toolchain setup and create processes in a logical order. A progress indicator shows the steps to complete the configuration. Follow the steps to access the template for the CI toolchain.

1. In the {{site.data.keyword.cloud_notm}} console, click the **Menu** icon ![Menu icon](../icons/icon_hamburger.svg) > **Platform Automation** > **Toolchains**.
2. On the Toolchains page, click **Create toolchain**.
3. Check **Infrastructure as Code**.
4. Click **CI - Develop secure infrastructure as code with DevSecOps practices** tile.

## Set up the CI toolchain settings
{: #devsecops-tutorial-iac-ci-options}
{: step}

The **Welcome** page summarizes the purpose of the toolchain along with pointers to the documentation and related materials.

1. Click **Start**.
2. Enter a **Toolchain name** within your toolchain for the same region and resource group in the {{site.data.keyword.cloud_notm}}.
3. **Select a region** from the dropdown list.
4. **Select a resource group** from the dropdown list.
5. Click **Continue**.

   You can advance to the next step only when the configuration for the current step is complete and valid. You can always click **Back** to view previous steps in the guided installer. The toolchain installer retains all the configuration settings from the successive steps.
   {: important}

   Some steps include a **Switch to advanced configuration** toggle button. These steps by default present you with the minimum configuration. However, advanced users that need finer grained control can click the **Switch to advanced configuration** toggle to reveal the options for the underlying integration.
   {: important}

## Set up CI tool integrations
{: #devsecops-tutorial-iac-ci-tools}
{: step}

Review the default settings and provide the user-defined configurations wherever necessary to set up CI tool integration. Configure multiple repositories during the setup. You can clone the sample repositories or you can use your own, but the toolchain supports {{site.data.keyword.gitrepos}} repositories only.

### Infrastructure code
{: #tutorial-ci-terraform-infra}

The infrastructure code repository is the project or module that the CI toolchain and pipeline builds, tests, and delivers the target artifact. The infrastructure code repository is hosted on {{site.data.keyword.ghe_short}}. For consistency, this tutorial refers to {{site.data.keyword.gitrepos}}.

Review the default information for the infrastructure code:

1. Accept **Use default sample**. If you want to link an existing IaC repository for the toolchain, select **Bring your own infrastructure code**, and provide as input to the **Repository URL** field. The toolchain currently supports linking only to existing {{site.data.keyword.gitrepos}} repositories. For more information, see [Bringing your own infrastructure code to DevSecOps](https://github.ibm.com/one-pipeline/hello-iac-sample/blob/main/README.md){: external}.
2. Enter a name for **New repository name**.
3. Click **Continue**.

### Inventory
{: #devsecops-tutorial-iac-ci-inventory}

The inventory repository records the details of the artifacts that are created by the CI toolchains.

1. You can accept the default configuration provided in the template.
2. Click **Continue**.

### Issues
{: #devsecops-tutorial-iac-ci-issues}

The issues repository records issues that are found while the CI pipeline is running.

1. You can accept the default configuration provided in the template.
2. Click **Continue**

### Secrets
{: #devsecops-tutorial-iac-ci-secrets}

Specify the secret vault integrations to be added to your toolchain by using the toggles, as explained in [Managing {{site.data.keyword.cloud_notm}} secrets](/docs/secrets-manager?topic=secrets-manager-manage-secrets-ibm-cloud).

1. This tutorial uses {{site.data.keyword.secrets-manager_full}} as the vault for secrets. The **Region**, **Resource group**, and **Service name** fields are automatically populated based on available choices. Click the drop-down indicators to see the other choices.
2. Type your **{{site.data.keyword.secrets-manager_short}} instance name**.
3. Select the **Authorization type** from the drop down list.
4. Click **Continue**.

### Evidence Storage
{: #devsecops-tutorial-iac-ci-evidence}

The evidence repository stores all the evidence and artifacts generated by the DevSecOps CI pipeline.

1. Toggle the {{site.data.keyword.cos_full_notm}} bucket slider to store all the evidence in the {{site.data.keyword.cos_full_notm}} bucket that can be configured on the next page.
2. Accept the default settings.
3. Click **Continue**.

### Cloud {{site.data.keyword.cos_short}} bucket
{: #devsecops-tutorial-iac-ci-cos-bucket}

You must have the [{{site.data.keyword.cos_full_notm}} instance](/docs/devsecops?topic=devsecops-cd-devsecops-cos-bucket-evidence) and a [bucket](/docs/devsecops?topic=devsecops-cd-devsecops-cos-config) to act as a compliance evidence locker.
{: note}

1. The **Cloud {{site.data.keyword.cos_short}} instance**, **Bucket name**, and **Cloud {{site.data.keyword.cos_short}} endpoint** fields are automatically populated.
2. Enter your **Service ID API key**.
   * Preferred: An existing key can be imported from a secrets vault by clicking the key icon.
   * An existing key can be copied and pasted.
3. Click **Continue**.

The endpoint field is not mandatory. You can select or provide the endpoint during the setup of the toolchain or during the pipeline run.
{: note}

### Deploy
{: #devsecops-tutorial-iac-ci-deploy}

Configure the target {{site.data.keyword.bpshort}} workspace where the Terraform states are maintained. If the workspace name is not provided, then toolchain creates a default {{site.data.keyword.bpshort}} workspace with the `ToolchainName-ToolchainId-PipelineID`.

1. Type your **{{site.data.keyword.cloud_notm}} API Key**. If the API key has sufficient access. The {{site.data.keyword.bpshort}} workspace, **Git repositories**, and **Resource group** details are automatically populated. You can change any of these fields to match your configuration.
   * Preferred: An existing key can be imported from a secrets vault by clicking the key icon.
   * An existing key can be copied and pasted.
   * A new key can be created from here by clicking the **New +**.

   The newly generated API key can be immediately saved to a secrets vault.
   {: tip}
2. Enter the **Schematic Workspace Name**.
3. Click **Continue**.

### Signing
{: #devsecops-tutorial-iac-ci-signing}

The artifacts that are created by the toolchain and recorded in the inventory must be signed before deployed to production.

1. Enter the **GnuPG Private Key**. Alternatively you can create a new GPG key by clicking **NEW**. For more information, see [Generating a GPG key](https://cloud.ibm.com/docs/devsecops?topic=devsecops-devsecops-image-signing).
2. Click **Continue**.

### Optional tools
{: #devsecops-tutorial-iac-ci-opt}

#### Slack
{: #devsecops-tutorial-iac-ci-slack}

Configure the [Slack](/docs/ContinuousDelivery?topic=ContinuousDelivery-slack) to receive notifications about your pull requests, or CI pipeline events. You can also add the Slack tool after the toolchain creation.

1. Enter your **Slack webhook**. For more information, see [Slack webhook](https://api.slack.com/messaging/webhooks).
2. Enter your **Slack channel** to post message.
3. Enter the **Slack team name**. For example, if your team URL is `https://team.slack.com`, the team name is `team`.
4. Choosing **Automated Slack Notifications** for the events which you want to receive notifications.
5. Click **Continue**.

Optionally, you can toggle sending notifications with the `slack-notifications` environment property in your CD pipeline by using `0 = off, and 1 = on`.

## Create the CI toolchain
{: #devsecops-tutorial-iac-ci-summary}
{: step}

1. On the Summary page, click **Create toolchain**.
2. Wait for the toolchain creation.

   Other toolchain integrations can be configured after the pipeline is created.
   {: tip}

## Explore the CI toolchain
{: #devsecops-tutorial-iac-ci-explore}
{: step}

The CI toolchain contains `pr-pipeline` and `ci-pipeline`. These pipelines trigger when a new merge request is submitted or merged to the main branch in the infrastructure code repository.

Follow the steps to access your toolchain:

1. In the {{site.data.keyword.cloud_notm}} console, click the **Menu** icon ![Menu icon](../icons/icon_hamburger.svg) > **Platform Automation** > **Toolchains**.
2. On the Toolchains page, click **Create toolchain**.
3. Click your toolchain to view the output as shown in the screen capture.

   The sample that is used in this tutorial expects a `resource_group` to create the {{site.data.keyword.keymanagementserviceshort}} instance. The default `resource_group` name is `Default`. If the default is not appropriate for your account, replace the default `resource_group` variable in the `variables.tf` file. Else specify the `resource_group` variable by setting the `TF_VAR_resource_group` environment property in the PR pipeline and CI pipeline to the appropriate resource group name.
   {: note}

   ![DevSecOps IaC CI toolchain](images/devsecops-ci-terraform-explore-pipeline-created.png){: caption="DevSecOps CI toolchain" caption-side="bottom"}

### Run the pr pipeline
{: #devsecops-tutorial-iac-pr-pipeline}

To start the `pr-pipeline`, create a merge request in your application repository:

1. From the CI toolchain page, click the application repository tile. By default it gets created with a name `compliance-app-<timestamp>`.
2. Create a branch from main branch.
3. Update some code in the application or add a readme file and save changes.
4. Submit **Merge request**.
5. On the CI toolchain page, click the **pr-pipeline** tile. Verify that the PR pipeline is triggered by the creation of the merge request.
6. Wait for the `pr-pipeline` run to complete. The corresponding merge request that is in your application repository is in `Pending` state until all the stages of the PR pipeline finish successfully.
7. After the PR pipeline run is successful, click the pipeline to explore numerous steps that are completed and to view the page as shown in the screen capture.

   ![DevSecOps IaC PR pipeline successful](images/devsecops-ci-terraform-explore-pr-pipeline-success.png){: caption="DevSecOps PR pipeline successful" caption-side="bottom"}

8. Navigate back to the **Merge request**.
9. Merge the request so that your changes are copied to the main branch of your application repository. See that your CI pipeline is automatically triggered.

### Run the CI pipeline
{: #devsecops-tutorial-iac-ci-pipeline}

Start the CI pipeline in one of the following ways:
   * Automatically: after a successful PR pipeline, by approving and merging the PR to the main branch.
   * Manually: to trigger the CI pipeline manually, select the Delivery Pipeline card and click **Run Pipeline** and select **Manual Trigger**.

In this tutorial, the CI pipeline was triggered after you merged your code changes to the master branch of your application repository.

1. On the **CI toolchain** page, click the **ci-pipeline** tile.
2. Click **Run** against your pipeline name. Observe a pipeline run is running. Wait for the pipeline run to complete.
3. After the CI pipeline run is successful, click the pipeline to explore the completed steps to view the page as shown in the screen capture.

   ![DevSecOps IaC CI Pipeline Successful](images/devsecops-ci-terraform-explore-ci-pipeline-success.png){: caption="DevSecOps CI pipeline" caption-side="bottom"}

Evidence from all compliance checks in the CI pipeline is uploaded to the evidence locker repository, but also to the {{site.data.keyword.cos_full_notm}} bucket.

To evaluate if you have any failures in your pipeline run, check the final step of your pipeline.
{: note}

### Customizing the pipeline
{: #devsecops-tutorial-iac-ci-pipeline-cfg}

For more information about pipeline customization, see [How do a user customize the pipeline](/docs-draft/devsecops-working?topic=devsecops-working-faq_devsecops#faq-ci-sec-pipeline-customize)?

## Related content
{: #devsecops-tutorial-iac-ci-related}

You can continue to learn the following topics.

* [Getting started with toolchains](https://cloud.ibm.com/devops/getting-started){: external}
* [Getting started with {{site.data.keyword.contdelivery_short}}](/docs/ContinuousDelivery?topic=ContinuousDelivery-help-and-support)

## Next steps
{: #tutorial-iac-ci-next}

You successfully created a DevSecOps CI toolchain for Terraform, edited the code in the infrastructure code repository, ran `ci-pipeline` to build, test, and deploy your changes to the development environment.

Now, explore to [Setting up a CD toolchain for Terraform](/docs-draft/devsecops-working?topic=devsecops-working-tutorial-iac-cd).

---

copyright:
  years: 2021, 2025
lastupdated: "2025-09-03"

keywords: tekton, pipeline, toolchain, cd, ci, cc, automate, automation, continuous delivery, continuous integration, continuous compliance, devsecops tutorial, devsecops, devops, shift-left, shift left, secure toolchain, infrastructure-as-code, terraform, schematics, compliance

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Setting up Tekton continuous deployment pipelines with DevSecOps
{: #tutorial-tekton-cd-compliance}
{: toc-content-type="tutorial"}
{: toc-services="schematics, ContinuousDelivery"}
{: toc-completion-time="1h"}

With this tutorial, you can set up a continuous deployment (CD) pipeline and toolchain that adhere to compliance best practices.
{: shortdesc}

## Before you begin
{: #tutorial-tekton-cd-prereq}

* [Create a Kubernetes cluster](/docs/containers?topic=containers-getting-started) on {{site.data.keyword.containerlong}} to deploy your application.
* [Create toolchain secrets](/docs/devsecops?topic=devsecops-cd-devsecops-toolchains-secrets) to access different integrations and secure them.
* [Compliance continuous integration (CI) toolchain](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-ci-toolchain){: external} configured with {{site.data.keyword.DRA_full}} integrations.
* Optional. [Configure {{site.data.keyword.cos_full}}](/docs/devsecops?topic=devsecops-cd-devsecops-cos-config) as the compliance evidence locker to store pipeline run evidence.
* [Validate your {{site.data.keyword.iamlong}} (IAM) permissions](/docs/devsecops?topic=devsecops-iam-permissions) that are assigned to the corresponding integrations.
* View the following video to get an overview of the CD toolchain setup process.

![Getting started with DevSecOps in {{site.data.keyword.cloud_notm}} - CD toolchain](https://www.kaltura.com/p/1773841/sp/177384100/embedIframeJs/uiconf_id/27941801/partner_id/1773841?iframeembed=true&entry_id=1_ceb64gbr){: video output="iframe" data-script="none" id="mediacenterplayer" frameborder="0" width="560" height="315" allowfullscreen webkitallowfullscreen mozAllowFullScreen}

## Start the CD toolchain setup
{: #tutorial-tekton-cd-setup}
{: step}

The {{site.data.keyword.contdelivery_short}} service provides templates that guide you through the toolchain setup and create processes in a logical order. A progress indicator shows the steps to complete the configuration. Follow the steps to access the template for the CI toolchain.

1. In the {{site.data.keyword.cloud_notm}} console, click the **Menu** icon ![Menu icon](../icons/icon_hamburger.svg) > **Platform Automation** > **Toolchains**.
2. On the Toolchains page, click **Create toolchain**.
3. Click **CD-Develop with DevSecOps practices** tile.

## Set up the CI toolchain settings
{: #tutorial-tekton-cd-options}
{: step}

The **Welcome** page summarizes the purpose of the toolchain along with pointers to the documentation and related materials.

1. Click **Start**.
2. Enter a **Toolchain name** within your toolchain for the same region and resource group in the {{site.data.keyword.cloud_notm}}. Make sure that the toolchain's name is unique within your toolchains for the same region and resource group in {{site.data.keyword.cloud_notm}}.
3. **Select a region** from the dropdown list.

    The toolchain region can differ from the cluster and registry region.
    {: tip}

4. **Select a resource group** from the dropdown list.
5. Click **Continue**.

   You can advance to the next step only when the configuration for the current step is complete and valid. You can always click **Back** to view previous steps in the guided installer. The toolchain installer retains all the configuration settings from the successive steps.
   {: important}

   Some steps include a **Switch to advanced configuration** toggle button. These steps by default present you with the minimum configuration. However, advanced users that need finer grained control can click the **Switch to advanced configuration** toggle to reveal the options for the underlying integration.
   {: important}

## Set up the CD tool integrations
{: #tutorial-tekton-cd-tool-integrations}
{: step}

The CD tool integration covers the setup of the various tool and service integrations that the CD toolchain uses. If you use [CI pipeline toolchain template](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-ci-toolchain){: external} to set up your CI process, refer to your CI toolchain and copy the names of the repositories that are used in that toolchain.

### Application related repositories
{: #tutorial-tekton-cd-tool-app-repos}

If you want to set up your CI toolchain from scratch, configure these repositories during CI toolchain creation.

Inventory
:   Deployment information is tracked using this repository. For each successful CI pipeline run, the CD pipeline creates a new branch that is named as the created CR number, and merges to the master after deployment is concluded. For example, `https://<region>.git.cloud.ibm.com/myorg/my-compliance-ci-inventory`.

Issues
:   Stores the issues about incidents that happen during the build and deployment process. For example, `https://<region>.git.cloud.ibm.com/myorg/my-compliance-ci-issues`.

Evidence
:   Collects all the raw compliance evidence that belongs to the application. For example, `https://<region>.git.cloud.ibm.com/myorg/my-compliance-ci-evidence`.

Toolchain
:   The tekton pipeline definitions, such as, pipelines, triggers, and listeners are stored in this repository. For example, `https://<region>.git.cloud.ibm.com/myorg/my-compliance-ci-toolchain`.

After you capture the names of repositories from CI toolchain, proceed with the `Guided Setup` to begin CD toolchain creation. During the setup process, for each repository you can provide the URL to an existing {{site.data.keyword.IBM_notm}} hosted {{site.data.keyword.gitrepos}} repository. Currently, the toolchain supports creating only {{site.data.keyword.gitrepos}} repositories. Future releases provide support to create repositories with GitHub, GitHub Enterprise (GHE), and other SCM providers.

### Inventory
{: #tutorial-tekton-cd-tool-inventory}

The inventory repository records details of artifacts that are built by the CI toolchains. The default behavior of the toolchain is to **Use existing inventory** to link an existing inventory repository for the toolchain. The toolchain currently supports linking only to existing {{site.data.keyword.gitrepos}} repositories. For more information, see [Application related repositories](#tutorial-tekton-cd-tool-app-repos).

1. You can accept the default configuration that is provided in the template.
2. Click **Continue**.

### Issues
{: #tutorial-tekton-cd-tool-issues}

The issues repository records issues that are found while the CI pipeline is running.

1. You can accept the default configuration that is provided in the template.
2. Click **Continue**

### Pipeline configuration
{: #tutorial-tekton-tool-pipeline-config}

The pipeline configuration repository contains YAML files and scripts that are needed for deployment, testing, and other custom tasks. For more information about Git repos, see [Configuring your {{site.data.keyword.gitrepos}}](/docs/devsecops?topic=devsecops-cd-devsecops-config-github). For more information about customizable scripts, see [Custom scripts](/docs/devsecops-working?topic=devsecops-working-cd-devsecops-basics-pipelines-customization).

1. Accept the default settings for **Source Provider** and **Create a new deployment configuration repository**.

   The toolchain clones the [sample hello-compliance-deployment](https://us-south.git.cloud.ibm.com/open-toolchain/hello-compliance-deployment) in your Git organization. For more information about Git repos, see [Configuring your {{site.data.keyword.gitrepos}}](/docs/devsecops?topic=devsecops-cd-devsecops-config-github). For more information about customizable scripts, see [Custom scripts](/docs/devsecops-working?topic=devsecops-working-cd-devsecops-basics-pipelines-customization).
   {: note}

2. Enter a **New repository name**.
3. Click **Continue**.

### Secrets
{: #tutorial-tekton-tool-secrets}

Several tools in this toolchain, and possibly in your customizable scripts, require secrets to access privileged resources. An {{site.data.keyword.cloud_notm}} API key is an example of such a secret. Store these secrets securely in a secrets management tool, such as [{{site.data.keyword.keymanagementservicefull_notm}}](https://www.ibm.com/products/key-protect), [{{site.data.keyword.secrets-manager_full}}](https://www.ibm.com/products/secrets-manager){: external}, or [HashiCorp Vault](https://developer.hashicorp.com/vault){: external}. The [secrets management tool](/docs/secrets-manager?topic=secrets-manager-use-case-kubernetes-secrets) can be integrated into the toolchain so that you can easily reference the secrets in your Tekton pipeline.

You can use the **AppRole** authentication method to read secret values.
{: tip}

1. This tutorial uses {{site.data.keyword.secrets-manager_full}} as the vault for secrets. The **Region**, **Resource group**, and **Service name** fields are automatically populated based on available choices. Click the drop-down indicators to see the other choices.
2. Type your **{{site.data.keyword.secrets-manager_short}} instance name**.
3. Select the **Authorization type** from the dropdown list.
4. Click **Continue**.

### Evidence Storage
{: #tutorial-tekton-cd-evidence-storage}

The evidence repository stores all the evidence and artifacts that are generated by the DevSecOps CI pipeline.

1. Select a **Use existing evidence locker repository**.
2. Select the **Repository URL** that was created when you configured the continuous integration (CI) toolchain. For more information about evidence storage, see [Evidence](/docs/devsecops?topic=devsecops-devsecops-evidence).
3. Toggle the {{site.data.keyword.cos_full_notm}} bucket slider to store all the evidence in the {{site.data.keyword.cos_full_notm}} bucket.
4. Click **Continue**.

### Cloud {{site.data.keyword.cos_short}} Bucket
{: #tutorial-tekton-cd-cos-bucket}

You must have the [{{site.data.keyword.cos_full_notm}} instance](/docs/devsecops?topic=devsecops-cd-devsecops-cos-bucket-evidence) and a [bucket](/docs/devsecops?topic=devsecops-cd-devsecops-cos-config) to act as a compliance evidence locker.
{: note}

1. The **Cloud {{site.data.keyword.cos_short}} instance**, **Bucket name**, and **Cloud {{site.data.keyword.cos_short}} endpoint** fields are automatically populated.
2. Enter your **Service ID API key**.
   * Preferred: An existing key can be imported from a secrets vault by clicking the key icon.
   * An existing key can be copied and pasted.
3. Click **Continue**.

The endpoint field is optional. It is recommended to select or provide the endpoint during the setup of the toolchain or during the pipeline run.
{: note}

### Deploy
{: #tutorial-tekton-cd-deploy}

Configure the inventory target and Kubernetes cluster where the application is deployed:

1. The default **App name** is `hello-compliance-app`.
2. Type your **{{site.data.keyword.cloud_notm}} API Key**. The API key is used to interact with the {{site.data.keyword.cloud_notm}} CLI tool in several tasks.
   * Preferred: An existing key can be imported from a secrets vault by clicking the key icon.
   * An existing key can be copied and pasted.
   * A new key can be created from here by clicking the **New +**.

   The newly generated API key can be immediately saved to a secrets vault.
   {: tip}

3. If the API key is valid and has sufficient access, the **{{site.data.keyword.registryshort}}**, **{{site.data.keyword.registryshort}} namespace**, **Dev cluster region**, **Resource group**, **Cluster name**, **Cluster namespace** are automatically populated. You can change any of these fields to match your configuration.
4. Click **Continue**.

### IBM Cloud API Key
{: #tutorial-tekton-tool-api-key}

The API key is used to interact with the `ibmcloud` CLI tool in several tasks.

* An existing API key can be imported from an existing secret provider intance created as prerequisites (Recommended)
* An existing key can be copy and pasted (Not Recommended)
* A new key can be created by clicking **New +**. Generate a `api-key` if you don’t have one. The newly generated API key can be immediately saved to an existing Key Protect instance

When the API Key field is filled, the registry and cluster related fields are filled automatically.

### Inventory target and source branches
{: #tutorial-tekton-tool-inventory-branches}

1. Accept the **Single cluster (push based deployment)** to deploy your application to targets such as Virtual Server Instance or customize the deployment process, use the **Custom** option.
2. Click **Continue** to view the cluster page.
3. Enter the {{site.data.keyword.cloud_notm}} API Key field to interact with the CLI tool in several tasks. **Note** an existing key can be imported from a secrets vault by clicking the key icon.
4. Verify your **Cluster region**, **Resource group**, **Cluster name**, and
**Cluster namespace** fields where your target cluster is created.
5. Click **Continue**.

### Change Request Management
{: #tutorial-tekton-cd-change-request}

1. Select {{site.data.keyword.cloud_notm}} hosted **{{site.data.keyword.gitrepos}}** repository to manage the change requests. For more information, see [Automating change management](/docs/devsecops-working?topic=devsecops-working-cd-devsecops-change-management).
2. Click **Continue**.

### {{site.data.keyword.DRA_short}} toolchain
{: #tutorial-tekton-cd-insights}

Link an existing {{site.data.keyword.DRA_short}} instance from another toolchain to this pipeline, so that all the build, deploy, and test records from the CI and CD toolchain pipelines can be collected in the same place.

The CD toolchain can publish the deployment records to an existing {{site.data.keyword.DRA_short}} instance. To enable this feature, provide the ID of the toolchain that contains the existing {{site.data.keyword.DRA_short}} instance by selecting in the {{site.data.keyword.DRA_short}} toolchain ID list.

1. Provide your **{{site.data.keyword.DRA_short}} {{site.data.keyword.cloud_notm}} API Key**.
2. Accept the default configuration. 
3. Verify your **Region**, **Resource group**, **{{site.data.keyword.DRA_short}} toolchain ID**, and **{{site.data.keyword.DRA_short}} target environment** fields where your target environment is created.
4. Click **Continue**.

### Link to existing {{site.data.keyword.DRA_short}} toolchain
{: #tutorial-tekton-cd-link-toolchain}

{{site.data.keyword.DRA_short}} can optionally be included in the created toolchain and after each compliance check evidence is published. The toolchain can use an existing {{site.data.keyword.DRA_short}} instance to publish the deployment records to insights. You can link {{site.data.keyword.DRA_short}} integration from another toolchain by providing the integration ID.

1. Accept the **Current Toolchain**.
2. Click **Continue**.

You can copy the toolchain ID from the URL of your toolchain.
A toolchain's URL follows this pattern: `https://cloud.ibm.com/devops/toolchains/<toolchain-ID-comes-here>?env_id=ibm:yp:us-south`. If the URL is: `https://cloud.ibm.com/devops/toolchains/aaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee?env_id=ibm:yp:us-south` then the toolchain's ID is: `aaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee`. _Include the ID only, not the full URL._
{: note}

You can also set a target environment for the {{site.data.keyword.DRA_short}} interactions. This parameter is optional, which is used instead of the target environment from the inventory.

### Optional tools
{: #cd-devsecops-optional-tools}

#### Slack
{: #tutorial-tekton-cd-slack}

Configure the [Slack](/docs/ContinuousDelivery?topic=ContinuousDelivery-slack) to receive notifications about your pull requests, or CI pipeline events. You can also add the Slack tool after the toolchain creation.

1. Enter your **Slack webhook**. For more information, see [Slack webhook](https://api.slack.com/messaging/webhooks).
2. Enter your **Slack channel** to post message.
3. Enter the **Slack team name**. For example, if your team URL `https://team.slack.com`, the team name is `team`.
4. Choosing the events for which you want to receive notifications for **Automated Slack Notifications**.
5. Click **Continue**.

### Security and Compliance
{: #tutorial-tekton-cd-tool-sec}

To integrate the toolchain with the {{site.data.keyword.compliance_short}} service, you need to provide a name and the evidence locker repository name for the {{site.data.keyword.compliance_short}} data collector. For more information, see the [Security and Compliance Center](https://cloud.ibm.com/security-compliance/overview){: external} and the [tool integration](/docs/devsecops?topic=devsecops-scc) configuration process.

1. Enter a **Name** to verify the {{site.data.keyword.compliance_short}} posture of the toolchain.
2. Enter an **Evidence repository name or URL**. 
3. Accept **Evidence context** as `Continuous Deployment`.
4. Accept **Use profile with attachment** as `Disabled`. For more information, see [{{site.data.keyword.compliance_short}}](https://cloud.ibm.com/security-compliance/overview){: external} and the [tool integration](/docs/devsecops?topic=devsecops-scc) configuration process.
5. Click **Continue** to view the Summary page.

### Private worker
{: #tutorial-tekton-cd-tool-private-worker}

The delivery pipeline private worker tool integration connects with one or more private workers that can run delivery pipeline workloads in isolation. For more information, see [Working with private workers](/docs/ContinuousDelivery?topic=ContinuousDelivery-private-workers).

1. Enter a **Name** for the delivery pipeline private worker.
2. Enter your **Service ID API key**.
   * Preferred: An existing key can be imported from a secrets vault by clicking the key icon.
   * An existing key can be copied and pasted.
3. Click **Continue**.

## Create the CD toolchain
{: #tutorial-tekton-cd-create}
{: step}

1. On the Summary page, click **Create**.
2. Wait for the toolchain creation to view the summary page as shown in the screen capture. This can take a few minutes.

![DevSecOps CD toolchain created](images/devsecops-cd-toolchain-created.png){: caption="DevSecOps CD toolchain created" caption-side="bottom"}

You can configure the individual toolchain integrations after the pipeline is created.
{: tip}

## Explore the CD toolchain
{: #tutorial-tekton-cd-explore}
{: step}

### Run the promotion pipeline
{: ##tutorial-tekton-cd-promotion-pipeline}

Make sure that the [CI pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-ci-pipeline) ran successfully before you run the promotion pipeline.

1. Click **cd-pipeline**.
2. Click **Run** for **Manual Promotion Trigger** pipeline.
3. Click **Run** to trigger the pipeline.
4. Click **Manual Promotion Trigger** > `#1promotion-pipelinerun` pipeline. Wait for the promotion pipeline run to complete and check the execution log.

   The promotion pipeline creates a pull request with the content of the inventory on the inventory source environment such as `master` branch targeting the inventory target environment branch such as `staging` or `production`.
   {: note}

5. After the promotion pipeline finishes successfully, the `promote` task log provides a link to the pull request in the inventory repository. The pull request name is of the format `promote <inventory source environment> to <inventory target environment>`, for example, `promote master to prod`
6. Open the pull request in your browser with the link provided in the log. Complete the details in the following sections:
   * **Priority**: (mandatory) Set as `Critical`, `High`, `Moderate`, `Low`, or `Planning`.
   * **Change Request assignee**: (mandatory) `Email-ID` of the assignee.
   * **Additional Description**: Description about the changes in the application.
   * **Purpose/Goal**: Purpose of the changes that are made to the application.
   * **Explanation of Impact**: Impact of the change to the application behavior or environment.
   * **Backout Plan**: Steps to back out if there is a deployment failure.
7. Complete the fields in the **Pull Request** and click  **save**.
8. Add the `EMERGENCY` label to your pull request if any compliance checks in CI failed and you want to [continue with deployment](/docs/devsecops?topic=devsecops-cd-devsecops-approve-cr#cd-devsecops-emergency-label)
9. Merge the pull request from {{site.data.keyword.gitrepos}}.

The details of the pull request are used during the CD pipeline that is run to create and update in the change request repository.

## Run the CD pipeline
{: #tutorial-tekton-cd-run-pipeline}

### Run promotion pipeline
{: #cd-devsecops-tekton-cd-run-promotion-pipeline}

1. Make sure that CI pipeline ran successfully before you run the `Promotion Pipeline`.
2. The promotion pipeline creates a `Pull Request` with the content of the inventory on the inventory source environment. For example, `master` branch targeting the inventory target environment branch, for example, `staging` or `prod`. An intermediary branch for the PR is created which can be discarded after the PR is merged.

    ![Running the promotion pipeline](images/run-promotion-pipeline.png){: caption="Running the promotion pipeline" caption-side="bottom"}

3. After the `Promotion Pipeline` finishes successfully, the `promote` task provides you with a link to the previously mentioned `Pull Request` in the inventory repository. The pull request name is in the following format:

    ```text
    promote <Inventory Source Environment> to <Inventory Target Environment>
    ```
    {: codeblock}

4. Open the `Pull Request` in your browser with the link provided in the log. Complete the sections as follows:

    * **Priority**: (mandatory) One of Critical, High, Moderate, Low, Planning
    * **Change Request assignee**: (mandatory) Email-id of the assignee
    * **Additional Description**: Description about the changes in the application
    * **Purpose**: Purpose of the changes that are made to the application
    * **Explanation of Impact**: Impact of the change to the application behavior or environment
    * **Backout Plan**: Steps to backout if the deployment fails

5. Complete the fields in the `Pull Request` and save.
6. Merge the `Pull Request` from the {{site.data.keyword.gitrepos}}.

The details of the `Pull Request` are used by the CD pipeline to create a change request in the change request management repository during the CD pipeline run.

### Run the CD pipeline
{: #tutorial-tekton-cd-pipeline-run}

You can trigger a CD pipeline in manual or automatic way. You can trigger the CD pipeline manually anytime, but if no changes since the last successful deployment, the CD pipeline stops early as nothing to deploy. A {{site.data.keyword.gitrepos}} trigger is set up to trigger automatic CD pipeline, but is disabled by default and can be enabled after the first promotion.You can add and use [`force-redeploy` variable](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm#cd-parameters) to rerun the CD with no code changes. View the successful screen capture of the DevSecOps CD pipeline.

![DevSecOps CD pipeline manual promotion](images/devsecops-cd-explore-manual-pipeline-trigger.png){: caption="DevSecOps CD pipeline manual promotion" caption-side="bottom"}

![DevSecOps CD pipeline automatic promotion](images/devsecops-cd-explore-automatic-trigger.png){: caption="DevSecOps CD pipeline automatic promotion" caption-side="bottom"}

You can also trigger the `CD Pipeline` manually anytime, but if there are no changes since the last successful deployment, the `CD Pipeline` doesn't deploy anything new.
{: tip}

A successful CD pipeline run looks like this:

![DevSecOps CD pipeline successful](images/devsecops-cd-explore-pipeline-success.png){: caption="DevSecOps CD pipeline successful" caption-side="bottom"}

Flow of the tasks in the CD pipeline:

![DevSecOps CD tasks](images/devsecops-cd-tasks.png){: caption="DevSecOps CD tasks" caption-side="bottom"}

After a successful CD pipeline run, you can find the sample app that is running on the prod namespace.
The app URL can be found under `run stage` substep of `prod deployment` step of CD pipeline run.
Use that URL to check that the app is running.

![DevSecOps app running](images/devsecops-cd-explore-app-running-prod-namespace.png){: caption="DevSecOps app running" caption-side="bottom"}

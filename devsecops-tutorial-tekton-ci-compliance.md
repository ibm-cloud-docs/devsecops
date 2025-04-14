---

copyright:
  years: 2021, 2025
lastupdated: "2025-04-14"

keywords: tekton, pipeline, toolchain, cd, ci, cc, automate, automation, continuous delivery, continuous integration, continuous compliance, devsecops tutorial, devsecops, devops, shift-left, shift left, secure devops, infrastructure-as-code, terraform, schematics


subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Setting up Tekton Continuous integration pipelines with DevSecOps
{: #tutorial-tekton-ci-compliance}
{: toc-content-type="tutorial"}
{: toc-services="schematics, ContinuousDelivery"}
{: toc-completion-time="1h"}

With this tutorial, you can set up the Tekton continuous integration (CI) pipeline and create your toolchain with compliance.
{: shortdesc}

## Before you begin
{: #tutorial-tekton-ci-prereq}

* [Create a Kubernetes cluster](/docs/containers?topic=containers-getting-started) on the {{site.data.keyword.containerlong}} to deploy your application.
* [Install the {{site.data.keyword.cloud_notm}} CLI](/docs/containers?topic=containers-getting-started) on your operating system to interact with {{site.data.keyword.cloud_notm}} resources.
* [Create an artifact signing key](/docs/devsecops?topic=devsecops-devsecops-image-signing) with proper encoding to sign your application docker artifacts.
* [Create toolchain secrets](/docs/devsecops?topic=devsecops-cd-devsecops-toolchains-secrets) to access different integrations and secure.
* [Configure {{site.data.keyword.cos_full}}](/docs/devsecops?topic=devsecops-cd-devsecops-cos-config) as the compliance evidence locker to durably store pipeline run evidence.
* [Validate your {{site.data.keyword.iamlong}} (IAM) permissions](/docs/devsecops?topic=devsecops-iam-permissions) that are assigned to the corresponding integrations.
* [Set up the GaraSign artifact signing service](/docs/devsecops?topic=devsecops-devsecops-imagesigning) to sign artifacts.
* [Get a Service API key for TaaS private worker](/docs/devsecops?topic=devsecops-devsecops-tass-pw).
* View the following video: 

![Getting started with DevSecOps in the {{site.data.keyword.cloud_notm}}](https://www.kaltura.com/p/1773841/sp/177384100/embedIframeJs/uiconf_id/27941801/partner_id/1773841?iframeembed=true&entry_id=1_8v84r6wl){: video output="iframe" data-script="none" id="mediacenterplayer" frameborder="0" width="560" height="315" allowfullscreen webkitallowfullscreen mozAllowFullScreen}

   The CI pipeline uses the GaraSign code signing service to sign build artifacts that require registration and on-boarding. GaraSign uses the internal {{site.data.keyword.IBM_notm}} network and the TaaS team provide Tekton private workers that have access to the internal {{site.data.keyword.IBM_notm}} network. Registration and on-boarding are required to use the TaaS Tekton private workers.
   {: note}

## Start the CI toolchain setup
{: #tutorial-tekton-ci-setup}
{: step}

The {{site.data.keyword.contdelivery_short}} service provides templates that guide you through the toolchain setup and create processes in a logical order. A progress indicator shows the steps to complete the configuration. Follow the steps to access the template for the CI toolchain.

1. In the {{site.data.keyword.cloud_notm}} console, click the **Menu** icon ![Menu icon](../icons/icon_hamburger.svg) > **Platform Automation** > **Toolchains**.
2. On the Toolchains page, click **Create toolchain**.
3. Check **Infrastructure as Code**.
4. Click **CI - Develop secure infrastructure as code with DevSecOps practices** tile.

## Set up the CI toolchain settings
{: #tutorial-tekton-ci-options}
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
{: #devsecops-tekton-ci-tool-integrations}
{: step}

Review the default settings and provide the user-defined configurations wherever necessary to set up CI tool integration. Configure multiple repositories during the setup. You can clone the sample repositories or you can use your own, but the toolchain supports linking only to the existing {{site.data.keyword.gitrepos}} repositories.


### Application
{: #tutorial-ci-tekton-iac}

Review the default information for the toolchain settings:

1. You can accept the default configuration that is provided in the template.
2. Click **Continue**

### Inventory
{: #tutorial-tekton-ci-tool-inventory}

The inventory repository records details of artifacts that are built by the CI toolchains.

1. You can accept the default configuration that is provided in the template.
2. Click **Continue**.

### Issues
{: #tutorial-teckton-ci-issues}

The issues repository records issues that are found while the CI pipeline is running.

1. You can accept the default configuration that is provided in the template.
2. Click **Continue**

### Secrets
{: #tutorial-tekton-ci-secrets}

Several tools in this toolchain, and possibly in your customizable scripts, require secrets to access privileged resources. An {{site.data.keyword.cloud_notm}} API key is an example of such a secret. Store these secrets securely in a secrets management tool, such as [{{site.data.keyword.keymanagementservicefull_notm}}](https://www.ibm.com/products/key-protect), [{{site.data.keyword.secrets-manager_full}}](https://www.ibm.com/products/secrets-manager){: external}, or [HashiCorp Vault](https://www.vaultproject.io/){: external}. The [secrets management tool](/docs/secrets-manager?topic=secrets-manager-manage-secrets-ibm-cloud) can be integrated into the toolchain so that you can easily reference the secrets in your Tekton pipeline.

1. This tutorial uses {{site.data.keyword.secrets-manager_full}} as the vault for secrets. The **Region**, **Resource group**, and **Service name** fields are automatically populated based on available choices. Click the drop-down indicators to see the other choices.
2. Type your **{{site.data.keyword.secrets-manager_short}} instance name**.
3. Select the **Authorization type** from the dropdown list.
4. Click **Continue**.

### Evidence Storage
{: #tutorial-tekton-ci-evidence-storage}

The evidence repository stores all the evidence and artifacts that are generated by the DevSecOps CI pipeline.

1. Toggle the {{site.data.keyword.cos_full_notm}} bucket slider to store all the evidence in the {{site.data.keyword.cos_full_notm}} bucket that can be configured on the next page.
2. Accept the default settings.
3. Click **Continue**.

### Cloud {{site.data.keyword.cos_short}} Bucket
{: #tutorial-tekton-ci-cos-bucket}

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
{: #tutorial-tekton-ci-deploy}

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

### Artifact signing
{: #tutorial-tekton-ci-image-signing}

The artifacts are built by the toolchain and recorded in the inventory and must be signed before deployed to production. The pipeline uses [Skopeo](https://github.com/containers/skopeo){: external} as the default tool to provide artifact signing capability. You can use an existing GPG Key or create a new [GPG Key Pair](/docs/devsecops?topic=devsecops-devsecops-image-signing).

1. Enter the **GnuPG Private Key**. Alternatively you can create a new GPG key by clicking **NEW**. For more information, see [Generating a GPG key](https://cloud.ibm.com/docs/devsecops?topic=devsecops-devsecops-image-signing).
2. Click **Continue**.

### {{site.data.keyword.DRA_short}}
{: #tutorial-tekton-tool-integration-insights}

The [{{site.data.keyword.cloud_notm}} {{site.data.keyword.DRA_short}}](/docs/ContinuousDelivery?topic=ContinuousDelivery-di_working) is included in the toolchain. View your pipeline test results for every build, from every deployment and environment.

1. Accept the default configuration.
2. Click **Continue**.

### Optional tools
{: #tutorial-tekton-ci-option-tools}

#### Slack
{: #tutorial-tekton-ci-slack}

Configure the [Slack](/docs/ContinuousDelivery?topic=ContinuousDelivery-slack) to receive notifications about your pull requests, or CI pipeline events. You can also add the Slack tool after the toolchain creation.

1. Enter your **Slack webhook**. For more information, see [Slack webhook](https://api.slack.com/messaging/webhooks).
2. Enter your **Slack channel** to post message.
3. Enter the **Slack team name**. For example, if your team URL `https://team.slack.com`, the team name is `team`.
4. Choosing the events for which you want to receive notifications for **Automated Slack Notifications**.
5. Click **Continue**.

#### Common {{site.data.keyword.DRA_short}} toolchain
{: #cd-devsecops-insights-toolchain-ci}

{{site.data.keyword.DRA_short}} can optionally be included in the created toolchain and after each compliance check evidence is published. The toolchain can use an existing {{site.data.keyword.DRA_short}} instance to publish the deployment records to insights. You can link {{site.data.keyword.DRA_short}} integration from another toolchain by providing the integration ID.

1. Accept the **Current Toolchain**.
2. Click **Continue**.

You can copy the toolchain ID from the URL of your toolchain.
A toolchain's URL follows this pattern: `https://cloud.ibm.com/devops/toolchains/<toolchain-ID-comes-here>?env_id=ibm:yp:us-south`. If the URL is: `https://cloud.ibm.com/devops/toolchains/aaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee?env_id=ibm:yp:us-south` then the toolchain's ID is: `aaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee`. _Include the ID only, not the full URL._
{: note}

You can also set a target environment for the {{site.data.keyword.DRA_short}} interactions. This parameter is optional, which is used instead of the target environment from the inventory.

#### {{site.data.keyword.DRA_short}}
{: #cd-devsecops-devops-insights-ci}

You can create an instance of {{site.data.keyword.DRA_short}} to be used for the toolchain. If No configuration is required, the CI pipeline automatically uses the insights instance that is included in the toolchain.

#### Delivery pipeline private worker
{: #ci-tool-integration-private-worker}

Artifacts that are built by the toolchain and recorded in the inventory must be signed by using GaraSign, a code signing service that is powered by Garantir, before the images can be deployed to production. To enable GaraSign artifact signing, you must have a [TaaS private worker](https://taas.cloud.ibm.com/getting-started/tekton/tekton-onboarding.md){: external} and an {{site.data.keyword.IBM_notm}} CISO signing certificate. For more information, see [Using GaraSign in the one pipeline CI pipeline](/docs-draft/devsecops-working?topic=devsecops-working-devsecops-imagesigning).

The [{{site.data.keyword.deliverypipeline}} private worker](/docs/ContinuousDelivery?topic=ContinuousDelivery-private-workers) tool integration connects with one or more private workers that can run delivery pipeline workloads in isolation.

#### SonarQube
{: #cd-devsecops-sonar-ci}

Configure SonarQube as the static code analysis tool for the toolchain. SonarQube provides an overview of the overall health and quality of your source code and highlights issues that are found in new code. The static code analyzers detect tricky bugs, such as null-pointer dereferences, logic errors, and resource leaks for multiple programming languages.

1. Accept the **Default Configuration**.
2. Click **Continue**.

## Create the CI toolchain
{: #cd-devsecops-tekton-ci-create}
{: step}

1. On the Summary page, click **Create**.
2. Wait for the toolchain creation. This can take a few minutes.

## Explore the CI toolchain
{: #ci-toolchain-explore}
{: step}

Now that the CD toolchain is created with two pipelines as shown in the screen capture. Click the `ci-pipeline` tile to open and run the promotion pipeline.

![DevSecOps continuous integration toolchain](images/devsecops-ci-explore-pipeline-created.png){: caption="DevSecOps continuous integration toolchain" caption-side="bottom"}

## Run the PR-CI pipeline
{: #cd-devsecops-tekton-ci-run-pipeline}

To start the `ci-pr pipeline`, you need to create a merge request in your application repository.

1. From the CI toolchain page, click the `pr pipeline` tile. By default, it's created with the name `compliance-app-<timestamp>`.
2. Create a branch from the master branch.
3. Update code in the application or add a readme file, and save changes.
4. Submit **Merge request**.
5. On the CI toolchain page, click the **pr-pipeline** tile. Verify that the `ci-pr pipeline` is triggered by the creation of the merge request.
6. Wait for the `ci-pr pipeline` run to complete. The corresponding merge request that is in your application repository is in the `Pending` state until all the stages of the PR pipeline finish successfully.
7. After the PR pipeline run is successful, click the pipeline to explore numerous steps that are completed and to view the page. To edit and resubmit the merge request, follow the step 3 till step 7.

   ![DevSecOps PR pipeline successful](images/devsecops-ci-explore-pr-pipeline-success.png){: caption="DevSecOps PR pipeline successful" caption-side="bottom"}

### Simplified flow of tasks in the pipeline
{: #cd-devsecops-tekton-pr-ci-pipeline-flow}

In the DevSecOps PR pipeline flow of tasks, the utility tasks are omitted. For example, status-check update on GitHub, credential fetching, and so on. In the DevSecOps world, shift left is a practice that prevents and finds issues such as defects, security vulnerabilities. Shift left also performs compliance checks early in the software delivery process as shown in the figure.

![DevSecOps PR tasks](images/devsecops-ci-pr-tasks.png){: caption="DevSecOps PR tasks" caption-side="bottom"}

- Checks that can be run on the code/repository and do not need the built. The artifact should be run as early as possible to prevent noncompliant code from being merged to master branch of the repository. Evidence is not collected from the PR pipeline. The pipeline's goal is to shift compliance checks, as far left as possible.
- All checks are done when a pipeline runs. Even if a previous check fails, the pipeline progresses to the next one. To evaluate if you have any failures in your run, you need to check the final step of your pipeline, which has a pipeline evaluator.
- If you are trying to merge an emergency fix, and want to bypass the compliance checks. Add a label to your merge request to indicate the fix. The same label must be provided when running the CD pipeline.

### Run PR pipeline
{: #cd-devsecops-run-pr-pipeline}

You can start the CI pipeline in one of the following ways:

* Automatically: After a successful PR pipeline, by approving and merging the PR to the master branch.
* Manually: To trigger the CI pipeline manually, select the delivery pipeline card, click **Run Pipeline**, and select **Manual Trigger**.

In this tutorial, the CI pipeline was triggered after you merged your code changes to the master branch of your application repository.

1. On the **CI toolchain** page, click the **ci-pipeline** tile.
2. Click **Run** against your pipeline name. Observe a pipeline run is running. Wait for the pipeline run to complete.
3. After the CI pipeline run is successful, click the pipeline to explore the completed steps to view the page as shown in the screen capture.

### Run CI pipeline
{: #cd-devsecops-run-ci-pipeline}

In this document, the CI pipeline was triggered after you merged your code changes to the master branch of your application repository.

1. On the CI toolchain page, click the **ci-pipeline** tile.
2. Observe: a pipeline-run is running. Wait for the pipeline-run to complete.

After the CI pipeline run is successful, you can click the pipeline tasks to explore the completed steps.

![DevSecOps CI pipeline successful](images/devsecops-ci-explore-pipeline-success.png){: caption="DevSecOps CI pipeline successful" caption-side="bottom"}

#### Simplified flow of tasks in the pipeline
{: #cd-devsecops-tekton-ci-pipeline-flow}

In the DevSecOps CI pipeline flow of tasks, the utility tasks are omitted. For example, status-check update on GitHub, credential fetching, and so on. Green colored tasks are outputting evidence.

![DevSecOps CI Tasks](images/devsecops-ci-tasks-flow.png){: caption="5. Figure DevSecOps CI Tasks" caption-side="bottom"}

Evidence is collected from all compliance checks in the CI pipeline, to the evidence-locker repository that was provided during toolchain setup. Evidence from CI is stored under `raw/ci/<pipeline-run-id>/*.json`.

Evidence is published to the {{site.data.keyword.DRA_short}} instance inside the toolchain. You can navigate by clicking the {{site.data.keyword.DRA_short}} tool card in the toolchain. You can review the collected evidence on the Quality Dashboard page.

![DevSecOps CI evidence](images/devsecops-ci-explore-evidence.png){: caption="DevSecOps CI evidence" caption-side="bottom"}

To evaluate if you have any failures in your pipeline run, you must check the final step of your pipeline, which has a pipeline evaluator.
{: note}

### Viewing the running application
{: #ci-tekton-toolchain-view-app}

After a successful CI pipeline run, the sample application is deployed on your Kubernetes cluster, and is running in the dev namespace.

The application URL can be found at the end of the log in the `run stage` step of `deploy-dev` task of the CI pipeline run. Use that URL to verify that the application is running.

![DevSecOps CI sample app](images/devsecops-ci-explore-app-dev-namespace.png){: caption="DevSecOps CI sample app" caption-side="bottom"}

## Configure pipeline
{: #cd-devsecops-config-pipeline-ci}

To add a `commit-id` text property.

1. Click **Add property**.
2. Select **Text property**.

If you trigger the pipeline manually without `commit-id`, the pipeline takes the latest commit ID from the master branch of your app.

Example

![`commit-id`](images/commit-id.png){: caption="commit-id" caption-side="bottom"}

To add the trigger parameters.

1. Click **Run Pipeline**.
2. Select **Manual Trigger**.
3. Click **Run**.

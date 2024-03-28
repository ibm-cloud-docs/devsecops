---

copyright: 
  years: 2022, 2024
lastupdated: "2024-03-28"

keywords: tekton, pipeline, toolchain, CD, CI, automate, automation, continuous delivery, continuous integration, devsecops tutorial, devsecops, DevOps, shift-left, shift left, secure DevOps, IBM Cloud, satellite, custom target, multiple clusters

subcollection: devsecops

content-type: tutorial
services: containers, ContinuousDelivery
account-plan: paid
completion-time: 1h

---

{{site.data.keyword.attribute-definition-list}}

# Part 2: Set up a Continuous Integration (CI) toolchain
{: #tutorial-ci-toolchain}
{: toc-content-type="tutorial"}
{: toc-services="containers, ContinuousDelivery"}
{: toc-completion-time="1h"}

This tutorial is part 2 of a 4-part tutorial series where you learn {{site.data.keyword.cloud}} DevSecOps best practices by using a complete reference implementation that is available as a service and powered by {{site.data.keyword.contdelivery_full}}. In part 2 of this tutorial series, you use the toolchain template for continuous integration (CI) with security and compliance-related best practices in DevSecOps. It is preconfigured for continuous deployment with inventory integration, change management with {{site.data.keyword.gitrepos}}, evidence collection, and deployment to {{site.data.keyword.containerlong_notm}}.
{: shortdesc}

## Before you begin
{: #tutorial-ci-toolchain-prereqs}

Before you begin part 2 of this tutorial series, ensure that you complete the following prerequisites:

1. Complete [Part 1: Set up prerequisites](/docs/devsecops?topic=devsecops-tutorial-cd-devsecops).
1. View the [Getting started with DevSecOps in IBM Cloud - Part 1](https://video.ibm.com/embed/recorded/130714354) video.
1. Start the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com/). The console is the starting point of this tutorial.

## Continuous Integration (CI) toolchain introduction
{: #tutorial-ci-toolchain-intro}

The CI toolchain implements the following best practices:

* Runs a static code scanner on the application repositories to detect secrets in the application source code and vulnerable packages that are used as application dependencies
* Builds a container image on every Git commit, setting a tag based on build number, timestamp, and commit ID for traceability
* Tests the Dockerfile before the image is created
* Stores the built image in a private image registry
* Automatically configures access permissions for target cluster deployment by using API tokens that can be revoked
* Scans the container image for security vulnerabilities
* Adds a Docker signature upon successful completion
* Inserts the built image tag into the deployment manifest automatically
* Uses an explicit namespace, in cluster, to insulate each deployment (and make it easy to clear, by `kubectl delete namespace`)
* Automatically builds, validates, and deploys to the Kubernetes cluster any code merged into the target Git repository branch.

The target cluster is configured during toolchain setup (by using an {{site.data.keyword.cloud_notm}} API key and cluster name). You can later change these settings by altering the Delivery Pipeline configuration.


## Guided setup overview for the CI toolchain
{: #tutorial-ci-toolchain-guided-setup}

The {{site.data.keyword.contdelivery_short}} service provides templates that guide you through the toolchain setup process in a logical order, presenting you with the recommended configuration options that are needed to create your toolchain.

A progress indicator shows the steps to complete the configuration. You can use the progress indicator to navigate to a previous step with a mouse click. The configuration options for the current step are displayed in the main area of the page.

![DevSecOps Continuous Integration toolchain welcome page](images/devsecops-ci-welcome-page.png){: caption="Figure 1. DevSecOps Continuous Integration toolchain welcome page" caption-side="bottom"}

To advance to the next step, click **Continue**. You can advance to the next step only when the configuration for the current step is complete and valid. You can navigate to the previous step by clicking **Back**.

Some steps include a **Switch to advanced configuration** toggle. These steps by default present you with the minimum recommended configuration needed. However, advanced users that need finer grained control can click the **Switch to advanced configuration** toggle to reveal all options for the underlying integration.

![DevSecOps Advanced configuration toggle](images/devsecops-advanced-options-toggle.png){: caption="Figure 2. DevSecOps Advanced configuration toggle" caption-side="bottom"}

After all the steps are successfully completed, you create the toolchain by clicking **Create** on the Summary step.

You can always go back to previous steps in the guided installer. The toolchain installer retains all the configuration settings from the successive steps.
{: tip}

## Start the CI toolchain setup
{: #tutorial-ci-toolchain-create-options}
{: step}

Use one of the following options to access the template for the CI toolchain:

* Click **Create toolchain**.

   [![Create toolchain](images/create_toolchain_button.png "Create toolchain")](https://cloud.ibm.com/devops/setup/deploy?repository=https%3A%2F%2Fus-south.git.cloud.ibm.com%2Fopen-toolchain%2Fcompliance-ci-toolchain&env_id=ibm:yp:us-south){: external}

* From the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com/), click **Menu** ![Menu icon](../icons/icon_hamburger.svg) and select **DevOps**. On the Toolchains page, click **Create toolchain**. On the Create a Toolchain page, click **CI - Develop a secure app with DevSecOps practices**.

## Set up the CI toolchain name and region
{: #tutorial-ci-toolchain-name-region}
{: step}

The **Welcome** page summarizes the purpose of the toolchain that is created, along with pointers to documentation and related materials. When you are ready to proceed, click **Start** to begin configuring the toolchain.

Review the default information for the toolchain settings:
1. The toolchain's name identifies it in {{site.data.keyword.cloud_notm}}. Make sure that the toolchain's name is unique within your toolchains for the same region and resource group in {{site.data.keyword.cloud_notm}}.
1. The toolchain region is the region where the toolchain itself and related resources (Git repos, pipelines, and so on) are created. Toolchains can deploy to any region, regardless of where they are created.

## Set up CI tool integrations
{: #tutorial-ci-toolchain-tool-integrations}
{: step}

Multiple repositories must be configured during the guided setup, as described in the following sections.

You can clone the sample repositories or you can use your own, but the toolchain supports {{site.data.keyword.gitrepos}} repositories only.
{: note}
{: note}

### Application
{: #tutorial-ci-toolchain-application}

The application source code repository can be hosted either on GitHub or the IBM-hosted {{site.data.keyword.gitrepos}} repository from the list of source providers. For consistency, this tutorial refers to {{site.data.keyword.gitrepos}}.
{: note}

The recommended options are displayed by default, but you can click the **Switch to advanced configuration** toggle to see all of the configuration options available for the underlying Git integration. The default behavior of the toolchain is **Use default sample** that clones the sample application into an IBM-hosted {{site.data.keyword.gitrepos}} Repository.

Enter a name for the IBM-hosted {{site.data.keyword.gitrepos}} repository that is created by the toolchain as your application repository.

The region of the repository remains the same as the region of the toolchain.

If you want to link an existing Application Repository for the toolchain, select the **Bring your own application** option, and provide it as input to **Repository URL** field. As noted earlier, the toolchain currently supports linking only to existing {{site.data.keyword.gitrepos}} repositories. If you want to know more about **Bring your own application**, see [Bringing your own app to DevSecOps](/docs/devsecops?topic=devsecops-cd-devsecops-apps-byoa).

### Inventory
{: #tutorial-ci-toolchain-inventory}

The inventory repository records details of artifacts that are built by the CI toolchains.

### Issues
{: #tutorial-ci-toolchain-issues}

The issues repository records issues that are found while the CI pipeline is running.

### Secrets
{: #tutorial-ci-toolchain-secrets}

Specify the secret vault integrations to be added to your toolchain by using the provided toggles, as explained in [Managing {{site.data.keyword.cloud_notm}} secrets](/docs/secrets-manager?topic=secrets-manager-manage-secrets-ibm-cloud).

The CI toolchain supports **Arbitrary secrets** and **IAM credentials** secret types only.
{: note}

This tutorial uses {{site.data.keyword.secrets-manager_full}} as the vault for secrets. The **Region**, **Resource group**, and **Service name** fields are automatically populated based on available choices. Click the drop-down indicators to see the other choices.

### Evidence Storage
{: #tutorial-ci-toolchain-evidence-storage}

The evidence repository stores all the evidence and artifacts that are generated by the DevSecOps CI pipeline.

Toggle the COS bucket slider to also store all the evidence in a Cloud {{site.data.keyword.cos_short}} bucket that can be configured on the next page.

### Cloud {{site.data.keyword.cos_short}} bucket
{: #tutorial-ci-toolchain-cos-bucket}

To use this feature, you must have a Cloud {{site.data.keyword.cos_short}} instance and a bucket. [Click here](/docs/devsecops?topic=devsecops-cd-devsecops-cos-bucket-evidence) to create a Cloud {{site.data.keyword.cos_short}} instance, and [read the recommendation](/docs/devsecops?topic=devsecops-cd-devsecops-cos-config) for configuring a bucket that can act as a compliance evidence locker.
{: note}

* **Cloud {{site.data.keyword.cos_short}} instance**, **Bucket name**, and **Cloud {{site.data.keyword.cos_short}} endpoint** fields are automatically populated.
* Enter the Service ID API key
   * Preferred: An existing key can be imported from a secrets vault by clicking the key icon.
   * An existing key can be copy and pasted.

The endpoint field is optional. It is recommended to select or provide the endpoint during the setup of the toolchain or during the pipeline run.
{: note}

### Deploy
{: #tutorial-ci-toolchain-deploy}

Configure the target Kubernetes cluster where the application is deployed:

* **App name:**
   The name of the application.
   * Default: hello-compliance-app

* **{{site.data.keyword.cloud_notm}} API Key:**
   The API key is used to interact with the ibmcloud CLI tool in several tasks.
   * Preferred: An existing key can be imported from a secrets vault by clicking the key icon.
   * An existing key can be copy and pasted.
   * A new key can be created from here by clicking the **New +**.

The newly generated API key can be immediately saved to a secrets vault.
{: tip}

If the API key is valid and has sufficient access, the Container registry region and namespace Cluster region, name, namespace, and resource group are automatically populated. You can change any of these fields to match your configuration if needed.

### Image Signing
{: #tutorial-ci-toolchain-image-signing}

Any images that are built by this toolchain and recorded in the inventory must be signed before they can be deployed to production.

To enable image signing, you need to provide a GPG key. You can select an existing key from a secret store or input the value directly. Alternatively you can create a new GPG key by clicking **NEW**.

Ensure that the key follows the appropriate encoding as required by the chosen tool to store secrets.
{: note}

### DevOps Insights
{: #tutorial-ci-toolchain-insights}

[IBM Cloud DevOps Insights](/docs/ContinuousDelivery?topic=ContinuousDelivery-di_working) is included in the created toolchain. View your pipeline test results for every build, from every deployment and environment. 

You do not need to provide any configuration steps for DevOps Insights (DOI). Select a DOI instance for your toolchain:

* Current (to be created) CI toolchain
* Another toolchain instance: select an existing toolchain (that has a DOI instance)

### SonarQube
{: #tutorial-ci-toolchain-sonarqube}

SonarQube provides an overview of the overall health and quality of your source code and highlights issues that are found in new code. The static code analyzers detect tricky bugs, such as null-pointer dereferences, logic errors, and resource leaks for multiple programming languages.

Default configuration
:   Run a SonarQube scan in an isolated Docker-in-Docker container without persisting the SonarQube instance.

Cluster configuration
:   A new SonarQube instance is provisioned in the Kubernetes cluster (chosen at the Deploy step) during the first CI pipeline run.

Custom configuration
:   Link an existing SonarQube instance with the toolchain.

### Optional tools
{: #tutorial-ci-toolchain-optional-tools}

#### Slack
{: #tutorial-ci-toolchain-slack}

Configure the [Slack Tool](/docs/ContinuousDelivery?topic=ContinuousDelivery-slack) to receive notifications about your PR/CI Pipeline events.
* [Slack webhook](https://api.slack.com/incoming-webhooks)
* Preferred: An existing webhook can be imported from a secrets vault by clicking the key icon.
* An existing webhook can be copy and pasted.
* Slack channel: an existing Slack channel to post message to
* Slack team name: the phrase before *.slack.com* in our team URL. For example, if your team URL https://team.slack.com, the team name is *team*.
* Automated Slack Notifications: Customize your selection by choosing the events for which you want to receive notifications.

You can add the Slack Tool after toolchain creation.
{: note}

## Create the CI toolchain
{: #tutorial-ci-toolchain-summary}
{: step}

On the Summary page, click **Create**, and wait for the toolchain to be created.

Other toolchain integrations can be configured after the pipeline is created.
{: tip}

## Explore the CI toolchain
{: #tutorial-ci-toolchain-explore}
{: step}

The DevSecOps CI toolchain looks like this:

![DevSecOps Continuous Integration Toolchain](images/devsecops-ci-explore-pipeline-created.png){: caption="Figure 3. DevSecOps Continuous Integration Toolchain" caption-side="bottom"}

It contains two pipelines:
* **ci-pr-pipeline (PR)**: triggers when a new Merge Request (MR) is submitted in the app repository.
* **ci-pipeline (CI)**: triggers when an MR is merged to master in the app repository, but can be manually triggered.

### Run the ci-pr Pipeline
{: #tutorial-ci-toolchain-pr-pipeline-run}

To start the ci-pr pipeline, you need to create a merge request in your application repository.
1. From the CI toolchain page, click the application repository tile. By default it gets created with a name `compliance-app-<timestamp>`.
1. Create a branch from master branch.
1. Update some code like in app or readme file and save changes.
1. Submit merge request.
1. On the CI toolchain page, click the **pr-pipeline** tile. Verify that the ci-pr pipeline is triggered (by the creation of the Merge Request).
1. Wait for the ci-pr pipeline run to complete.

The corresponding Merge Request that is in your application repository is in `Pending` state until all the stages of PR Pipeline finish successfully.

After the PR Pipeline run is successful, you can click it to explore numerous steps completed.

![DevSecOps PR Pipeline Successful](images/devsecops-ci-explore-pr-pipeline-success.png){: caption="Figure 4. DevSecOps PR Pipeline Successful" caption-side="bottom"}

1. Navigate back to the merge request.
1. Merge the request so that your changes are copied to the master branch of your application repository: the CI pipeline is automatically triggered.

### Run the CI pipeline
{: #tutorial-ci-toolchain-ci-pipeline-run}

Start the CI pipeline in one of the following ways:
* Automatically: after a successful PR pipeline, by approving and merging the PR to the master branch.
* Manually: to trigger the CI pipeline manually, select the Delivery Pipeline card and click **Run Pipeline** and select **Manual Trigger**.

In this tutorial, the CI Pipeline was triggered after you merged your code changes to the master branch of your application repository.
1. On the **CI toolchain** page, click the **ci-pipeline** tile.
1. Observe: a pipeline-run is running. Wait for the pipeline-run to complete.

After the CI Pipeline run is successful, you can click it to explore the completed steps.

![DevSecOps CI Pipeline Successful](images/devsecops-ci-explore-pipeline-success.png){: caption="Figure 5. DevSecOps CI pipeline" caption-side="bottom"}

Evidence from all compliance checks in the CI pipeline is uploaded to the evidence-locker repository, but also to the COS bucket if enabled during setup.
Evidence is also published to DevOps Insights. You can navigate to DOI by clicking the DevOps Insights tool card in the toolchain. You can review the collected evidence on the Quality Dashboard page.

![DevSecOps CI Evidence](images/devsecops-ci-explore-evidence.png){: caption="Figure 6. DevSecOps CI evidence" caption-side="bottom"}

To evaluate if you have any failures in your pipeline run, you need to check the final step of your pipeline, which has a pipeline evaluator.
{: note}

### Viewing the running application
{: #tutorial-ci-toolchain-view-app}

After a successful CI pipeline run, the sample application is deployed on your Kubernetes cluster.

The app url can be found at the end of the log of the `run stage` step of `deploy-dev` task of the CI Pipeline run. Use that url to verify that the app is running.

### Pipeline customization
{: #tutorial-ci-toolchain-pipeline-config}

Custom scripts are extension points in the pipeline where adopters, teams, and users can provide scripts to run custom tasks that are required by their continuous integration and continuous deployment strategies.

Custom scripts control the pipeline stages. You can use a configuration file (`pipeline-config.yaml`) to configure the behavior of stages, script content, and the base image that runs the scripts. The scripts and configuration for pipeline stages are loaded from a Git repository (repo) that can either be the application (app) repo (similar to `.travis.yml` or `Jenkinsfile`) or a custom repo.

More detailed information on customizing the CI pipelines can be found [here](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-stages#cd-devsecops-pipelines-custom-customize). 

### Wrapping up
{: #tutorial-ci-toolchain-done}

By completing the CI part of this tutorial, you:
* Created a DevSecOps CI toolchain
* Modified some code in the application repository
* Ran the ci-pr pipeline before you merge your changes
* Ran the ci-pipeline to build, test, and deploy your changes to your dev environment.

## Next steps
{: #tutorial-ci-toolchain-next}

Continue to [Part 3: Set up a Continuous Deployment (CD) toolchain](/docs/devsecops?topic=devsecops-tutorial-cd-toolchain).

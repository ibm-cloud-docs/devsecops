---

copyright:
  years: 2023
lastupdated: "2023-10-16"

keywords: DevSecOps

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Customizing DevSecOps pipelines for beginners
{: #cd-devsecops-basics-pipelines-customization}

Learn the basics for adopting DevSecOps and onboard your first application or microservice.
{: shortdesc}

## About DevSecops toolchains
{: #devsecops-about}

You discovered and tested {{site.data.keyword.cloud_notm}} [DevSecOps](https://www.ibm.com/topics/devsecops) Continuous Integration ([CI](/docs/devsecops?topic=devsecops-tutorial-ci-toolchain)), Continuous Deployment ([CD](/docs/devsecops?topic=devsecops-tutorial-cd-toolchain)), and Continuous Compliance ([CC](/docs/devsecops?topic=devsecops-tutorial-cc-toolchain)) toolchains that implement DevSecOps best practices and security tools.

Now, you're ready to onboard your own application or microservice and adopt DevSecOps.

## Before you begin
{: #before-begin}

You need the following resources to onboard an application in a DevSecOps toolchain:
- An application source code Git repository that contains your application's code, often referred to as the "app repo".
- A `.pipeline-config.yaml` file. This file is the core configuration file that is used by CI, CD, and CC pipelines to customize any of the stages in the pipeline run process. Get started with a [sample `.pipeline-config.yaml` file](https://us-east.git.cloud.ibm.com/open-toolchain/hello-compliance-app/-/blob/master/.pipeline-config.yaml), which you can download and customize to fit your needs. All stages except the start stage can be customized by using the `.pipeline-config.yaml` file. The file triggers and runs your custom scripts to build, test, and deploy your application. You can change the name of the `.pipeline-config.yaml` file, or use different files for different pipelines or triggers. Make sure that the parameter values in your pipeline or trigger match your configuration file. For more information, see [pipeline parameters](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm).

{{site.data.keyword.cloud_notm}} has the following DevSecOps templates to get you started:

- [Node sample application](https://us-south.git.cloud.ibm.com/open-toolchain/hello-compliance-app)
- [CodeEngine compliance sample application](https://us-south.git.cloud.ibm.com/open-toolchain/code-engine-compliance-app)
- [Go compliance sample application](https://us-south.git.cloud.ibm.com/open-toolchain/go-gin-compliance-app)
- [Python compliance sample application](https://us-south.git.cloud.ibm.com/open-toolchain/python-compliance-app)
- [Node / Cloudant compliance sample application](https://us-south.git.cloud.ibm.com/open-toolchain/node-cloudant-compliance-app)

### How DevSecOps pipelines use repositories
{: #devsecops-repos}

To build, test, and deploy your application, DevSecOps pipelines use two repositories:
- `app-repo`: The application repository, which contains the source code for your application.
- `config-repo`: The configuration repository, which contains your pipeline configuration YAML files and scripts.

In the [DevSecOps sample app](https://us-east.git.cloud.ibm.com/open-toolchain/hello-compliance-app/-/tree/master/scripts), these two repositories are the same. Most of the DevSecOps adopters start with this pattern. But, as you onboard more microservices, a dedicated and separate pipeline configuration repository might be needed. Because the application repository and the configuration repository are the same in the sample app, the repository is cloned twice when the pipelines are run, which can lead to errors. Therefore, it's a best practice to have a separate configuration repository to host configuration files and scripts that can be shared and reused among different DevSecOps toolchains and pipelines. For more information about customizing the configuration repository, see the [advanced customization steps](/docs/devsecops?topic=devsecops-cd-devsecops-advanced-pipelines-customization).

DevSecOps scripts consider the `app-repo` and the `config-repo` to be two separate repositories. The scripts clone the repositories twice during the start stage of the pipeline. These clones are referred to as the `app-repo` and the `one-pipeline-config-repo`. Each stage is run in the context of the `config repo`.
{: important}

## Onboarding an application
{: #onboard-app}

For simplicity, create and test a DevSecOps CI toolchain with the [sample Node application](https://us-south.git.cloud.ibm.com/open-toolchain/hello-compliance-app) before you continue.
{: note}

There are three main ways to onboard your first application into an existing DevSecOps CI toolchain:
- Option 1: Add your application repo to the toolchain and use the sample application repo as the configuration repo.
- Option 2: Replace the sample application repo with your application repo.
- Option 3: Add your application repo to the toolchain and use a dedicated configuration repository. For more information about this option, see the [advanced customization steps](/docs/devsecops?topic=devsecops-cd-devsecops-advanced-pipelines-customization).

DevSecOps toolchains use Gitlab repositories that are managed by {{site.data.keyword.IBM_notm}}, also known as GRIT. Other Git providers can be used instead. Your app repo might be hosted on GitHub or Gitlab, for example.

### Option 1: Add your application repo and use the sample application repo as the configuration repo
{: #add-app-use-sample-as-config}

To add your application repository to the toolchain and use the sample application repository as the configuration repository, complete the following steps:

1. In the {{site.data.keyword.cloud_notm}} console, click the **Menu** icon ![Menu icon](../../icons/icon_hamburger.svg) > **DevOps** > **Toolchains**, and select the toolchain that you want to edit.
1. Click **Add**.
1. Select where your app repo is hosted, either `Gitlab` or `GitHub`.
1. Use the default server or add a new one.
1. Enter the URL of the custom server and Personal Access Token.
1. Click **Create integration**.
1. Enter your application source code repository URL.
1. Click **Create integration**.

Next, specify the sample application repo as your configuration repo by completing the following steps:

1. In the {{site.data.keyword.cloud_notm}} console, click the **Menu** icon ![Menu icon](../../icons/icon_hamburger.svg) > **DevOps** > **Toolchains**, and select the toolchain that you want to edit.
1. Click **pr-pipeline**.
1. Click **Settings** and go to the **Environment properties** tab.
1. Edit the value of the `pipeline-config-repo` property to point to the sample application repository URL.
1. Go back to your CI toolchain.
1. Click **ci-pipeline**.
1. Click **Settings** and go to the **Environment properties** tab.
1. Edit the value of the `pipeline-config-repo` property to point to the sample application repository URL.

Your CI pipelines now use the sample app repo `pipeline-config` as your configuration repo.

### Option 2: Replace the sample application repository with your own application repository
{: #replace-sample-app}

To replace the sample app repo with your own app repo, complete the following steps:

1. In the {{site.data.keyword.cloud_notm}} console, click the **Menu** icon ![Menu icon](../../icons/icon_hamburger.svg) > **DevOps** > **Toolchains**, and select the CI toolchain that you want to edit.
1. Find the sample application source code repository and select **Configure**.
1. Replace the repository URL with your application source code repository URL.
1. Click **Save integration**.

After you replace the sample app repo, make sure that the new app repo contains a `.pipeline-config.yaml` file and corresponding scripts. Either copy the `.pipeline-config.yaml` file and scripts from the sample app repository to your application repository, or use [this sample configuration file](https://eu-de.git.cloud.ibm.com/open-toolchain/hello-compliance-app/-/blob/master/.pipeline-config.yaml).
{: important}

### Configuring the CI pipelines
{: #configure-ci-pipelines}

After you add your application repository, you must configure the CI pipelines to work with the new repository.

#### Configuring the pipeline triggers
{: #configure-pipeline-triggers}

Default triggers use the sample application repository, so you must update them to use your app repository. Verify the trigger settings, and modify them if needed to make sure all the triggers point to your app repo. Complete the following steps:

1. In the {{site.data.keyword.cloud_notm}} console, click the **Menu** icon ![Menu icon](../../icons/icon_hamburger.svg) > **DevOps** > **Toolchains**, and select the CI toolchain that you want to edit.
1. Click **pr-pipeline**.
1. Edit the **Git PR Trigger** and provide your application repository URL and branch.
1. Click **Save**.
1. Go back to your CI toolchain and click **ci-pipeline**.
1. Edit the **Git CI Trigger** and provide your application repository URL and branch. Make sure that the app name is correct in the **Properties** section.
1. Click **Save**.
1. Edit the **Manual Trigger**. In the **Properties** section, make sure that the app name is correct.
1. Make sure that the repository and branch properties are correct and pointing to your app repository.

#### Configuring the `.pipeline-config.yaml` file
{: #configure-pipeline-config-yaml}

Either copy the `.pipeline-config.yaml` file from the sample app repository to your application repository, or use [this sample configuration file](https://eu-de.git.cloud.ibm.com/open-toolchain/hello-compliance-app/-/blob/master/.pipeline-config.yaml).

For both `pr-pipeline` and `ci-pipeline`, make sure that the `pipeline-config`, `pipeline-config-branch`, and `pipeline-config-repo` parameters are correctly set and match your configuration. If these parameters are not set correctly, you might commit changes to the wrong branch, which results in an error with your pipeline.

If the `pipeline-config-repo` variable is not set, DevSecOps pipelines assume it’s the same repository as your application source code repository.
{: important}

### Using custom scripts with DevSecOps
{: #byos-devsecops}

The `pipeline-config.yaml` file is the key component that orchestrates and customizes the DevSecOps pipeline behavior. The file uses scripts to build, test, and deploy your application. The file defines how the stages are configured, and which scripts are run. For more information, see [custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-stages#cd-devsecops-pipelines-custom-customize).

There are two major categories of scripts that are used by DevSecOps pipelines:
- Application-related scripts that are used to build, test, and deploy your application. These scripts are your own responsibility and out of scope for DevSecOps support. As these scripts don’t have a default implementation, you must add them to your application or configuration repository. You might need to pull the scripts from Jenkins or another source.
- Security and compliance scripts that run security and compliance scans. Most come with [default scripts](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-commons) that you can override to use your own custom implementation.

Except for the start stage, each stage of a CI, CD, or CC pipeline can be customized with your own scripts to override the default implementation of the stage. The start stage can’t be customized with your own scripts.
{: important}

Though Bash scripts are provided as samples, you can build, test, and deploy your application by using other languages like Python or Go. Make sure you're using the correct `image` for each stage. Refer to the [Docker images in DevSecOps pipelines section](#docker-images-in-devsecops-pipelines).

 Refer to the table [Stages and tasks](#devsecops-ci-pipeline-stages) that summarize various stages of the CI Pipeline. The table also provides a consolidated information about whether the stage has a default reference implementation, whether it can be customized or skipped, or whether there is explicit evidence collection that is required by the stage execution.


#### Migrating from Jenkins or Travis to DevSecOps
{: #migrate-from-jenkins-travis}

Most DevSecOps adopters don't start from nothing. Most adopters already have continuous integration and continuous delivery processes in place, along with a corresponding script library. These processes are typically implemented by using Jenkins, Travis, or some other platform.

Key points for migrating to DevSecOps:
- The `pipeline-config.yaml` configuration file should reflect how the CI and CD pipelines are currently orchestrated. Stages and steps should be mapped to [DevSecOps pipeline stages](/docs/devsecops?topic=devsecops-cd-devsecops-ci-pipeline).
- You should use the same scripts, base images, and variables that are already in place.
- Secret properties need to be migrated to a [secret store](/docs/devsecops?topic=devsecops-cd-devsecops-toolchains-secrets).
- Nonsecret properties need to be added as pipeline or trigger properties.

#### Working in development mode
{: #dev-mode}

You can use development mode for CI and CD pipelines to test the implementation of your `.pipeline-config.yaml` file and scripts. The development mode pipeline does not run any security or compliance-related tasks, which reduces the runtime for the pipeline. For more information, see [running pipelines in development mode](/docs/devsecops?topic=devsecops-devsecops-devmode).

Use development mode for development purposes only. Development mode is not a replacement for the official DevSecOps CI and CD pipelines, which remain the reference implementations.
{: important}

### Configuring the CD pipeline
{: #configure-cd-pipeline}

In the [DevSecOps continuous integration, continuous deployment workflow](/docs/devsecops?topic=devsecops-cd-devsecops-arch#cd-devsecops-design), the CI pipeline pushes updates to the inventory repository during the `deploy-release` stage of the CI pipeline. For more information, see [the sample release.sh script](https://us-east.git.cloud.ibm.com/open-toolchain/hello-compliance-app/-/blob/master/scripts/release.sh).

In this workflow, multiple CI triggers and different DevSecOps CI toolchains can contribute to the same inventory repository.

Contrary to the CI pipeline, the deployment script that is used by the CD pipeline needs to be adapted from your current scripts in Jenkins, Travis, or some other platform to use the entries in the [inventory repo](/docs/devsecops?topic=devsecops-cd-devsecops-inventory).

This [sample code](https://us-east.git.cloud.ibm.com/open-toolchain/hello-compliance-deployment/-/blob/master/scripts/deploy.sh#L12) shows how to retrieve information from the inventory and use it to run a Kubernetes deployment.

### DevSecOps scripts location
{: #devsecops-scripts-location}

DevSecOps pipelines come with default security and scanning tools and associated scripts.

For example, the beginning of a stage log references the default script:

```bash
scan-artifact:
  image: icr.io/continuous-delivery/pipeline/pipeline-base-ubi:3.24
  dind: true
  dind_image: icr.io/continuous-delivery/toolchains/devsecops/docker:20.10.21-dind
  abort_on_failure: false
  image_pull_policy: IfNotPresent
  script: |
    #!/bin/sh

    "/opt/commons/scan-artifact/scan.sh"
```

`/opt/commons/scan-artifact/scan.sh` is the default script.

The DevSecOps pipeline provides the path to the root folder of [the commons library](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-commons) in an environment variable called `COMMONS_PATH`, which is available in all tasks and steps. To access a script that is built into the compliance base image, use the `COMMONS_PATH` variable with the script folder that you need:

```bash
source "${COMMONS_PATH}/<script folder in commons>/<script file name>
```

Refer to the table [Stages and tasks](#devsecops-cd-pipeline-stages) that summarizes various stages of the CD Pipeline. The table also provide a consolidated information about whether the stage has a default reference implementation, whether it can be customized or skipped, or whether there is explicit evidence collection required by the stage execution.

### Environment properties
{: #pipeline-parameters}

DevSecOps pipelines come with [predefined environment properties](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm), but you can add as many custom properties as needed. You can access these properties values in your scripts by using the [`get_env` command](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#get_env).

After you add your property and its optional default value to the pipeline or trigger, use the `get_env` command in your script to retrieve the property's value. The following example retrieves the `my-variable` property's value:
```bash
MY_VARIABLE=$(get_env my-variable "")
```
You can also dynamically override a property's value in a script by using the [`set_env` command](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#set_env). The following example overrides the value in the `my-variable` property:
```bash
set_env my-variable new-value
```

### Skipping stages
{: #skip-stage}

Some stages might be irrelevant. For example, you might not be building any Docker images, or you haven't implemented acceptance tests yet. If you want to skip a stage, you can edit the `.pipeline-config.yaml` file to include `exit 0`, or set the `skip` variable to `true`.

The following example adds `exit 0` to skip a stage:

```bash
scan-artifact:
  image: icr.io/continuous-delivery/pipeline/pipeline-base-ubi:3.24
  dind: true
  dind_image: icr.io/continuous-delivery/toolchains/devsecops/docker:20.10.21-dind
  abort_on_failure: false
  image_pull_policy: IfNotPresent
  skip: false
  runAfter: null
  script: |
    #!/bin/sh

    exit 0
    "/opt/commons/scan-artifact/scan.sh"
```

### Docker images in DevSecOps pipelines
{: #docker-images-in-devsecops}

By default, DevSecOps pipelines use [{{site.data.keyword.IBM_notm}} Continuous Delivery images](/docs/ContinuousDelivery?topic=ContinuousDelivery-pipeline_versioned_base_images#image_version_contents). These images contain some of the most common tools such as Node and Java to run your scripts. You can also use other vendor's images or your own custom images that contain your preferred tools.

Docker images that are used by DevSecOps pipelines are specified in the [`.pipeline-config.yaml`](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-stages#cd-devsecops-pipelines-custom-customize#cd-devsecops-scripts-configparm) file. Each stage can use a different image.

#### Changing the image version
{: #change-image-version}

Based on your requirements, you might need to use a different version of an image. To change the image version, edit the `.pipeline-config.yaml` file. For example, if you referenced the following image in your YAML file `icr.io/continuous-delivery/pipeline/pipeline-base-ubi`:```3.24```, changing it to `icr.io/continuous-delivery/pipeline/pipeline-base-ubi`:```3.27``` results in using version 3.27 of the image.

In the same way, change the `dind_image` for the stage:

```bash
scan-artifact:
  image: icr.io/continuous-delivery/pipeline/pipeline-base-ubi:3.24
  dind: true
  dind_image: icr.io/continuous-delivery/toolchains/devsecops/docker:20.10.21-dind
(...)
```

## Getting support
{: #get-support-devsecops}

As you customize your DevSecOps pipelines, get help directly from the {{site.data.keyword.cloud_notm}} development teams by [joining us on Slack](https://join.slack.com/t/ibm-devops-services/shared_invite/zt-1znyhz8ld-5Gdy~biKLe233Chrvgdzxw).

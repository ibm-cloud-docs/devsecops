---

copyright:
  years: 2023
lastupdated: "2023-11-08"

keywords: DevSecOps

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Advanced DevSecOps pipeline customization
{: #cd-devsecops-advanced-pipelines-customization}

Learn about the advanced features of DevSecOps adoption after you onboard your first application or microservice.
{: shortdesc}

Make sure that you review the [basics of DevSecOps pipeline customization](/docs/devsecops?topic=devsecops-cd-devsecops-basics-pipelines-customization). There, you can learn about the different templates that are available, support options, and other important information to get you started with DevSecOps.

## Design choices
{: #design-choices}

While you are onboarding more applications and microservices to DevSecOps, you might have the following design questions:

- Do I need one toolchain per microservice?
- Do I need one pipeline per microservice?
- Do I need to use a shared DevSecOps repository for inventory and issues or do I need separate repositories?

The following information and best practices are meant to help you make these design choices.

### Toolchains and pipelines considerations
{: #toolchains-pipelines-considerations}

Most applications are made of multiple microservices with different source repositories. Usually, a single toolchain is used to host microservices that are logically grouped. In general, use one toolchain and one pipeline, or as few pipelines as possible, but multiple triggers. Within each pipeline, you can duplicate and configure as many triggers as you need, up to 1024 triggers per pipeline. This strategy helps enforce common processes, scripts, and configuration files. For more information, see [configuring multiple apps on one CI toolchain](/docs/devsecops?topic=devsecops-cd-devsecops-multiple-apps).

This approach has the following advantages:
- Using fewer pipelines avoids duplication and decreases errors. Updates are also easier to maintain, for example, when an environment property or secret is added, edited, or deleted.
- Onboarding a service is quicker with this approach. Add a Git integration for your microservice, duplicate the Git and manual triggers and modify them as needed. Then, make sure your `.pipeline-config.yaml` file and scripts are implemented for your microservice.

This approach has the following drawbacks:
- One edit can impact every team that is using the same pipeline.
- User access is managed by using [Identity and Access Management (IAM)](/docs/ContinuousDelivery?topic=ContinuousDelivery-toolchains-using&interface=ui#managing_access_resource_groups) and is set at the toolchain level, not the pipeline level. So, if you are using a single toolchain for multiple services, every member of the team can view other teams' pipelines. Depending on the user's access in IAM, they might be able to edit, delete, or trigger a pipeline.

### Billing considerations
{: #advanced-billing-considerations}

The {{site.data.keyword.contdelivery_short}} service determines billing based on the number of authorized users per CD instance, and how many resource groups you use for your toolchains. Users who have access to your repositories are also considered authorized users. For more information, see [authorized users](/docs/ContinuousDelivery?topic=ContinuousDelivery-limitations_usage&interface=ui#authorized_users). To reduce costs, either you should organize all of your toolchains in the same resource group, or you should configure {{site.data.keyword.contdelivery_short}} consolidated billing within an enterprise account hierarchy. For more information, see [Consolidated billing](/docs/ContinuousDelivery?topic=ContinuousDelivery-limitations_usage#consolidated_billing).

### DevSecOps repositories considerations
{: #repo-considerations-devsecops}

If you are creating an application that is composed of multiple microservices, most DevSecOps repositories - except the evidence repository - can be shared across the microservices.

For more information, see [how DevSecOps pipelines use repositories](/docs/devsecops?topic=devsecops-cd-devsecops-basics-pipelines-customization#devsecops-repos).

#### Shared configuration repository considerations
The DevSecOps sample applications come with a co-located .pipeline-config.yaml configuration file and corresponding scripts.
When onboarding multiple microservices, a best practice is to separate source code repositories from DevSecOps configuration files and scripts. Store these in a separate and dedicated common configuration repository that can be used by your CI, CD, or CC pipelines.

This consists of:
- A dedicated Git repository to host a common scripts library and .pipeline-config.yaml file(s).
- A single .pipeline-config.yaml file that's common to all services. If it's too complex, or not possible, use different .pipeline-config.yaml files.
- Customization on a per folder basis. Implement your .pipeline-config.yaml file(s) to point to different scripts folders in your config repo. Organize your scripts in separate CI, CD, and CC folders, or based on the service language (Go, Python, NodeJS), service, application name, or whatever best fits your needs.

Change your pipeline(s) to use this shared configuration repository by setting the value of the `pipeline-config-repo` pipeline property (optionally, the `pipeline-config-branch`) to the shared configuration repository URL.

#### Inventory repository considerations
{: #inventory-repo-considerations}

A single inventory repository can be shared among many pipelines and toolchains. Several CI toolchains, pipelines, and triggers can contribute to the same inventory repository. CI pipelines push updates to the inventory repository, usually during the release stage. The inventory repository is then used as the source input for the CD pipeline. In general, it's a best practice to group inventory repositories into the same toolchain to reduce the number of change requests that are eventually created.

Using a shared inventory repository is an architectural decision that needs to be made before you start onboarding microservices because this decision affects the number of change requests that are created when a pipeline runs. For example, let's say you want to onboard 10 microservices to DevSecOps. You can have 10 different CD toolchains, with 10 different inventory repositories, which results in 10 different change requests each time a CD pipeline runs. It's easier to manage these 10 different microservices if they share a common inventory repository and a common CD toolchain. This results in only one change request even if you make updates to multiple microservices because they are all sharing the inventory repository and toolchain.

#### Issues repository considerations
{: #issues-repo-considerations}

Your issues repository is where all vulnerability issues for your microservices are tracked. If you decide to use a shared inventory repository, you might want to use a shared issues repository as well.

Setting `incident-assigness` and `incident-labels` at the pipeline or trigger level helps automatically assign issus to the correct team. For more information, see [continuous deployment parameters](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm).


### {{site.data.keyword.cos_full_notm}} considerations
{: #cos-bucket-considerations}

If you are using a shared inventory repository and a shared issues repository, you can use a shared instance of {{site.data.keyword.cos_full}} as well. Complete the following steps:

1. Create an [instance of {{site.data.keyword.cos_full_notm}}](/docs/cloud-object-storage?topic=cloud-object-storage-provision) to use across toolchains and pipelines.
1. Within that shared {{site.data.keyword.cos_full_notm}} instance, create one {{site.data.keyword.cos_short}} bucket per microservice.
1. Set up pipelines and triggers, if applicable, to use the {{site.data.keyword.cos_full_notm}} bucket by setting the `cos-bucket-name` environment property.

All microservices within the same CI, CD, and CC pipelines share one {{site.data.keyword.cos_short}} bucket, regardless of the deployment environment. The {{site.data.keyword.cos_short}} bucket functions similarly to the evidence repository, but without the performance issues that can occur with a substantial evidence repository. Because performance issues don't occur with a substantial {{site.data.keyword.cos_short}} bucket, you don't need to prune evidence from {{site.data.keyword.cos_short}} buckets.
{: important}

### Deploying to multiple environments
{: #deploy-multi-envs}

Deploying to multiple target environments with the CD pipeline can be achieved in several ways.

Each `targeted environment` should have a corresponding branch in the inventory repository, where changes are promoted from a `source` to a `target` branch.
{: remember}

The following optional designs might fit your architecture:
- Use one manual trigger per target environment. Duplicate a trigger, then edit the environment properties to fit the new environment.
- Use a Git configuration repository with one folder per target environment, where specific configuration settings are stored in files.

## Working with default and custom scripts
{: #default-and-custom-scripts}

Most of the security and compliance scripts come with [default scripts](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-commons) that are run if no custom scripts are provided for a stage. It can sometimes be difficult to determine which script is run, where to find the corresponding source script, and how to override the default scripts.

### Identify which script is run
{: #id-script}

To identify which script is run for a stage, open the pipeline run (preferably a CI pipeline). Expand a task, then click `run-stage` to open the logs. The beginning of the `run-stage` logs provides information about the scripts that were used in the stage, including which repository the script is located in. In the logs, you can scroll to find more details about the scripts that were run. For example, the `code-compliance-checks` task might have the following information in the `run-stage` log, which includes environment properties for the stage:

``` bash
compliance-checks:
  image: icr.io/continuous-delivery/pipeline/pipeline-base-ubi:3.18
  dind: true
  abort_on_failure: false
  image_pull_policy: IfNotPresent
  script:
    #!/bin/sh

    "/opt/commons/compliance-checks/run.sh"
```

In that example, the script that is run is `/opt/commons/compliance-checks/run.sh`, which is located in the [commons library](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-commons). Use this commons library as a source to copy code that can be customized for specific edge cases. In the example, `compliance-checks/run.sh` is located in the [compliance-commons library](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-commons/-/blob/master/doc/compliance-checks__run.md).

### Overriding default scripts
{: #override-default-scripts}

To override default scripts, complete the following steps:
1. In the stage log, copy the snippet that identifies a default script:

``` bash
compliance-checks:
  image: icr.io/continuous-delivery/pipeline/pipeline-base-ubi:3.18
  dind: true
  abort_on_failure: false
  image_pull_policy: IfNotPresent
  script:
    #!/bin/sh

    "/opt/commons/compliance-checks/run.sh"
```
1. Paste the snippet into your `.pipeline-config.yaml` file.
1. Add your custom code before or after the script is called. 
   This snippet invokes the default [compliance-checks script](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-commons/-/blob/master/doc/compliance-checks__run.md)
   {: note}
1. Edit, delete, or add properties as needed.

The following example shows the updated script in your `.pipeline-config.yaml` file:

```bash
compliance-checks:
  image: icr.io/continuous-delivery/pipeline/pipeline-base-ubi:3.18
  dind: true
  abort_on_failure: false
  image_pull_policy: IfNotPresent
  script: |
    #!/bin/sh
    # run some custom script here
    ./scripts/my-custom-script1.sh

    "/opt/commons/compliance-checks/run.sh"

    # then some additional work
    ./scripts/my-custom-script2.sh
```

For more information, see [custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize).

## Terraform templates to use DevSecOps toolchains-as-code
{: #tf-templates-tac}

The following toolchains provide code to create DevSecOps CI, CD, and CC toolchains by using Terraform:
- [Terraform IBM DevSecOps CI Toolchain](https://us-south.git.cloud.ibm.com/open-toolchain/terraform-ibm-devsecops-ci-toolchain)
- [Terraform IBM DevSecOps CD Toolchain](https://us-south.git.cloud.ibm.com/open-toolchain/terraform-ibm-devsecops-cd-toolchain)
- [Terraform IBM DevSecOps CC Toolchain](https://us-south.git.cloud.ibm.com/open-toolchain/terraform-ibm-devsecops-cc-toolchain)

Early access to these Terraform toolchains is being provided on an as-is basis. These toolchains are in active development, so variables and variable names might change.
{: #note}

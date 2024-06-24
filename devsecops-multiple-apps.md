---

copyright:
  years: 2024
lastupdated: "2024-05-24"

keywords: DevSecOps, inventory model, inventory, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}


# Configuring multiple apps on one CI toolchain
{: #cd-devsecops-multiple-apps}

When you run service offerings through Continuous Integration (CI), consider consolidating all of your offerings microservices or components into a single common Toolchain, rather than managing multiple toolchains for each repo.
{: shortdesc}

To configure the [CI Pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-ci-pipeline) and [pull request (PR) pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-pr-pipeline) to work for multiple applications' repositories is simple. Use the following steps and changes to enable multiple applications.

## Toolchain customization
{: #multiple-apps-toolchain-customization}


Customize your toolchain by adding your toolchain to multiple apps and increase its functionality. These are the steps:


1. Add a GitHub Tool integration for each application that you want to build on the toolchain pipelines.
1. Add more GitHub integrations for any extra [issue](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-incident-issues){: external}, [inventory](/docs/devsecops?topic=devsecops-cd-devsecops-inventory), and [evidence](/docs/devsecops?topic=devsecops-devsecops-evidence) repositories that can be configured for certain applications.


   * Refer to the documentation pages and best practices to understand if you need these extra repositories for the applications.

   * One inventory and issues repository should be enough. However, you might want to have a different evidence repository for each application to avoid the complexity of the repository structure. And a separate repo helps you to know which application you're looking at.
   * The **Application** repos can also serve as its own issues repo. This is applicable only when the GitHub issues option is enabled on the tool integration.
   * The **Inventory** repo must be enough as a single consolidated repository for recording your build records because the records are already divided by the `app-name`, so long as that parameter is different nothing is going to get lost or confused. Though you might want to consult the [inventory documentation](/docs/devsecops?topic=devsecops-cd-devsecops-inventory) for ideas on how to handle this repo because its main function is to help support deployments in the continuous deployment pipeline.
   {: tips}

1. Set up an {{site.data.keyword.cos_full}} bucket.

   {{site.data.keyword.cos_full_notm}} is the preferred evidence locker method for the pipeline evidence artifacts and is required for audit compliance. Use the same {{site.data.keyword.cos_short}} bucket for each application that you bring into the CI toolchain. Also, reuse the same {{site.data.keyword.cos_short}} bucket when you are integrating your CI toolchain with CD or CC toolchain.

1. Optional: Extra HashiCorp Vault integrations.

   Because the HashiCorp Vault tool integration supports only one path, you might need more of them depending on how your HashiCorp Vault secrets are configured. Different applications might need different or other credentials from each other.

## CI and PR pipeline customization
{: #cr-pr-pipeline-changes}

Customize your CI and PR pipeline by configuring them using the following steps:


1. Create a Git trigger for each application.

   * Copy the  existing triggers to save some tedium when you create triggers.
   * Make sure each of the triggers is pointing to the required GitHub repository and branch.
   * Make sure that the following trigger properties are set:
      * `app-name` (text): App names must be unique across the different applications. This is because the app name is used in the inventory, DevOps insights, and others as a unique identifier when placed and recorded with artifacts.
      * `cos-bucket-name` (text): The name of the {{site.data.keyword.cos_short}} bucket the applications evidence is placed in.
   {: note}

1. Environment property changes for each application.

   * This is a list of the possible environment properties that need to be changed to a different value for each of the applications. This can be accomplished by using the trigger properties on the pipeline triggers; which overrides the environmental properties with your own chosen values.
      * `app-name` (text): App names always need to be unique across the different applications because the app name is used in the inventory, DevOps insights, and so on, as a unique identifier when placing and recording artifacts.
      * `cos-bucket-name` (text): The name of the {{site.data.keyword.cos_short}} bucket the applications evidence is placed in.
      * `repository` (text): This parameter controls what application repo gets cloned to the pipeline and serves as the target for the CI and PR pipelines for the various compliance and security tasks.
      * Optional: `evidence-repo` (text): URL of the repo that serves as an evidence locker for the application.
      * Optional: `incident-repo` (text): URL of the repo that serves as the issues repo for the application.
      * Optional: `inventory-repo` (text): URL of the repo that serves as the inventory repo for the application.
   {: important}

1. `optional step` Set up manual triggers for each of the applications.
   * You technically need only one manual trigger because its properties can be edited at call time, but it might be useful for teams to have pre-composed manual triggers to save them some steps in writing all the possible small differences between the apps.
1. `optional step` Create Timer triggers for your applications.

   * It is good practice to frequently rebuild and revalidate an application, so you can keep on top of any compliance and vulnerability issues, as well as any build issues.
   * Configure the trigger properties the same way you configured the manual trigger because the timer trigger is just a manual trigger on a timer.
   * Stagger your cron jobs from each other, otherwise you might overload the worker cluster and your jobs might suffer increased build times or lag between stages.
   {: note}

For information about the other properties that can be set on the pipeline triggers or within the environment properties, see the [pipeline parameter catalog](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm).
{: note}

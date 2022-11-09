---

copyright:
  years: 2021, 2022
lastupdated: "2022-11-09"

keywords: DevSecOps, CI toolchain with multiple apps, multiple apps toolchain

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Configuring multiple apps on one CI toolchain
{: #cd-devsecops-multiple-apps}

When you run service offerings through Continuous Integration (CI), consider consolidating all of your offering's micro-services or components into a single common Toolchain, rather than having to manage multiple toolchains for each repo that you own.
{: shortdesc}

Configuring the [CI Pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-ci-pipeline) and [pull request (PR) pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-pr-pipeline) to work for multiple applications' repositories is simple. Use the following steps and changes to enable multiple applications.

## Toolchain changes
{: #multiple-apps-toolchain-changes}

Follow these steps:

1. Add a GitHub Tool integration for each application you want to build on the toolchain pipelines.
1. Add more GitHub integrations for any extra [issue](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-incident-issues){: external}, [inventory](/docs/devsecops?topic=devsecops-cd-devsecops-inventory), and [evidence](/docs/devsecops?topic=devsecops-devsecops-evidence) repositories that can be configured for certain applications.
   * Refer to the documentation pages and best practices to understand if you need these extra repositories for the applications.
   * One inventory and issues repository should be enough, but you might want to have a different evidence repository for each application due to how "inhumanly" readable the repository structure is. And a separate repo helps you to know which application you're looking at.
   * The Application repos can also serve as its own issues repo. That is, if the GitHub issues option is enabled on the tool integration.
   * The Inventory repo should enough as a single consolidated repository for recording your build records, because the records are already divided by the `app-name`, so long as that parameter is different nothing is going to get lost or confused. Though you might want to consult the [inventory documentation](/docs/devsecops?topic=devsecops-cd-devsecops-inventory) for ideas on how to handle this repo, since its main function is to help support deployments in the continuous deployment pipeline.
1. Set up {{site.data.keyword.cos_full}} buckets for each application
   * {{site.data.keyword.cos_short}} is the preferred evidence locker method for the pipeline evidence artifacts and is required for audit compliance. But, it is unfriendly to human readers, because all the artifacts are placed under one directory in the bucket with names like `ci-<toolchain_id>-<artifact_name>`, which are far too long for the user interface to render. For this reason, use one bucket per application to reduce the level of confusion.
1. Optional: Extra HashiCorp Vault integrations. Since the HashiCorp Vault tool integration supports only one path, you might need more of them depending on how your HashiCorp Vault secrets are configured, since different applications might need different or other credentials from each other.

## CI and PR pipeline changes
{: #cr-pr-pipeline-changes}

1. Create a Git trigger for each application.
   * The good news is that you can copy existing triggers, which can save you some tedium of creating triggers.
   * Make sure each of the triggers is pointing to the wanted GitHub repository and branch.
   * Make sure the following trigger properties are set:
      * `app-name` (text): App names always need to be unique across the different applications, since the app name is used in the inventory, DevOps insights, and so on, as a unique identifier when placing and recording artifacts.
      * `cos-bucket-name` (text): The name of the {{site.data.keyword.cos_short}} bucket the applications evidence will be placed in.
1. Environment properties changes for each app.
   * This is a list of the possible environment properties that need to be changed to a different value for each of the applications. This can be accomplished by using the trigger properties on the pipeline triggers; which allows you to override the environmental properties with your own chosen values.
      * `app-name` (text): App names always need to be unique across the different applications, since the app name is used in the inventory, DevOps insights, and so on, as a unique identifier when placing and recording artifacts.
      * `cos-bucket-name` (text): The name of the {{site.data.keyword.cos_short}} bucket the applications evidence will be placed in.
      * `repository` (text): This parameter controls what application repo gets cloned to the pipeline and serves as the target for the CI and PR pipelines for the various compliance and security tasks.
      * Optional: `evidence-repo` (text): URL of the repo that serves as an evidence locker for the application.
      * Optional: `incident-repo` (text): URL of the repo that serves as the issues repo for the application.
      * Optional: `inventory-repo` (text): URL of the repo that serves as the inventory repo for the application.
1. Optional: Set up manual triggers for each of the applications.
   * You technically need only one manual trigger, since its properties can be edited at call time, but it might be useful for teams to have pre-composed manual triggers to save them some steps in writing all the possible small differences between the apps.
1. Optional: Create timer Triggers for your applications.
   * Its good practice to often rebuild and revalidate an application, so you can keep on top of any compliance and vulnerability issues, as well as any build issues.
   * Make sure to configure the trigger properties the same way you configured the manual trigger, since the timer trigger is just a manual trigger on a timer.
   * Make sure you stagger your cron jobs from each other, otherwise you might overload the worker cluster and your jobs might suffer increased builds times or lag between stages.

For information on the other properties that can be set on the pipeline triggers or within the environment properties, see the [pipeline parameter catalog](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm).
{: note}

## Best practices for multiple applications
{: #best-practices-multiple-apps}

Review the following best practices for multiple applications:

* It is best to name the triggers by the application name and then its purpose, so people can easily filter on them in the dashboard. For example, `hello-world Git <branch> Trigger`.

* Seek to maximize the usage of the trigger properties in the manual triggers to reduce the need to insert input values. It also has the helpful effect of moving those values to the top of the properties list, so there's no need to search for the properties that need to change.

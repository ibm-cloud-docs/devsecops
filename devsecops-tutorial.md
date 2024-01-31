---

copyright: 
  years: 2022, 2024
lastupdated: "2024-01-17"

keywords: tekton, pipeline, toolchain, CD, CI, CC, automate, automation, continuous delivery, continuous integration, continuous compliance, devsecops tutorial, devsecops, DevOps, shift-left, shift left, secure DevOps, IBM Cloud, satellite, custom target, multiple clusters


subcollection: devsecops

content-type: tutorial
services: containers, ContinuousDelivery
account-plan: paid
completion-time: 1h

---

{{site.data.keyword.attribute-definition-list}}

# Part 1: Set up prerequisites
{: #tutorial-cd-devsecops}
{: toc-content-type="tutorial"}
{: toc-services="containers, ContinuousDelivery"}
{: toc-completion-time="1h"}

This tutorial is part 1 of a 4-part tutorial series where you learn {{site.data.keyword.cloud}} DevSecOps best practices by using a complete reference implementation that is available as a service and powered by {{site.data.keyword.contdelivery_full}}. In part 1 of this tutorial series, you review some background information and set up prerequisites.
{: shortdesc}

## Before you begin
{: #devsecops-tutorial-prereqs}

See the following documentation to get more information about DevSecOps and its implementation in {{site.data.keyword.contdelivery_short}}:

* [What is DevSecOps](https://www.ibm.com/cloud/learn/devsecops)
* [DevSecOps architecture](/docs/devsecops?topic=devsecops-cd-devsecops-arch)
* [DevSecOps with {{site.data.keyword.contdelivery_short}}](/docs/devsecops?topic=devsecops-devsecops_intro)
* [Understanding DevSecOps pipelines](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines)

## Set up an {{site.data.keyword.cloud_notm}} account
{: #tutorial-part1-account}
{: step}

Set up an [{{site.data.keyword.cloud_notm}} account](/registration){: external}. Depending on your {{site.data.keyword.cloud_notm}} account type, access to certain resources might be limited. Depending on your account plan limits, certain capabilities that are required by some DevSecOps toolchains might not be available. For more information, see [Setting up your {{site.data.keyword.cloud_notm}} account](/docs/account?topic=account-account-getting-started) and [Upgrading your account](/docs/account?topic=account-upgrading-account).

## Store secrets values in a secrets management vault
{: #tutorial-part1-secrets}
{: step}

Ensure that all of the secret values that you need are stored in a secrets management vault. [Managing {{site.data.keyword.cloud_notm}} secrets](/docs/secrets-manager?topic=secrets-manager-manage-secrets-ibm-cloud) can help you to choose from various secrets management and data protection offerings. If you don't already have an instance of the secrets management vault provider of your choice, create one. For information about {{site.data.keyword.secrets-manager_full}}, see [Getting started with {{site.data.keyword.secrets-manager_short}}](/docs/secrets-manager?topic=secrets-manager-getting-started).

## Create a Kubernetes cluster
{: #tutorial-part1-cluster}
{: step}

Create a [Kubernetes cluster](/kubernetes/catalog/cluster/create){: external}. The cluster might take some time to provision. As the cluster is created, it progresses through these stages: Deploying, Pending, and Ready. [Learn more.](/docs/containers?topic=containers-clusters).

## Create a {{site.data.keyword.registryshort_notm}} namespace
{: #tutorial-part1-namespace}
{: step}

Create an [{{site.data.keyword.registrylong}} namespace](/registry/namespaces){: external}. {{site.data.keyword.registrylong_notm}} provides a multi-tenant private image registry that you can use to store and share your container images with users in your {{site.data.keyword.cloud_notm}} account. Select the location for your namespace, and click **Create**. [Learn more.](/docs/Registry?topic=Registry-getting-started)

## Complete optional steps
{: #tutorial-part1-optional}
{: step}

Complete any or all of the following optional steps. If you don't complete these steps now, you can complete them in the next tutorial.

1. Create an [image signing key](/docs/devsecops?topic=devsecops-devsecops-image-signing) with the proper encoding to sign your application docker images.
1. Create an [{{site.data.keyword.cloud_notm}} API key](https://cloud.ibm.com/iam/apikeys){: external}. Save the API key value by either copying, downloading it or adding it to your vault. Alternatively, you can create the API key during the template-guided setup process.
1. Validate that the [recommended IAM permissions](/docs/devsecops?topic=devsecops-iam-permissions) are assigned to corresponding integrations.
1. [Install the {{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cloud-cli-getting-started).
1. Create an [{{site.data.keyword.cos_full_notm}} instance and bucket](/docs/devsecops?topic=devsecops-cd-devsecops-cos-config). [Learn more.](/docs/cloud-object-storage?topic=cloud-object-storage-about-cloud-object-storage)
1. Create an [{{site.data.keyword.satellitelong_notm}} cluster group](/docs/satellite?topic=satellite-setup-clusters-satconfig) if you want to deploy your application to multiple clusters that are grouped as a {{site.data.keyword.satelliteshort}} cluster group by using {{site.data.keyword.satelliteshort}} Config. [Learn more.](/docs/satellite?topic=satellite-cluster-config)

If you want to automatically set up the DevSecOps infrastructure for your toolchains by using an [{{site.data.keyword.bplong}} workspace](/docs/schematics?topic=schematics-about-schematics) and a Terraform-based quick start template, skip the previous steps and go to [Set up your DevSecOps infrastructure and CI toolchain for deploying a secure app](/docs/apps?topic=apps-tutorial-apps-devsecops). After the prerequisites and CI toolchain are successfully configured, proceed to the [Continuous Deployment (CD) toolchain introduction](#devsecops-cd-toolchain-intro).
{: tip}

## Related content
{: #tutorial-part1-related}

* [Getting started with clusters](/docs/containers?topic=containers-getting-started)
* [Getting started with toolchains](https://cloud.ibm.com/devops/getting-started){: external}
* [Getting started with {{site.data.keyword.contdelivery_short}}](/docs/ContinuousDelivery?topic=ContinuousDelivery-getting-started)
* [Getting started with IBM Wazi as a Service](https://www.ibm.com/docs/en/wazi-aas/1.0.0?topic=overview){: external}

## Looking for help?
{: #tutorial-part1-help}

Get help fast directly from the {{site.data.keyword.cloud_notm}} {{site.data.keyword.contdelivery_short}} development teams by joining us on [Slack](https://ic-devops-slack-invite.us-south.devops.cloud.ibm.com/){: external}.

For more support options, see [Getting help and support for {{site.data.keyword.contdelivery_short}}](/docs/ContinuousDelivery?topic=ContinuousDelivery-gettinghelp).

## Next steps
{: #tutorial-part1-next}

When you have your prerequisites in place, continue to the next tutorial in the series:

[Part 2: Set up a Continuous Integration (CI) toolchain](/docs/devsecops?topic=devsecops-tutorial-ci-toolchain).

This tutorial uses an example Node.js app, but you can also deploy other language-based apps.
{: note}

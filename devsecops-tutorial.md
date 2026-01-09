---

copyright:
  years: 2023, 2024
lastupdated: "2024-03-28"

keywords: tekton, pipeline, toolchain, cd, ci, cc, automate, automation, continuous delivery, continuous integration, continuous compliance, devsecops tutorial, devsecops, devops, secure devops, satellite, custom target, multiple clusters


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

This tutorial walks you through how to set up prerequisites and learn more about {{site.data.keyword.cloud}} DevSecOps by using a complete reference implementation that is available as a service and powered by {{site.data.keyword.contdelivery_full}}.
{: shortdesc}

This tutorial uses an example of `Node.js` app, but you can also deploy other language-based apps.
{: note}

## Before you begin
{: #devsecops-tutorial-prereqs}

* [DevSecOps reference implementation](/docs/devsecops?topic=devsecops-cd-devsecops-arch)
* [DevSecOps with {{site.data.keyword.contdelivery_short}}](/docs/devsecops?topic=devsecops-devsecops_intro)
* [Understanding DevSecOps pipelines](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines)

For more information, see [DevSecOps](https://www.ibm.com/think/topics/devsecops){: external} architecture and its implementation in {{site.data.keyword.contdelivery_short}}.

## Set up an {{site.data.keyword.cloud_notm}} account
{: #tutorial-account}
{: step}

To get started, you must set up an [{{site.data.keyword.cloud}} account](/registration){: external}. Depending on your {{site.data.keyword.cloud_notm}} account plan, access to certain resources by DevSecOps toolchains might be limited. For more information, see [Setting up your {{site.data.keyword.cloud_notm}} account](/docs/account?topic=account-account-getting-started), and [Upgrading your account](/docs/account?topic=account-upgrading-account).

## Store secrets values
{: #tutorial-secrets}
{: step}

Storing secrets values is [essential](/docs/secrets-manager?topic=secrets-manager-best-practices-using) to the DevSecOps process. As a user, storing an instance is required to manage and protect systems access that are inside or outside of {{site.data.keyword.cloud_notm}}. Choosing one of the secret management options helps you to protect your sensitive data and centralize your secrets. [Managing {{site.data.keyword.cloud_notm}} secrets](/docs/secrets-manager?topic=secrets-manager-use-case-kubernetes-secrets) can help you choose from various secrets management and data protection offerings. If you don't already have an instance of the secrets management vault provider of your choice, create one. For more information, see [Getting started with {{site.data.keyword.secrets-manager_short}}](/docs/secrets-manager?topic=secrets-manager-getting-started).

## Create a Kubernetes cluster
{: #tutorial-cluster}
{: step}

Create a [Kubernetes cluster](/containers/cluster-management/catalog/create){: external}. The cluster takes time to provision. For more information about provisioning clusters, see [Preparing your account to create clusters](/docs/containers?topic=containers-clusters).

## Create a {{site.data.keyword.registryshort_notm}} namespace
{: #tutorial-namespace}
{: step}

Create an [{{site.data.keyword.registrylong}} namespace](/registry/namespaces){: external}. The {{site.data.keyword.registrylong_notm}} provides a multi-tenant private artifact registry that you can use to store and share your container artifacts with users in your {{site.data.keyword.cloud_notm}} account. For more information about {{site.data.keyword.registrylong_notm}}, see [Getting started with {{site.data.keyword.registryshort_notm}}](/docs/Registry?topic=Registry-getting-started).

## Apply the steps
{: #tutorial-other}
{: step}

If applicable to your use case, you can complete any or all the following steps.

- Create an [artifact key](/docs/devsecops?topic=devsecops-devsecops-image-signing) with the right encoding to sign in to your applications.
- Create and save an [{{site.data.keyword.cloud_notm}} API key](https://cloud.ibm.com/iam/apikeys){: external}. Alternatively, you can create the API key during the template-guided setup process.
- Validate that the [IAM permissions](/docs/devsecops?topic=devsecops-iam-permissions) are assigned to the corresponding integrations.
- Install the [{{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cli-getting-started).
- Create an [{{site.data.keyword.cos_full}} instance and bucket](/docs/devsecops?topic=devsecops-cd-devsecops-cos-config). For more information, see [What is {{site.data.keyword.cos_full_notm}}](/docs/cloud-object-storage?topic=cloud-object-storage-about-cloud-object-storage)?
- Create an [{{site.data.keyword.satellitelong_notm}} cluster group](/docs/satellite?topic=satellite-setup-clusters-satconfig) to deploy your application into {{site.data.keyword.satellitelong_notm}} cluster group. For more information, see [Understanding {{site.data.keyword.satelliteshort}} config](/docs/satellite?topic=satellite-cluster-config).

You can automatically set up the DevSecOps infrastructure for your toolchains by using an [{{site.data.keyword.bplong}} workspace](/docs/schematics?topic=schematics-learn-about-schematics) and a Terraform template by skipping the steps and going directly to [DevSecOps infrastructure and Continuous Integration (CI) toolchain for deploying a secure app set up](/docs/devsecops?topic=devsecops-tutorial-apps-devsecops). After the prerequisites and CI toolchain are successfully configured, continue to the [Continuous Deployment (CD) toolchain introduction](/docs/ContinuousDelivery?topic=ContinuousDelivery-getting-started&interface=ui).
{: tip}

## Next steps
{: #tutorial-next}

After you complete the prerequisite steps and are ready to get started with toolchain integration, continue to the next tutorial in the series: [Setting up a Continuous Integration toolchain](/docs/devsecops?topic=devsecops-tutorial-ci-toolchain).

For additional information about related topics, review the following documentation:

* [Getting started with clusters](/docs/containers?topic=containers-getting-started)
* [Getting started with toolchains](https://cloud.ibm.com/devops/getting-started){: external}
* [Getting started with {{site.data.keyword.contdelivery_short}}](/docs/ContinuousDelivery?topic=ContinuousDelivery-getting-started)
* [Getting started with {{site.data.keyword.waziaas_full_notm}}](https://www.ibm.com/docs/en/wazi-aas/1.1.0?topic=overview){: external}

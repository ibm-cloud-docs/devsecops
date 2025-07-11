---

copyright:
  years: 2023, 2024
lastupdated: "2024-03-20"

keywords: tekton, pipeline, toolchain, cd, ci, cc, automate, automation, continuous delivery, continuous integration, continuous compliance, devsecops tutorial, devsecops, devops, shift-left, shift left, secure devops, infrastructure-as-code, iac, terraform, schematics

subcollection: devsecops

content-type: tutorial
services: schematics, ContinuousDelivery
account-plan: paid
completion-time: 1h

---

{{site.data.keyword.attribute-definition-list}}

# Setting up prerequisites
{: #devsecops-tutorial-iac}
{: toc-content-type="tutorial"}
{: toc-services="schematics, ContinuousDelivery"}
{: toc-completion-time="1h"}

With this tutorial, see how to set up prerequisites to develop an Infrastructure as Code (IaC) (Terraform configuration) by using {{site.data.keyword.cloud}} DevSecOps. Also how to deploy, secure, and manage a complete reference implementation that is available as a service and powered by {{site.data.keyword.contdelivery_full}}.
{: shortdesc}

## Before you begin
{: #devsecops-tutorial-iac-prereqs}

* About [DevSecOps](https://www.ibm.com/think/topics/devsecops){: external}.
* [DevSecOps architecture](/docs/devsecops?topic=devsecops-cd-devsecops-arch).
* [DevSecOps with {{site.data.keyword.contdelivery_short}}](/docs/devsecops?topic=devsecops-devsecops_intro).
* [Understanding DevSecOps pipelines](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines).

## Setting up an {{site.data.keyword.cloud_notm}} account
{: #devsecops-tutorial-iac-account}
{: step}

Set up an [{{site.data.keyword.cloud_notm}} account](/registration){: external}. Depending on your {{site.data.keyword.cloud_notm}} account type, access to certain resources, and capabilities of DevSecOps toolchains might be limited. For more information, see [Setting up your {{site.data.keyword.cloud_notm}} account](/docs/account?topic=account-account-getting-started) and [Upgrading your account](/docs/account?topic=account-upgrading-account).

## Creating a resource group
{: #devsecops-tutorial-iac-resource-group}
{: step}

Resource groups organize your account resources in customizable groupings so that you can assign users access to multiple resources at a time. Every toolchain is associated with a resource group. By default, the toolchain is created in the `Default` resource group. For more information, see [Creating a resource group](/docs/account?topic=account-rgs&interface=ui#create_rgs).

## Storing secrets values
{: #devsecops-tutorial-iac-secrets}
{: step}

Check the secret values that you need are stored in a secrets management vault. [Managing {{site.data.keyword.cloud_notm}} secrets](/docs/secrets-manager?topic=secrets-manager-use-case-kubernetes-secrets) helps you to choose from various secrets management, and data protection offerings. Create an instance of the secrets management vault, if you don't have one. For more information about {{site.data.keyword.secrets-manager_full}}, see [Getting started with {{site.data.keyword.secrets-manager_short}}](/docs/secrets-manager?topic=secrets-manager-getting-started).

## Apply the steps
{: #devsecops-tutorial-iac-optional}
{: step}

You can complete any or all the important steps.

- Create an [artifact key](/docs/devsecops?topic=devsecops-devsecops-image-signing) by encoding to sign in to IaC.
- Create an [{{site.data.keyword.cloud_notm}} API key](https://cloud.ibm.com/iam/apikeys){: external}. Save the API key value by either copying, downloading, or adding to your vault. Alternatively you can create the API key during the template-guided setup process.
- Validate that the [IAM permissions](/docs/devsecops?topic=devsecops-iam-permissions) are assigned to the corresponding integrations.
- [Install the {{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cli-install-ibmcloud-cli).
- Create an [{{site.data.keyword.cos_full}} instance and bucket](/docs/devsecops?topic=devsecops-cd-devsecops-cos-config). For more information, see [What is {{site.data.keyword.cloud_notm}} {{site.data.keyword.objectstorageshort}}](/docs/cloud-object-storage?topic=cloud-object-storage-about-cloud-object-storage)?

## Related content
{: #tutorial-iac-part1-relatedcontent}

You can continue to learn the following topics.

* [Getting started with {{site.data.keyword.terraform-provider_full}}](/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started)
* [Getting started with toolchains](https://cloud.ibm.com/devops/getting-started){: external}
* [Getting started with {{site.data.keyword.contdelivery_short}}](/docs/ContinuousDelivery?topic=ContinuousDelivery-help-and-support)

For more information, see [help and support](/docs/devsecops?topic=devsecops-gettinghelp).
{: note}

## Next steps
{: #tutorial-iac-part1-next}

Now, you set the prerequisites. Continue to the next tutorial in the series [Setting up a CI toolchain for IaC](/docs/devsecops-working?topic=devsecops-working-devsecops-tutorial-iac-ci). 

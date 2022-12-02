---

copyright: 
  years: 2022, 2022
lastupdated: "2022-12-02"

keywords: tekton, pipeline, toolchain, CD, CI, CC, automate, automation, continuous delivery, continuous integration, continuous compliance, devsecops tutorial, devsecops, DevOps, shift-left, shift left, secure DevOps, IBM Cloud, infrastructure-as-code, terraform, schematics

subcollection: devsecops

content-type: tutorial
services: schematics, ContinuousDelivery
account-plan: paid
completion-time: 1h

---

{{site.data.keyword.attribute-definition-list}}

# Part 1: Set up prerequisites
{: #devsecops-tutorial-iac}
{: toc-content-type="tutorial"}
{: toc-services="schematics, ContinuousDelivery"}
{: toc-completion-time="1h"}

This tutorial is part 1 of a 3-part tutorial series where you learn {{site.data.keyword.cloud}} DevSecOps best practices to develop Infrastructure as Code (IaC) (Terraform configuration) and deploy it by using a complete reference implementation that is available as a service and powered by {{site.data.keyword.contdelivery_full}}. In part 1 of this tutorial series, you review some background information and set up prerequisites.
{: shortdesc}

## Before you begin
{: #devsecops-tutorial-iac-prereqs}

See the following documentation to get more information about DevSecOps and its implementation in {{site.data.keyword.contdelivery_full}}:

* [What is DevSecOps](https://www.ibm.com/cloud/learn/devsecops?){: external}
* [DevSecOps architecture](/docs/devsecops?topic=devsecops-cd-devsecops-arch)
* [DevSecOps with {{site.data.keyword.contdelivery_short}}](/docs/devsecops?topic=devsecops-devsecops_intro)
* [Understanding DevSecOps pipelines](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines)

## Set up an {{site.data.keyword.cloud_notm}} account
{: #devsecops-tutorial-iac-account}
{: step}

Set up an [{{site.data.keyword.cloud_notm}} account](/registration){: external}. Depending on your {{site.data.keyword.cloud_notm}} account type, access to certain resources might be limited. Depending on your account plan limits, certain capabilities that are required by some DevSecOps toolchains might not be available. For more information, see [Setting up your {{site.data.keyword.cloud_notm}} account](/docs/account?topic=account-account-getting-started) and [Upgrading your account](/docs/account?topic=account-upgrading-account).

## Create resource group
{: #devsecops-tutorial-iac-resource-group}
{: step}

Resource groups organize your account resources in customizable groupings so that you can assign users access to multiple resources at a time. Every toolchain is associated with a resource group. By default, the toolchain is created in the `Default` resource group. For more information, see [Creating a resource group](/docs/account?topic=account-rgs&interface=ui#create_rgs).

## Store secrets values in a secrets management vault
{: #devsecops-tutorial-iac-secrets}
{: step}

Ensure that all of the secret values that you need are stored in a secrets management vault. [Managing {{site.data.keyword.cloud_notm}} secrets](/docs/secrets-manager?topic=secrets-manager-manage-secrets-ibm-cloud) can help you to choose from various secrets management and data protection offerings. If you don't already have an instance of the secrets management vault provider of your choice, create one. For more information, see [Getting started with {{site.data.keyword.secrets-manager_short}}](/docs/secrets-manager?topic=secrets-manager-getting-started).

## Complete the optional steps
{: #devsecops-tutorial-iac-optional}
{: step}

Complete any or all of the following optional steps:

1. Create a [code signing key](/docs/devsecops?topic=devsecops-devsecops-image-signing) with the proper encoding to sign your infrastructure code.
1. Create an [{{site.data.keyword.cloud_notm}} API key](https://cloud.ibm.com/iam/apikeys){: external}. Save the API key value by either copying, downloading it, or adding it to your vault. Alternatively, you can create the API key during the template-guided setup process.
1. Validate that the [recommended IAM permissions](/docs/devsecops?topic=devsecops-iam-permissions) are assigned to corresponding integrations.
1. [Install the {{site.data.keyword.cloud_notm}} CLI](/docs/cli?topic=cloud-cli-getting-started).
1. Create an [{{site.data.keyword.cos_full_notm}} instance and bucket](/docs/devsecops?topic=devsecops-cd-devsecops-cos-config). For more information, see [What is Cloud Object Storage?](/docs/cloud-object-storage?topic=cloud-object-storage-about-cloud-object-storage)

## Related content
{: #devsecops-tutorial-iac-part1-related}

* [Getting started with Terraform on IBM Cloud](https://cloud.ibm.com/docs/ibm-cloud-provider-for-terraform?topic=ibm-cloud-provider-for-terraform-getting-started){: external}
* [Getting started with toolchains](https://cloud.ibm.com/devops/getting-started){: external}
* [Getting started with {{site.data.keyword.contdelivery_short}}](/docs/ContinuousDelivery?topic=ContinuousDelivery-getting-started)

## Looking for help?
{: #devsecops-tutorial-iac-part1-help}

Get help fast directly from the {{site.data.keyword.cloud_notm}} {{site.data.keyword.contdelivery_short}} development teams by joining us on [Slack](https://ic-devops-slack-invite.us-south.devops.cloud.ibm.com/){: external}.

For more support options, see [Getting help and support for {{site.data.keyword.contdelivery_short}}](/docs/ContinuousDelivery?topic=ContinuousDelivery-gettinghelp).

## Next steps
{: #devsecops-tutorial-iac-part1-next}

When you have your prerequisites in place, continue to the next tutorial in the series:

[Part 2: Set up a CI toolchain for Infrastructure as Code](/docs/devsecops?topic=devsecops-devsecops-tutorial-iac-ci).

This tutorial uses an example Terraform configuration to create a KeyProtect service instance and a root key.
{: note}

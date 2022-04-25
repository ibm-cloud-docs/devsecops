---

copyright:
  years: 2022
lastupdated: "2022-04-25"

keywords: DevSecOps, inventory model, inventory, IBM Cloud

subcollection: devsecops

---

{:shortdesc: .shortdesc}
{:table: .aria-labeledby="caption"}
{:external: target="_blank" .external}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:download: .download}
{:help: data-hd-content-type='help'}
{:support: data-reuse='support'}

# Forced redeployment of an application
{: #forced-redeployment}

Sometimes, a running application in an environment crashes or behaves abnormally due to changes in the infrastructure configuration and requires a redeployment. In such cases, you can use the Continuous Delivery (CD) Pipeline to deploy the existing state of the inventory to the target environment by setting a new environment variable for the CD Pipeline.
{: shortdesc}

Set the `force-redeploy` environment variable with the value as `true` while the **Manual CD** pipeline run is triggered.

During redeployment, the branch in your inventory repository that corresponds to the environment (for example prod, stage) does not contain any new items, and the `<target-environment>_latest` tag points to the current commit. The presence of the `force-redeploy` flag tells the CD Pipeline to ignore checking any new changes and proceed with the current stage of the inventory.

The summary of steps that are followed by the CD Pipeline for forced redeployment include:

1. The CD Pipeline starts and tags the current commit with the pipeline run ID.
2. The pipeline picks up the content of the corresponding environment branch from that tag.
3. The pipeline calculates the deployment delta between the current commit and the contents of the `<target-environment>_latest` tag. Because the content is identical in this case, the delta is empty. The setting `force-redeploy` makes it possible for the pipeline to continue.
4. A successful deployment concludes by attaching the `<target-environment>_latest` tag to the commit that you work with. Because the content is identical, no changes are present.

---

copyright:
  years: 2022
lastupdated: "2024-05-28"

keywords: DevSecOps, inventory model, inventory, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

#  Redeploying  an application manually
{: #manual-redeployment}

Sometimes, an application running in an environment can crash or behave strangely due to changes in the infrastructure configuration. This can be resolved by redeployment. Duringf redeployment, use the continuous deployment (CD) pipeline to deploy the existing state of the inventory to the target environment by setting a new environment variable for the continuous deployment pipeline.
{: shortdesc}

Set the `force-redeploy` environment variable with the value as `true` while the **Manual CD** pipeline run is triggered.
{: note}


When you redeploy, the branch in your inventory repository that corresponds to the environment (for example,  production and staging) does not contain any new items, and the `<target-environment>_latest` tag points to the current commit. The presence of the `force-redeploy` flag tells the CD Pipeline to ignore checking any new changes and proceed with the current stage of the inventory.

Here's a quick summary of the procedure followed by the CD Pipeline to redeployment manuallly:

1. The CD Pipeline starts and tags the current commit with the pipeline run ID.
2. The pipeline picks up the content of the corresponding environment branch from that tag.
3. The pipeline calculates the deployment delta between the current commit and the contents of the `<target-environment>_latest` tag. Because the content is identical in this case, the delta is empty. The setting `force-redeploy` makes it possible for the pipeline to continue.
4. A successful deployment concludes by attaching the `<target-environment>_latest` tag to the commit that you work with. Because the content is identical, no changes are present.

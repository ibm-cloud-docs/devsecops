<evidence>---

copyright:
  years:  2020, 2023
lastupdated: "2023-10-09"

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Gathering evidence for inventory
{: #evidence-ci-inventory}

When you are going through an audit, you must provide evidence that your development and production environments are meeting the criteria in the regulatory standards that you claim compliance with.

## Before you begin
{: #before-evidence-cr}

Before you can start gathering evidence, be sure that you have the following prerequisites:

* A configured pipeline
* The Viewer role or higher on the Continuous Delivery service. For more information, see [Managing access for toolchains in resource groups](/docs/ContinuousDelivery?topic=ContinuousDelivery-toolchains-iam-security).

## Viewing inventory commitment details

If your auditor requests proof of change logs, you can use the following steps to create a screen capture of your configuration.

1. In the {{site.data.keyword.cloud_notm}} console, click the **Menu** icon ![Menu icon](../../icons/icon_hamburger.svg) **> DevOps > Toolchains**, and select the toolchain for which you want to provide evidence.
2. In the **Delivery pipelines** section on the **Overview** tab, click `cd-pipeline`. A list of the previous pipeline runs is displayed.
3. Select `cd-pipelinerun` on the **Pipeline runs** tab. The details of the run open.
4. From here, select `deploy-release` and click the **run-stage** tab.
5. Search for "Inventory successfully updated" in the logs to view all the inventory commits.

The commit URL doesn't direct you to the inventory repository. {: note}

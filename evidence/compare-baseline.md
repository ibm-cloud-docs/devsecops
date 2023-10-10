<evidence>---

copyright:
  years:  2020, 2023
lastupdated: "2023-10-10"

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Gathering evidence for comparing two baseline configurations
{: #evidence-compare-baseline-config}

When you are going through an audit, you must provide evidence that your development and production environments are meeting the criteria in the regulatory standards that you claim compliance with. Use the following instructions for comparing two baseline confirgurations.

## Before you begin
{: #before-evidence-compare-baseline-config}

Before you can start gathering evidence, be sure that you have the following prerequisites:

* A configured pipeline
* The Viewer role or higher on the Continuous Delivery service. For more information, see [Managing access for toolchains in resource groups](/docs/ContinuousDelivery?topic=ContinuousDelivery-toolchains-iam-security).

## Compare baseline configuration details
{: #gather-evidence-compare-config}

1. Complete the steps to gather evidence for [viewing a baseline configutation](/docs/devsecops?topic=devsecops-evidence-baseline-config).
2. Go to the inventory repository. [Niraj to provide screencap]
3. Click **Release**.
4. Select the `prod_latest` release option.
5. Click **Compare**.
6. Make a selection to compare the `prod_latest` release against any other release that's listed.

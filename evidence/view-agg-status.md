<evidence>---

copyright:
  years:  2020, 2023
lastupdated: "2023-10-10"

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Gathering evidence for comparing two baseline configurations
{: #evidence-aggregated}

When you are going through an audit, you must provide evidence that your development and production environments are meeting the criteria in the regulatory standards that you claim compliance with. Use the following instructions for comparing two baseline configurations.

## Before you begin
{: #before-evidence-aggregated-summary}

Before you can start gathering evidence, be sure that you have the following prerequisites:

* A configured pipeline
* The Viewer role or higher on the Continuous Delivery service. For more information, see [Managing access for toolchains in resource groups](/docs/ContinuousDelivery?topic=ContinuousDelivery-toolchains-iam-security).

## Viewing an aggregated summary of evidence status
{: #view-evidence-status}

1. Add `opt-in-promotion-validation to 1` environment variable in CD tool-chain. For more information, see [How to opt-in promotion validation](/docs/devsecops?topic=devsecops-cd-devsecops-promotion-pipeline#cd-devsecops-promotion-validation-pipeline-opt-in).--> Topic states this is deprecated? -HL
2. Trigger a manual promotion pull request in the CD tool-chain.
3. Find the pull request url in the logs.
4. The pull request validation starts and takes about 10 minutes for the report to show.
5. A table appears with the detailed validation report that is generated in the pull request.

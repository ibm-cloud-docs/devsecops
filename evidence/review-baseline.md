<evidence>---

copyright:
  years:  2020, 2023
lastupdated: "2023-10-10"

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Gathering evidence of the review of your baseline configuration
{: #evidence-review-baseline-config}

When you are going through an audit, you must provide evidence that your development and production environments are meeting the criteria in the regulatory standards that you claim compliance with.

## Before you begin
{: #before-review-baseline-config}

Before you can start gathering evidence, be sure that you have the following prerequisites:

* A configured pipeline
* The Viewer role or higher on the Continuous Delivery service. For more information, see [Managing access for toolchains in resource groups](/docs/ContinuousDelivery?topic=ContinuousDelivery-toolchains-iam-security).

## Reviewing the baseline configuration details
{: #gather-evidence-review-baseline}

1. Open the change request that's associated with the baseline configuration. For information about navigating to a change request, see [Viewing change request details](/docs/devsecops?topic=devsecops-evidence-change-request).
2. Provide a screen capture of the change request that includes the deployment details.
3. Provide information from latest CC pipeline run that includes the contents re-scanned.
4. In addition, you can you can go to any CI/CD/CC pipeline-run and download the logs archive to the environment configuration. All configurations(including Definition/Environment Variables) can be seen as *.json format.

Logs are also stored persistently in the evidence locker according to expiry contracts. {: note}

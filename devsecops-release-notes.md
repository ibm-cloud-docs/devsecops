---

copyright:
  years: 2022
lastupdated: "2022-09-27"

keywords: IBM Cloud DevSecOps release notes, Cloud DevSecOps changes, Cloud DevSecOps updates

subcollection: devsecops
content-type: release-note

---

{{site.data.keyword.attribute-definition-list}}

# Release notes for DevSecOps
{: #release-notes}

Use the release notes to learn about the latest changes to {{site.data.keyword.cloud_notm}} DevSecOps.
{: shortdesc}

## 16 Sep 2022
{: #devsecops-sep1622}
{: release-note}

- Added the `retry_sleep` environment property for the number of Vulnerability Advisor (VA) scans to attempt for a given pipeline run.
- Improved log messages so the evidence status is clearer. For example: `Evaluated evidence status: <success|failure|pending>`.


## 11 Aug 2022
{: #devsecops-aug1122}
{: release-note}

- Use the native due date field in GitLab for managing issues.
- Added `custom-exempt-label` as an environment property that so pipelines are not marked as failed if they have the matching exempt label.
- Fixed Cloud Object Storage as an evidence locker for CC pipeline.

## 25 July 2022
{: #devsecops-jul2522}
{: release-note}

- Upload Continious Compliance evidence summary for Security and Compliance Center.

## 24 June 2022
{: #devsecops-jun2422}
{: release-note}

- Enabled DevOps Insights integration for the continuous compliance (CC) template. 

If you have an existing CC toolchain, you can set the [`doi-toolchain-id`](/docs/devsecops?topic=devsecops-tutorial-cc-toolchain#tutorial-cc-toolchain-insights) and `doi-environment` properties by linking an existing Insights tool integration from your DevSecOps continuous integration (CI) toolchain.
{: note}

- Added [tags](/docs/devops-insights-cli-plugin?topic=devops-insights-cli-plugin-CLI_devops-insights) to DevSecOps pipeline scripts to visualize Continuous Compliance scan results in the Insights user interface.
- Surfaced dynamic scan results in the [Insights quality dashboard](/docs/ContinuousDelivery?topic=ContinuousDelivery-devops-data-aggregation).
- Improved message logging for the pipeline evaluator task so that users can better understand what failed or succeeded for a pipeline run.

## 17 June 2022
{: #devsecops-jun1722}
{: release-note}

The deployment BOM that is attached in a generated Change Request was missing information that is related to artifacts in the inventory. This information is now included.

## 19 May 2022
{: #devsecops-may1922}
{: release-note}

- Notify users about incident issues that have approaching due dates and overdue due dates by using Slack after the Slack tool integration is enabled.
- Support for [cyclonedx](/docs/devsecops?topic=devsecops-generate-cyclonedx-sbom) format when generating Software Bill of Material by using Code Risk Analyzer in compliance pipelines.

## 13 May 2022
{: #devsecops-may1322}
{: release-note}

Added support to the Continuous Integration and Continuous Compliance pipelines for [incident issues](/docs/devsecops?topic=devsecops-incident-issues), such as vulnerabilities and CVEs, that are related to creating, storing, updating, and closing those issues. With this feature, you can set due dates for issues, add default assignees, and filter issues by using labels. 

## 24 March 2022
{: #devsecops-mar2422}
{: release-note}

### Introducing {{site.data.keyword.cloud_notm}} DevSecOps
{: #devsecops-intro}

DevSecOps is a methodology that integrates security practices with the software development and operations lifecycle. The goal of the merge is to prioritize the balance of development speed and security.

- New Continuous Compliance template that periodically scans your deployed application code.
- Support for Zap Dynamic scans that use async pipelines.
- Support for tracing the origin of default scripts that are running in the compliance pipelines.
- Incident issues support a "Due Date" field based on the issue severity.
- Format issues created by Continuous Compliance pipeline for better readability.
- Support for disabling static and dynamic scans that use optional environment properties.
- Updated {{site.data.keyword.cos_full_notm}} tool integration UI for easy setup.
- Support for querying environment tags from inventory repository while setting up CC and CD toolchain.
- New wizard support for customizing the welcome page.

{{site.data.keyword.DRA_full}} is not supported in the Continuous Compliance pipeline. Also, there is limited support for triggering dynamic scans for multiple apps out of the box.
{: note}

## 16 March 2022
{: #devsecops-mar1622}
{: release-note}

- Enhanced DevSecOps pipeline code to publish CRA results to {{site.data.keyword.DRA_short}} including BOM, vulnerability, and deployment records.
- Added support for the optional CRA plug-in parameter `--dockerfilepattern`.

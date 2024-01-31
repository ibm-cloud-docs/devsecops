---

copyright:
  years: 2022, 2023
lastupdated: "2024-01-24"

keywords: IBM Cloud DevSecOps release notes, Cloud DevSecOps changes, Cloud DevSecOps updates

subcollection: devsecops
content-type: release-note

---

{{site.data.keyword.attribute-definition-list}}

# Release notes for DevSecOps
{: #release-notes}

Use the release notes to learn about the latest changes to {{site.data.keyword.cloud_notm}} DevSecOps.
{: shortdesc}
## 24 January 2024
{: #devsecops-Jan2024}
{: release-note}

Switching from SNYK to Clair database
:   The Code Risk Analyzer tool now uses the Clair database to identify the vulnerabilities.

Removal of `pipeline.data`
:   `pipeline.data`, which is used to share environment variables across the pipeline, is no longer available.

Pre-prod evidence collection
:   According to the FedRAMP requirements, pre-prod change requests must be linked to the production change request and pre-prod evidence should be included in the production change of the request summary. To meet these requirements, we've developed a new feature to collect pre-prod evidence. For more information, see [Collect evidence summary](/docs/devsecops?topic=devsecops-cd-devsecops-cd-pipeline#cd-devsecops-pipeline-collect).

SLSA attestation
:   You can now attest images built in CI pipeline with SLSA level 3 attestation. For more information, see [Configuring collection of SLSA attestations for images](/docs/devsecops?topic=devsecops-cd-devsecops-slsa).

Collection evidences for multiple assets
:   You can now collect evidence for multiple assets. For more information, see [Multiple assets in collect-evidence](/docs/devsecops?topic=devsecops-devsecops-collect-evidence#multi-asset-evidence-collection).

Support for Security and Compliance Center push API
:   You can now push results to Security Control Center. For more information please see the [Security and Compliance Center integration docs](/docs/devsecops?topic=devsecops-cd-devsecops-scc-toolchains).


## 08 Dec 2023
{: #devsecops-Dec2023}
{: release-note}

- **Amber status for deployment with errors** - Deployment of manually-approved CR with red evidence now shows amber status.

- **Batch processing of evidence collection** - During a pipeline run, all the evidences are collected locally to the pipeline and proceed at the end of the pipeline run. This process results in performance improvement. For more information, see [Batched evidence collection](/docs/devsecops?topic=devsecops-devsecops-collect-evidence#batched-evidence-collection).

## 30 October 2023
{: #devsecops-oct2023}
{: release-note}

- **Isolated workarea for individual stages in a pipeline** - A new environment property $STAGE_WORKSPACE pointing to a stage specific location is available. Any data written to this location is accessible exclusively to the present stage and remains inaccessible to other stages.

- **Separate Stage to run detect-secret scans** - By running the detect secrets task alongside the compliance-checks, there is a chance of a secret leaking into the container registry or dev clusters. To avoid this situation, detect-secrets is now a separate stage and a pipeline will fail if there are any secrets detected. For more information, see [Configuring detect-secrets scans](/docs/devsecops?topic=devsecops-cd-devsecops-detect-secrets-scans).

- **Ability to skip stages in open-v10** - Users can skip pipeline stages by defining `skip: true` in `pipeline-config.yaml`

- **Ability to configure stages in specific order** - Users can specify a order by defining `runAfter: <task name>`. This will make the current task to wait till the previous task completes, even when both tasks are scheduled to run concurrently

- **Concurrent execution of DevSecOps Pipeline stages** - The devsecops pipelines have been restructured to run independent stages concurrently. This speeds up overall pipeline run considerably. For more information, see [Improve compliance pipeline performance using concurrent tasks](/docs/devsecops?topic=devsecops-devsecops-conc).

- **Validation of SBOM** - Validation of SBOM by default.

- **Addition of GoSec Tool within Static Scan Stage for CI Pipeline** - GoSec security scan has been integrated to the compliance checks stage for Go applications. For more information, see [Configuring GoSec scans](/docs/devsecops?topic=devsecops-devsecops-gosec).

- **Addition of peer-review stage** - Added a new peer review stage to verify whether the peer review has been performed on all the commits in integration branch. For more information, see [Peer review compliance](/docs/devsecops?topic=devsecops-cd-devsecops-peer-review).

- **Support for management of non image based assets** - Pipeline support for the development of generic assets that can be of any type. e.g tar, zip, etc.

- **Addition of SysDig Tool scanning within the Artifact Scan Stage for CI Pipeline** - Integrated Sysdig secure tool to scan container images. For more information, see [Configuring Sysdig Image scans](/docs/devsecops?topic=devsecops-cd-devsecops-sysdig-scans).

- **Customizable finish stage** - Modified the last stage of the pipeline to run always irrespective of pipeline status, and added ability to have a customizable step in the last stage for the use of housekeeping in case of a pipeline failure. For more information, see [Custom finish stages](/docs/devsecops?topic=devsecops-custom-scripts#devsecops-scripts-custom-finish)

- **Added new pipeline to perform inventory PR validation** - A new pipeline has been created to seperate out inventory PR validation in CD toolchain. For more information, see [How to opt-in into promotion validation?](/docs/devsecops?topic=devsecops-cd-devsecops-promotion-pipeline#cd-devsecops-promotion-validation-pipeline-opt-in)

- **Auto remediation of vulnerabilities using CRA in CC Pipeline** - Added ability to auto remediate vulnerabilities using CRA. For more information, see [Automatically remediating vulnerabilities](/docs/devsecops?topic=devsecops-cd-devsecops-cra-scans#cd-devsecops-auto-remediate)

- **Generation of SBOM in cyclonedx format and its validation** - Added ability to generate and merge SBOM using cyclonedx format and also the bom is validated using `sbom-utility` tool. For more information, see [Generating the software bill of materials (SBOM) in cyclonedx format](/docs/devsecops?topic=devsecops-generate-cyclonedx-sbom)

- **Evidence pruner** - A new tool has been developed to prune older evidences before a specific time. For more information, see [Removing compliance evidence generated before a designated time](/docs/devsecops?topic=devsecops-devsecops-prune-evidence)


## 31 January 2023
{: #devsecops-jan3123}
{: release-note}

- **Validation of inventory promotion pull request** - Provides evidence aggregation and summarization for the new state into which the inventory enters when the promotion PR is merged. Aggregation involves evidence collection and summarization from all the CI pipelines responsible for the inventory updates. The feature gives an early validation of the inventory promotion before the PR is merged to the target branch (environment). Based on the status of the PR, you can either move ahead with the promotion (when all the evidence results are GREEN), or fix the evidence (when the evidence is marked in RED) in the CI Pipelines before proceeding with promotion PR merge. For more information, see [Promotion pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-promotion-pipeline).

- **Support for Git submodule clone** - Enhances the ability of the CI Pipeline to clone the Git submodules for the application repositories that reference submodules. For more information, see [Configuring cloning of git submodules](/docs/devsecops?topic=devsecops-cd-devsecops-clone-submodules).

- **Prevent CI Pipeline from updating inventory for a RED build** - Gives the ability to detect failure within the CI pipeline and prevent the pipeline from updating the Inventory. For more information, see [Continuous integration parameters](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm).

## 07 December 2022
{: #devsecops-dec0722}
{: release-note}

DevSecOps Continuous Integration for Infrastructure as Code is available as a Toolchain template in the Toolchain catalog. For more information, see [Develop and deploy secure IaC with DevSecOps practices](/docs/devsecops?topic=devsecops-devsecops-tutorial-iac).

## 30 November 2022
{: #devsecops-nov3022}
{: release-note}

- CRA scans are now part of the `app-preview-pr-listener` in the CI pipeline.
- Cocoa Tekton `get-pipeline-logs` supports pulling logs for specific stages.
- Fixed malformed Git GraphQL URL for nonexistent target branch in the inventory repository.

## 25 October 2022
{: #devsecops-oct2522}
{: release-note}

- CRA issue parser now contains an **Introduced by** field when creating a new issue found during a scan.
- Improved error logging to debug long running promotion requests in the CD pipeline.


## 5 October 2022
{: #devsecops-oct0522}
{: release-note}

- Amber task support for pipelines to notify users that a pipeline has a non-fatal failure.
- Performance improvements in the CD pipeline change request flow.


## 16 September 2022
{: #devsecops-sep1622}
{: release-note}

- Added the `retry_sleep` environment property for the number of Vulnerability Advisor (VA) scans to attempt for a pipeline run.
- Improved log messages so the evidence status is clearer. For example, `Evaluated evidence status: <success|failure|pending>`.


## 11 August 2022
{: #devsecops-aug1122}
{: release-note}

- Use the native due date field in GitLab for managing issues.
- Added `custom-exempt-label` as an environment property that so pipelines are not marked as failed if they have the matching exempt label.
- Fixed Cloud Object Storage as an evidence locker for CC pipeline.

## 25 July 2022
{: #devsecops-jul2522}
{: release-note}

- Upload Continuous Compliance evidence summary for Security and Compliance Center.


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

Added support to the Continuous Integration and Continuous Compliance pipelines for [incident issues](/docs/devsecops?topic=devsecops-incident-issues), such as vulnerabilities and CVEs that are related to creating, storing, updating, and closing those issues. With this feature, you can set due dates for issues, add default assignees, and filter issues by using labels.

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

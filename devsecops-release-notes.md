---

copyright:
  years: 2022, 2024
lastupdated: "2024-10-21"

keywords: IBM Cloud DevSecOps release notes, Cloud DevSecOps changes, Cloud DevSecOps updates

subcollection: devsecops
content-type: release-note

---

{{site.data.keyword.attribute-definition-list}}

# Release notes for DevSecOps
{: #release-notes}

Use the release notes to learn about the latest changes to {{site.data.keyword.cloud_notm}} DevSecOps.
{: #shortdesc}

## 18 October 2024
{: #devsecops-Oct2024-18}
{: release-note}

### Updates for DevSecOps version - open-v9.42.0 and open-v10.23.0
{: #devsecops-open-v10.23.0-open-v9.42.0}

Enhanced evidence collection to branch protection checks
:   The `cocoa check pull-request-status` command now supports the `--branch-protection-settings-output-path` option to collect branch protection checks in a JSON file.


### Updates for DevSecOps version - open-v9.41.0 and open-v10.22.0
{: #devsecops-open-v10.22.0-open-v9.41.0}

Fast rollback during emergency situation outside normal deployment
:   Its now possible to perform rollback during emergency situations. See [Rolling back a deployment](https://cloud.ibm.com/docs/devsecops?topic=devsecops-devsecops-rollback-deployment) for more details

Single commit for all artifacts built during pipelinerun
:   The Cocoa inventory add command now includes a new from-file flag. To add multiple artifact entries, you should place the artifact details in a file and specify the file path with the from-file flag. This will read the file and create all the artifact entries in a single commit.

### Updates for DevSecOps version - open-v9.40.1 and open-v10.21.1
{: #devsecops-open-v10.21.1-open-v9.40.1}

Enhance `load_artifact` command
:   `load_artifact <artifact>` command now lists the properties associated with the artifact command. Earlier this used be an error.

### Updates for DevSecOps version - open-v9.40.0 and open-v10.21.0
{: #devsecops-open-v10.21.0-open-v9.40.0}

Availability of `PIPELINE_NAME` environment property
:   Made `PIPELINE_NAME` environment property across all stages using pipeline annotations

Enhancements to inventory add validation
:   If inventory validation was not successful (with cocoa inventory add) during a pipeline run, it will now notify as part of ci-pipeline end message

Evidence locker using Cloud Object Storage only
:   Pipelines can move away from git evidence locker. Its now possible to use COS as sole evidence locker

Enhancements to evidence reuse feature
:   `gosec` scan script will reuse evidence if evidence reuse is enabled

### Updates for DevSecOps version - open-v9.38.0 and open-v10.19.0
{: #devsecops-open-v10.19.0-open-v9.38.0}

Validation of inputs for `cocoa inventory add` command
:   Using cocoa inventory add command will now validate inventory fields and provide a message if changes are required to help standardize entries, functionality of command remains unchanged

Enhancements to evidence reuse feature
:   `sysdig` scan script will reuse evidence if evidence reuse is enabled

Refactor compliance scripts
:   compliance scripts have been refactored to enable execution of individual check

## 11 September 2024
{: #devsecops-Sep2024-09}
{: release-note}

### Updates for DevSecOps version - open-v9.37.0 and open-v10.18.1
{: #devsecops-open-v10.18.1-open-v9.37.0}

Bug fix release for `open-v10`
:   This release has bug fixes only.

### Updates for DevSecOps version - open-v9.37.0 and open-v10.18.0
{: #devsecops-open-v10.18.0-open-v9.37.0}

Transition to Cloud Object Storage(COS) as evidence locker
:   `devsecops` started transition to Cloud Object Storage(COS) as primary evidence locker. Also sends slack notifications informing users to migrate to COS as primary evidence locker.

`yq` upgrade
:   `devsecops` now creates `yq` version 3 and version 4 as `yq3` and `yq4` respectively.

Reuse of evidences in pipeline
:   It is now possible to reuse evidences from older pipeline runs to current run. See [Enable evidence collection in PR pipeline](https://cloud.ibm.com/docs/devsecops?topic=devsecops-cd-devsecops-pr-pipeline#cd-devsecops-pr-evidence-collection) for more information

### Updates for DevSecOps version - open-v9.36.1 and open-v10.17.1
{: #devsecops-open-v10.17.1-open-v9.36.1}

Bug fix release
:   This release has bug fixes only

### Updates for DevSecOps version - open-v9.36.0 and open-v10.17.0
{: #devsecops-open-v10.17.0-open-v9.36.0}

Logging of evidence collection data
:   Improved pipeline logs to show relavant data in tabular format

## 16 July 2024
{: #devsecops-July2024-16}
{: release-note}

### Updates for DevSecOps version - open-v9.35.0 and open-v10.16.0
{: #devsecops-open-v10.16.0-open-v9.35.0}

ZAP upgrade
:   ZAP version used for dynamic scan is upgraded to version 2.15.

`gosec` image upgrade
:   `gosec` image used for Go source code security scanning in the static scan step is upgraded to version 2.20.0.

CVSS scores
:   Compliance issues identified by `sysdig` scanner will now contain CVSS scores.

## 10 July 2024
{: #devsecops-July2024}
{: release-note}

### Updates for DevSecOps version - open-v9.34.1 and open-v10.15.1
{: #devsecops-open-v10.15.1-open-v9.34.1}

Bug fix release
:   This release has bug fixes only

### Updates for DevSecOps version - open-v9.34.0 and open-v10.15.0
{: #devsecops-open-v10.15.0-open-v9.34.0}

Warning on invalid baseline during secrets detection
:   Checks added to fail detect secrets scan if an corrupt / incompatible baseline file is provided. An issue will now be created if the detect secrets scan finds a baseline file that would not work with the scanner, users would need to rectify the same to proceed with the scan

### Updates for DevSecOps version - open-v9.33.0 and open-v10.14.0
{: #devsecops-open-v10.14.0-open-v9.33.0}

Bug fix release
:   This release has bug fixes only

### Updates for DevSecOps version - open-v9.32.0 and open-v10.13.0
{: #devsecops-open-v10.13.0-open-v9.32.0}

Enhancements for “Required Evidence Check” Feature in the CD/CC Pipeline
:   Released new schema (v2) for the required evidence validation configuration file. Please refer [here](https://cloud.ibm.com/docs/devsecops?topic=devsecops-evidence-checks-validation#config-file-version-2) for documentation using the new schema.

Performance improvements
:   Improved pipeline performance when Cloud Object Storage(COS) as the evidence locker for one pipeline evidences

Support for Sysdig policies
:   Added the support for Sysdig policies. Issues are created based on the applied policies.

## 18 June 2024
{: #devsecops-June2024-2}
{: release-note}

### Updates for DevSecOps version - open-v9.31.0 and open-v10.12.0
{: #devsecops-open-v10.12.0-open-v9.31.0}

Partial promotion of inventory artifacts
:   It is now possible to promote a subset of inventory items during promotion. See https://cloud.ibm.com/docs/devsecops?topic=devsecops-cd-devsecops-promotion-pipeline#cd-devsecops-promotion-partial-promotion for more details

## 06 June 2024
{: #devsecops-June2024}
{: release-note}

### Updates for DevSecOps version - open-v9.30.0 and open-v10.11.0
{: #devsecops-open-v10.11.0-open-v9.30.0}

Pipeline-run name can be customized
:   Its now possible to use custom names for pipeline-run name. Customized pipeline-run name can be provided using environment property `pipeline-display-name`

Custom finish stages for promotion and promotion-validation pipelines
:   Added custom finish stages for promotion and promotion validation pipelines, where user provided script can be executed in finish stage for the purpose of cleanup. This works similar to finish stages in CI, PR, CD, CC pipelines

Required Evidence Check Enhancements
:   Enhanced the `cocoa locker evidence check` command to support version 2 of the config schema. The updated config schema allows users to specify the application environment or service environment by providing the name and region details along with the required set of evidence to be validated for the given application environment or service environment.

Made Code Risk Analyzer scanning optional using environment properties
:   Three opt-in environment properties have been added as below to provide an option to opt in/out of CRA scans. See https://cloud.ibm.com/docs/devsecops?topic=devsecops-cd-devsecops-cra-scans#optout-cra-scans

| Name       | Type | Set of values | Required or optional | Pipelines(PR/CI/CD/CC) | Environment property| Default |Description|
|----------------------------|------|--------|----------|--------|----|---|-|
| `cra-bom-generate`         | enum | 0 or 1 | optional |CI/PR/CC|Yes | 1 | Software Bill of Materials(SBOM) generation will be done by CRA only if this value is set to 1 |
| `cra-vulnerability-scan`   | enum | 0 or 1 | optional |CI/PR/CC|Yes | 1 | CRA vulnerability scan will be done only if this value is set to 1  and `cra-bom-generate` is set to 1. If this value is set to 1 and `cra-bom-generate` is set to 0, this scan will be marked as failure |
| `cra-deploy-analysis`      | enum | 0 or 1 | optional |CI/PR/CC|Yes | 1 | CRA deployment analysis will be done only if this value is set to 1|
{: caption="Environment property parameters" caption-side="bottom"}

## 09 May 2024
{: #devsecops-May2024}
{: release-note}

### Updates for DevSecOps version - open-v9.29.0 and open-v10.10.0
{: #devsecops-open-v10.10.0-open-v9.29.0}

Fixes to `clone_repo` script
:   `clone_repo` script now reports exact error in case of clone failure.

Removed unwanted error messages for change requests
:   Removed inappropriate error messages from the log for change management using gitlab

### Updates for DevSecOps version - open-v9.28.1 and open-v10.9.1
{: #devsecops-open-v10.9.1-open-v9.28.1}

Bug fix release
:   This release has bug fixes only


## 18 April 2024
{: #devsecops-Apr2024}
{: release-note}

### Updates for DevSecOps version - open-v9.27.0 and open-v10.8.0
{: #devsecops-open-v10.8.0-open-v9.27.0}

Sysdig secure tool uses new vulnerability engine
:   Sysdig secure tool integration now uses new vulnerability engine in DevSecOps.

Evidence for peer review in CC pipeline
:   CC pipeline will now collect evidence for peer review validation.

Support for `eu-es` region
:   DevSecOps toolchains support `eu-es` region.

### Updates for DevSecOps version - open-v9.26.0 and open-v10.7.0
{: #devsecops-open-v10.7.0-open-v9.26.0}

Incorrect behavior with CD finish task
:   Earlier, CD finish task had different behavior. This issue is fixed now.

### Updates for DevSecOps version - open-v9.25.1 and open-v10.6.2
{: #devsecops-open-v10.6.2-open-v9.25.1}

Environment property `IAM_ACCESS_TOKEN` has been removed
:   Environment property `IAM_ACCESS_TOKEN` has been depreceated in this release.

Failed evidence will be collected if verification certificate is not available
:   During the artifact verification stage, if the verification certificate is not available- DevsecOps collects failed evidence.

### Updates for DevSecOps version - open-v10.6.1
{: #devsecops-open-v10.6.1}

Support for inline rollback in dev mode cd pipelines
:   Development mode of cd pipelines is updated to support inline rollback.

### Updates for DevSecOps version - open-v9.25.0 and open-v10.6.0
{: #devsecops-open-v10.6.0-open-v9.25.0}

Support for inline rollback, if there is deployment failure
:   Continuous Deployment now supports inline rollback when a deployment fails. For more information, see [Inline Rollback](/docs/devsecops?topic=devsecops-cd-devsecops-cd-pipeline#cd-devsecops-inline-rollback).

Deprecated environment property `IAM_ACCESS_TOKEN`
:   The environment property `IAM_ACCESS_TOKEN` is deprecated. If this token is needed by users, it can be retrieved by using `get_env iam-access-token`.

Ability to run `detect-secrets` tool in docker mode and in non-docker mode
:   `detect-secrets` tool can be run in non-docker mode by defining `detect-secrets-docker` to `0`. By default `detect-secrets` runs in docker mode.

## 21 March 2024
{: #devsecops-Mar2024}
{: release-note}

### Updates for DevSecOps version - open-v9.24.0 and open-v10.5.0
{: #devsecops-open-v10.5.0-open-v9.24.0}

Support for complex tfvars formats in Devsecops IaC
:   `tfvars` parameters in JSON format are now supported.

Exempt status if found issues in evidence summary
:   Evidence summary now indicates the exemption status of the detected issues.

### Updates for DevSecOps version - open-v9.23.8 and open-v10.4.8
{: #devsecops-open-v10.4.8-open-v9.23.8}

Fallback to defaults for stage parameters in `.pipeline-config.yaml`
:   In `.pipeline-config.yaml`, all stage parameters fallback to `defaults.yaml` if they are not specified to a dedicated stage.



## 22 February 2024
{: #devsecops-Feb2024}
{: release-note}

### Updates for DevSecOps version - open-v9.23.7 and open-v10.4.7
{: #devsecops-open-v10.4.7-open-v9.23.7}

Mandatory Image signature validation
:   Image signature validation is mandatory for deployments.

`:status-yellow:` icon in slack
:   Added new warning icon `:status-yellow:` in Slack notifications

### Updates for DevSecOps version - open-v9.23.6 and open-v10.4.6
{: #devsecops-open-v10.4.6-open-v9.23.6}

Validation of pipeline stages
:   At the start of the pipeline, validation of pipeline stages occur with respect to required, optional stage configuration.


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

- **Customizable finish stage** - Modified the last stage of the pipeline to run always irrespective of pipeline status, and added ability to have a customizable step in the last stage for the use of housekeeping in case of a pipeline failure. For more information, see [Custom finish stages](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize).

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

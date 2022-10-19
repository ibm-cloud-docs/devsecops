---

copyright:
  years: 2021, 2022
lastupdated: "2022-08-19"

keywords: DevSecOps, collect-evidence, script

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# collect-evidence script
{: #devsecops-collect-evidence}

The `collect-evidence` script helps adopters, users, and contributors to send their compliance data into the DevSecOps [change Management data flow](/docs/devsecops?topic=devsecops-cd-devsecops-automate-changemgmt).
{: shortdesc}

The script does the following tasks:

- Attempts to process any attachments as results, and creates incident issues from those results. A limited number of tool output formats are supported.
- If issues are found, the script evaluates their grace periods (due dates) and exemption states.
- Creates an evidence asset in the evidence locker.
- Creates the [evidence](/docs/devsecops?topic=devsecops-devsecops-evidence) itself, and attaches the issues and provided attachments.

The `collect-evidence` script is provided by the pipeline. It does not need to be installed. The script has the following dependencies:

- bash
- `libstdc++` shared library
- `libgcc` shared library

Make sure that the dependencies are installed in the base image that uses this tool for reporting evidence.

## Usage
{: #collect-evidence-usage}

The script `collect-evidence` requires the following parameters:

- `--tool-type`  
   The ID of the tool that provides evidence data. For example:
   "owasp-zap-ui", "cra"
- `--evidence-type`  
   The ID of the evidence type. For example:
   `com.ibm.image_vulnerability_scan`, `com.ibm.unit_tests`
- `--asset-key`  
   The key in pipelinectl assets. For the following commands
   `load_artifact <key>` or `load_repo <key>`
- `--asset-type`  
   The asset type from pipelinectl and can be one of the following types:
   `repo`, `artifact`
- `--status`  
   The evidence status and can be one of the following:
   `success`, `pending`, `failure`

The following parameter is optional:

- `--attachment`  
   The file to be processed as result and attached to the evidence. The parameter can be specified multiple times for multiple files.
- `--meta`  
   Arbitrary metadata to be added to the evidence. The parameter accepts 'key=value' pairs and can be specified multiple times.

Use the following command to get help:

```text
collect-evidence --help
```
{: pre}

### Return value
{: #collect-evidence-return-value}

`collect-evidence` outputs the evaluated evidence status string on STDOUT (one of `success`, `failure`, or `pending`). This evaluated value depends on the processed result attachments, found incident issues, and possible remediation of those issues, for example having a due date set or an exempt label. For more information, see [Incident issues](/docs/devsecops?topic=devsecops-incident-issues) and [Incident issues with due date](/docs/devsecops?topic=devsecops-devsecops-issues-due-date).

```bash
# example on how to read the output into a variable in bash

read -r status < <(collect-evidence "${evidence_params[@]}")

echo $status # success
```
{: codeblock}


### Example usage
{: #collect-evidence-usage-example}

```bash
collect-evidence \
  --tool-type "sonarqube" \
  --evidence-type "com.ibm.static_scan" \
  --asset-type "repo" \
  --asset-key "app-repo" \
  --status "success" \
  --attachment ./sonarqube-result-1.json \
  --attachment ./sonarqube-result-2.json \
  --meta environment=staging
```
{: codeblock}

## Supported tool formats
{: #collect-evidence-tool-formats}

The current implementation currently supports the following tools (provided as the `--tool-type` parameter):

- `cra` IBM Code Risk Analyzer
- `va` Vulnerability Advisor for IBM Cloud Container Registry
- `gosec` GoLang Security Scanner
- `xray` JFrog Xray - Vulnerability Scanning & Container Security
- `owasp-zap` OWASP Zed Attack Proxy (ZAP)
- `owasp-zap-ui` OWASP Zed Attack Proxy UI (ZAP UI)
- `sonarqube` 'SonarQube scan

If the `collect-evidence` script is called with a tool type that is not supported, the script doesn't attempt to process the attachments. Additionally, the issue handling is skipped, and the evidence collection is not stopped. 

If your script provides an attachment from a supported tool, but the attachment cannot be processed, the issue handling is skipped, and the evidence collection is not stopped.

## Evidence type
{: #collect-evidence-type}

You can set the evidence type by using the `--evidence-type` parameter. You can set any type, but the {{site.data.keyword.compliance_full}} supports the following evidence types:

- `com.ibm.unit_tests`
- `com.ibm.detect_secrets`
- `com.ibm.branch_protection`
- `com.ibm.static_scan`
- `com.ibm.code_vulnerability_scan`
- `com.ibm.code_bom_check`
- `com.ibm.code_cis_check`
- `com.ibm.cloud.image_vulnerability_scan`
- `com.ibm.cloud.image_signing`
- `com.ibm.dynamic_scan`
- `com.ibm.acceptance_tests`
- `com.ibm.prod_change_request`
- `com.ibm.close_change_reques`

### Asset requirements
{: #collect-evidence-asset}

Evidence that is collected with this tool is part of the V2 evidence collection work and the related evidence locker updates.

This new method focuses on asset-based evidence, meaning that evidence is connected to the artifact and repo through the scans and tests that run on those artifacts or repos, and produced results for evidence. For example:

- A repo with a certain commit becomes a commit asset, which is scanned, creating evidence for the commit asset.
- Using the same repo and commit, an image is built. The image becomes an asset that is related to the source asset, the repo and commit.
- The image is scanned, and evidence is created. All scan results are connected through the evidence, its asset, and the related assets.

To make all this work together, the assets that are provided with the parameters `--asset-type` and `--asset-key` must comply with some requirements:

#### `repo` assets added by using the `save_repo` command
{: #collect-evidence-repo-asset}

Check the [command reference](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#save_repo) for exact usage information.

**Required fields:**

- `url`  
   The repository URL.
- `commit`  
   The commit SHA.

#### `artifact` assets added by using the `save_artifact` command
{: #collect-evidence-artifact-asset}

Check the [command reference](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#save_artifact) for exact usage information.

**Required fields:**

- `name`  
   The image name, including registry, namespace, and image (example: `us.icr.io/team-images/service`).
- `digest`  
   The image digest (example: `sha256:a2292ed2b82c7a51d7d180c3187dbb0f7cc9ab385a68484c4f117e994acd6192`).

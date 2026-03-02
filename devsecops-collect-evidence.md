---

copyright:
  years: 2021, 2026
lastupdated: "2026-03-02"

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

For a status of `success` or `failure`, if no attachments are passed inside `collect-evidence`, the pipeline-logs for that particular task and the stage are captured as an attachment.

The `collect-evidence` script is provided by the pipeline. It does not need to be installed. The script has the following dependencies:

- bash
- `libstdc++` shared library
- `libgcc` shared library

Make sure that the dependencies are installed in the base image that uses this tool for reporting evidence.

## CLI Command Architecture
{: #collect-evidence-cli-architecture}

The `collect-evidence` functionality is available through two interfaces:

1. **Shell Script Wrapper** (`collect-evidence`): Traditional bash script interface that provides backward compatibility
2. **Direct CLI Command** (`cocoa locker evidence collect`): Native CLI interface with full feature access

### Version Toggle
{: #collect-evidence-version-toggle}

The `collect-evidence` shell script supports two implementation versions that can be toggled using the `collect-evidence-version` environment property:

| Version | Implementation | Status | Description |
|---------|---------------|--------|-------------|
| `v1` | Legacy | Available | Original bash-based implementation with full backward compatibility |
| `v2` | CLI-based | Default | Modern implementation that wraps `cocoa locker evidence collect` CLI command |

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
- `--assets`
   Specify multiple asset-key and asset-type pairs. For example, you can use `--assets asset-key1:asset-type1 --assets asset-key2:asset-type2`. If you use this option, do not specify asset-key and asset-type separately.

The following parameter is optional:

- `--attachment`
   The file to be processed as a result and attached to the evidence. The parameter can be specified multiple times for multiple files. For image signing, ensure that the signature file is attached using the --attachment parameter. The signature file must include the signature details, such as key ID, algorithm, and signed digest. Common formats include JSON or TXT.
- `--meta`
   Arbitrary metadata to be added to the evidence. The parameter accepts 'key=value' pairs and can be specified multiple times. You can include metadata relevant to the image signing process, such as the signing environment or any specific configurations used during the signing.
-  `--additional-comment`
   The comment that is added to an issue if a pipeline has failed.

Use the following command to get help:

```text
collect-evidence --help
```
{: pre}

### Return value
{: #collect-evidence-return-value}

`collect-evidence` outputs the evaluated evidence status string on STDOUT (one of `success`, `failure`, or `pending`). This evaluated value depends on the processed result attachments, found incident issues, and possible remediation of those issues, for example having a due date set or an exempt label. For more information, see [Incident issues](/docs/devsecops?topic=devsecops-incident-issues).

```bash
# example on how to read the output into a variable in bash

read -r status < <(collect-evidence "${evidence_params[@]}")

echo $status # success
```
{: codeblock}


#### Switching to v2 (CLI-based implementation)
{: #switch-to-v2}

To use the new CLI-based implementation, set the environment property in your pipeline:

```bash
collect-evidence-version=v2
```
{: codeblock}

### Using the CLI Command Directly
{: #collect-evidence-cli-direct}

```bash
cocoa locker evidence collect \
  --tool-type "sonarqube" \
  --evidence-type "com.ibm.static_scan" \
  --assets "app-repo:repo" \
  --status "success" \
  --attachment ./sonarqube-result.json \
  --pipeline-run-id "${PIPELINE_RUN_ID}" \
  --pipeline-namespace "ci" \
  --incident-org "my-org" \
  --incident-repo "compliance-issues"
```
{: codeblock}

```bash
cocoa locker evidence collect \
  --tool-type "detect-secrets" \
  --evidence-type "com.ibm.detect_secrets" \
  --assets "app-repo:repo" \
  --status "success" \
  --pipeline-run-id "${PIPELINE_RUN_ID}" \
  --pipeline-namespace "ci" \
  --incident-org "my-org" \
  --incident-repo "compliance-issues"
```
{: codeblock}

```bash
cocoa locker evidence collect \
  --tool-type "va" \
  --evidence-type "com.ibm.cloud.image_vulnerability_scan" \
  --assets "image-0:artifact" \
  --status "success" \
  --pipeline-run-id "${PIPELINE_RUN_ID}" \
  --attachment image-0_va-report.json \
  --pipeline-namespace "ci" \
  --incident-org "my-org" \
  --incident-repo "compliance-issues"
```
{: codeblock}


For complete CLI command reference and all available parameters, see [cocoa locker evidence collect](/docs/devsecops?topic=devsecops-cd-devsecops-cli#locker-evidence-collect).

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

```bash
collect-evidence \
  --tool-type "ciso-code-signing" \
  --evidence-type "com.ibm.cloud.image_signing" \
  --asset-type "artifact" \
  --asset-key "signed-image" \
  --status "success" \
  --attachment ./signature.json \   # The signature details in JSON format
  --attachment "./${artifact}.fingerprint" \ #  The fingerprint is a hash value generated from the artifact, ensuring integrity and authenticity.
  --meta environment=production
```
{: codeblock}

You can use the `cocoa locker evidence collect` command directly:

```bash
cocoa locker evidence collect \
  --tool-type "sonarqube" \
  --evidence-type "com.ibm.static_scan" \
  --assets "app-repo:repo" \
  --status "success" \
  --attachment ./sonarqube-result.json \
  --pipeline-run-id "${PIPELINE_RUN_ID}" \
  --pipeline-namespace "ci" \
  --incident-org "my-org" \
  --incident-repo "compliance-issues"
  ```
  {: codeblock}

## Supported tool formats
{: #collect-evidence-tool-formats}

The current implementation currently supports the following tools (provided as the `--tool-type` parameter):

| Tool Name | Description |
|--------|-------------------------|
| `cra` | IBM Code Risk Analyzer |
| `va` | Vulnerability Advisor for IBM Cloud Container Registry |
| `gosec` | GoLang Security Scanner |
| `xray` | JFrog Xray – Vulnerability Scanning & Container Security |
| `owasp-zap` | OWASP Zed Attack Proxy (ZAP) |
| `owasp-zap-ui` | OWASP Zed Attack Proxy UI (ZAP UI) |
| `sonarqube` | SonarQube scan |
| `peer-review` | Peer Review Scan |
| `twistlock` | TwistLock |
| `mend` | Mend Scan |
| `checkov` | Checkov Scan |
| `cra-tf` | Code Risk Analyzer for Terraform |
| `tfsec` | Terraform Security Scanner |
| `fips-scanner` | FIPS (Federal Information Processing Standards) Scanner |
| `detect-secrets` | Detect Secrets |
| `ciso-code-signing` | CISO Code Signing Tool |
| `sysdig` | Sysdig Scan |
| `cyclonedx` | CycloneDX format. Tool detection for issue management will be performed based on the CycloneDX metadata [here](https://cyclonedx.org/docs/1.4/json/#metadata_tools_items_name) |

 CycloneDX metadata is
 Tool detection for issue management

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
- `com.ibm.cloud.image_signing`
- `com.ibm.acceptance_tests`
- `com.ibm.prod_change_request`
- `com.ibm.close_change_reques`

## Evidence type collection and tool mapping
| Evidence type ID | Default supported tool | Origin | Ownership | Recommended asset | Issues |
|:----------|:------------------------------| ---- | ---- | ------- | ------ |
|`com.ibm.branch_protection`| `cocoa-branch-protection` | CI | Platform | repo | Non incident issues |
|`com.ibm.unit_tests`| `jest` | PR/CI | User | repo | Non incident issues |
|`com.ibm.detect_secrets`| `detect-secrets` | PR/CI/CC | Platform | repo | Incident / Non incident issues |
|`com.ibm.code_vulnerability_scan`| `cra-tf`, `cra`, `mend` </br>For infrastructure as code: `tfsec`, `checkov`| CI | Platform | repo | Incident / Non incident issues |
|`com.ibm.code_bom_check`| `cra-bom`, `sbom-utility` | PR/CI/CC | Platform | repo | Incident / Non incident issues |
|`com.ibm.code_cis_check`| `cra-cis` | PR/CI/CC | Platform | repo | Non incident issues |
|`com.ibm.peer_review`| `peer-review` | CI | Platform | repo | Non incident issues |
|`com.ibm.static_scan`| `sonarqube`, `gosec` </br> For infrastructure as code: `terraform-fmt`, `terraform-validate`, `tflint` | CI/CC | Platform | repo | Incident / Non incident issues |
|`com.ibm.cloud.image_signing`| `artifact-signing` | CI | Platform | repo | Non incident issues |
|`com.ibm.acceptance_tests`| `jest` | CI | User | artifact | Non incident issues |
|`com.ibm.dynamic_scan`| `owasp-zap`, `owasp-zap-ui` | CI | Platform | artifact | Incident / Non incident issues |
|`com.ibm.cloud.image_vulnerability_scan`| `va`, `sysdig`, `xray` | CI/CC | Platform | artifact | Incident / Non incident issues |
|`com.ibm.prod_change_request`|  `gitlab` | CD | Platform | artifact | Non incident issues |
|`com.ibm.close_change_request`|  `gitlab` | CD | Platform | artifact | Non incident issues |
|`com.ibm.cloud.slsa`| `tekton-chains` | CI | Platform | artifact | Non incident issues |
|`com.ibm.cloud.verify_signature`| `ciso-code-signing` | CD | Platform | artifact | Non incident issues |
|`com.ibm.pipeline_logs`| NA | CI/CD/CC | Platform | NA | NA |
|`com.ibm.pipeline_run_data`| NA | CI/CD/CC | Platform | NA | NA |
|`com.ibm.network_compliance`|  | CI | Platform | repo | Incident / Non incident issues |
{: caption="Supported tool for evidence" caption-side="top"}

When a scan fails or when attachments cannot be parsed, the tool automatically creates a non-incident issue to track the failure.
{: note}

## Asset requirements
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
   The artifact name. For example, for an image include registry, namespace, and image (example: `us.icr.io/team-images/service`).
- `digest`
   The artifact digest (example: `sha256:a2292ed2b82c7a51d7d180c3187dbb0f7cc9ab385a68484c4f117e994acd6192`).


**Changes required in save_artifact for non-images:** Collect evidence now supports all the asset types. For collect evidence to work on any asset
type
 `save_artifact` should explicitly save the asset with `type` for example, zip
`save_artifact artifact-1 type=zip ...`.
 In collect evidence script, the `asset-type` should be artifact and the type is queried from the artifact. For this process to work, cocoa locker asset add was modified to add asset of any type. Once saved, the  collect evidence script can be called as below:

  `collect-evidence --tool-type toolType --evidence-type  artifact --asset-key artifact-1 ...`





Please refer to our sample application for sample implementation for  `deployment` type  https://us-south.git.cloud.ibm.com/open-toolchain/hello-compliance-app


With these changes, collect-evidence script processes all types of artifacts, including both image and non-image artifacts.


## Multiple assets in collect-evidence
{: #multi-asset-evidence-collection}

By using collect-evidence, you can configure the simultaneous collection of evidence for multiple assets. You initiate evidence collection by using the `--assets` flag, which specifies multiple asset-key and asset-type pairs. For example, `input --assets asset-key1:asset-type1 --assets asset-key2:asset-type2`. If you choose this option, don't indicate asset-key and asset-type separately.

Remember these key points about the multi-asset collection:

- `status`, `attachment`, `tool-type`, `evidence-type`, and `upload-logs` are constant across all assets.
- By default, when you designate multiple assets, evidence processing follows the legacy flow. If you specify a single asset, evidence processing occurs through a flow that is specific to the tool or attachment.
- In case of failure, issues are created per asset. These issues are closed upon successful rerun of evidence collection. Closure correlates with the assets that you specified.
- A singular evidence file is generated, which features an ID that encompasses all combined assets.

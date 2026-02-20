---

copyright: 
  years: 2024
lastupdated: "2024-08-09"

keywords: DevSecOps, doi publish

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# check-evidence-for-reuse script
{: #check-evidence-for-reuse}

The `check-evidence-for-reuse` script is used to determine if evidence can be reused based on the provided parameters. This script is a wrapper around the `cocoa locker evidence reuse` CLI command.

## CLI Command Architecture
{: #check-evidence-reuse-cli-architecture}

The `check-evidence-for-reuse` functionality is available through two interfaces:

1. **Shell Script Wrapper** (`check-evidence-for-reuse`): Simplified interface for pipeline scripts
2. **Direct CLI Command** (`cocoa locker evidence reuse`): Native CLI interface with full feature access

### Version Toggle
{: #check-evidence-reuse-version-toggle}

The shell script uses the same version toggle as `collect-evidence` via the `collect-evidence-version` environment property:

| Version | Implementation | Status | Description |
|---------|---------------|--------|-------------|
| `v1` | Legacy | Available | Original implementation with full backward compatibility |
| `v2` | CLI-based | Default | Modern implementation that wraps `cocoa locker evidence reuse` CLI command |

To use the new CLI-based implementation, set the environment property in your pipeline:

```bash
collect-evidence-version=v2
```
{: codeblock}

## Shell Script Usage
{: #check-evidence-reuse-shell-usage}

### Required Parameters

- `--tool-type`
ID of the tool that provides evidence data. Examples: "owasp-zap-ui", "cra".

- `--evidence-type`
ID of the evidence type. Examples: "com.ibm.cloud.image_vulnerability_scan", "com.ibm.unit_tests".

- `--asset-key`
Key in pipelinectl assets for commands load_artifact or load_repo.

- `--asset-type`
Asset type from pipelinectl. Can be one of the following: repo | artifact.

### Optional Parameters

- `--output-path`
Full path of the file where the reused evidence JSON output should be saved. If not specified, output will be directed to a default location.

- `--meta`
Arbitrary metadata to be validated against the evidence found for reuse. Accepts key=value pairs and can be specified multiple times.

### Example usage

```bash

check-evidence-for-reuse \
  --tool-type "sonarqube" \
  --evidence-type "com.ibm.static_scan" \
  --asset-type "repo" \
  --asset-key "app-repo" \
```
{: codeblock}

### Exit Code
- `0`
Evidence for reuse was found and saved successfully.

- `1`
Evidence for reuse was not found.

### Output Value
- `success/failure`
Indicates the result of the reused evidence check.

## Using the CLI Command Directly
{: #check-evidence-reuse-cli-direct}

```bash
cocoa locker evidence reuse \
  --tool-type "detect-secrets" \
  --evidence-type "com.ibm.detect_secrets" \
  --assets "app-repo:repo" \
  --pipeline-run-id "${PIPELINE_RUN_ID}" \
  --pipeline-namespace "ci" \
  --incident-org "my-org" \
  --incident-repo "compliance-issues" \
  --format "json"
```
{: codeblock}

```bash
cocoa locker evidence reuse \
  --tool-type "sonarqube" \
  --evidence-type "com.ibm.static_scan" \
  --assets "app-repo:repo" \
  --attachment ./sonarqube-result.json \
  --pipeline-run-id "${PIPELINE_RUN_ID}" \
  --pipeline-namespace "ci" \
  --incident-org "my-org" \
  --incident-repo "compliance-issues" \
  --format "json"
```
{: codeblock}

The additional parameters:

- `--evidence-validity-period <hours>`: Set custom validity period (default: 24, max: 720)
- `--evidence-reuse-for-failure`: Enable reuse of failed evidence
- `--dry-run`: Preview without actually reusing evidence
- `--meta <key=value>`: Additional metadata for validation

For complete CLI command reference and all available parameters, see [cocoa locker evidence reuse](/docs/devsecops?topic=devsecops-cd-devsecops-cli#locker-evidence-reuse).

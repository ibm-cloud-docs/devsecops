---

copyright: 
  years: 2024
lastupdated: "2024-08-09"

keywords: DevSecOps, doi publish

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# check-evidence-for-reuse
{: #devsecops-check-evidence-for-reuse}

The `check-evidence-for-reuse` script is used to determine if evidence can be reused based on the provided parameters. This script utilizes `the check-evidence-for-reuse` command with parameters similar to those used in the `collect-evidence` script.

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

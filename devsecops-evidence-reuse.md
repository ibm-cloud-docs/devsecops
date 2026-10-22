---

copyright:
  years: 2021, 2026
lastupdated: "2026-10-22"

keywords: DevSecOps, compliance evidence, IBM Cloud, evidence reuse

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Reuse of evidence across PR and CI pipelines
{: #devsecops-evidence-reuse}

By default, when a PR or CI pipeline runs, all the configured scans and tests always run, even when the repository content or image that
is being scanned or tested has not changed since the last scan or test. As a result, new evidence is collected with every run. This wastes time and resources.

DevSecOps pipelines provide an opt-in feature called **Evidence Reuse** to skip the scan or test in pipeline stages when the repository
content or image that is being scanned or tested has not changed since the last scan or test. In this case, no scan or test runs, so no new evidence is collected.
Instead, the evidence that was previously collected for the scan or test is fetched and reused in the CI, CD, and CC summaries. This speeds up the PR and CI pipelines
and optimizes resource usage. When the feature is enabled, the pipeline validates that what is being tested or scanned has not changed since the previous run.
If the validation succeeds, the pipeline stages can skip the test or scan and rely on the evidence and results that were collected earlier.

Typical use cases for evidence reuse are:

- Use case 1:
    - Run unit tests in the PR pipeline with evidence collection.
    - Merge the PR by any method you prefer (rebase, squash, or regular merge).
    - If the source has not changed between the PR and CI pipeline runs, the unit-test run in the CI pipeline can be skipped. The unit-test evidence from the PR pipeline run is fetched and reused for the CI summary.

- Use case 2:
    - Run the PR pipeline with evidence collection, or run the CI pipeline.
    - One of the pipeline stages, for example `compliance-check`, fails due to an intermittent issue.
    - Rerun the PR or CI pipeline on the same commit.
    - The successful scans and tests are skipped in the rerun, and their evidence from the previous run is fetched and reused.
    - Only the failed scan runs, and new evidence is collected for it.

To enable evidence collection in the PR pipeline, see [PR pipeline evidence collection](/docs/devsecops?topic=devsecops-cd-devsecops-pr-pipeline#cd-devsecops-pr-evidence-collection).

## Enabling evidence reuse
{: #evidence-reuse-properties}

To opt in to evidence reuse in the PR or CI pipeline, set the environment property `evidence-reuse` to `1`. By default, this property is not enabled.

| Property | Description | Default |
|----------|-------------|---------|
| `evidence-reuse` | Set to `1` to enable evidence reuse. | Not enabled |
| `evidence-reuse-for-failure` | Set to `1` to also reuse failed evidence. Set it in addition to `evidence-reuse=1`. | Not enabled |
| `evidence-validity-period` | Number of hours within which evidence can be reused. | 24 (maximum 720, which is 30 days) |
{: caption="Table 1. Evidence reuse properties" caption-side="bottom"}

The `check-evidence-for-reuse` tool checks whether evidence exists for the combination of `asset-key`, `tool-type`, and `evidence-type`, and
validates that the evidence meets the conditions for reuse. For more information, see [check-evidence-for-reuse](/docs/devsecops?topic=devsecops-check-evidence-for-reuse).


For the complete CLI command reference and all available parameters, see [cocoa locker evidence reuse](/docs/devsecops?topic=devsecops-cd-devsecops-cli#locker-evidence-reuse).

## Conditions for reusing evidence
{: #evidence-reuse-conditions}

The `check-evidence-for-reuse` tool validates the following conditions:

- The evidence was collected on the same commit asset, repo content, or image asset.
- The evidence status is `success`. Evidence with any other status is not considered for reuse, and a new scan runs.
  This ensures that when failure evidence was collected due to an intermittent failure in a stage, a rerun of the pipeline does not reuse the failed evidence. Instead, a new scan runs and new evidence is collected.
  However, in some cases the failed evidence needs to be reused. For example, a scan detected a vulnerability in the CI pipeline, and the change must be deployed as an emergency deployment. To reuse the failed evidence, set the environment property `evidence-reuse-for-failure=1` in addition to `evidence-reuse=1`.
- The evidence is not stale. The evidence collection date is within the validity period. The default validity period is 24 hours.
  You can configure the value for a pipeline run by using the environment property `evidence-validity-period` (in hours). The value is bound by an upper limit of 720 hours (30 days).
- Optionally, the evidence matches any configuration checks that were saved in the details section, such as the tool version or the step base image version.

## Supported tools and evidence types
{: #evidence-reuse-supported-types}

The default scanning scripts involved in DevSecOps have been updated to allow evidence reuse when it is enabled.

Evidence types for which evidence reuse is supported:

| Evidence type | Can be reused |
|---------------|---------------|
| `com.ibm.static_scan` | Yes |
| `com.ibm.dynamic_scan` | Yes |
| `com.ibm.detect_secrets` | Yes |
| `com.ibm.code_bom_check` | Yes |
| `com.ibm.code_cis_check` | Yes |
| `com.ibm.code_vulnerability_scan` | Yes |
| `com.ibm.acceptance_tests` | Yes. You must update your custom script. |
| `com.ibm.unit_tests` | Yes. You must update your custom script. |
| `com.ibm.branch_protection` | No |
| `com.ibm.pipeline_run_data` | No |
| `com.ibm.peer_review` | No |
{: caption="Table 3. Evidence types and reuse support" caption-side="bottom"}

Update your custom scripts, such as unit tests and acceptance tests, to allow evidence reuse when it is enabled.
To adopt evidence reuse in your custom scripts, see the following section.

## Adopting evidence reuse in scripts
{: #adopting-evidence-reuse-in-scripts}

To implement evidence reuse in your scripts, first check whether reusable evidence is available. The following pseudo code outlines how to check for existing evidence and decide the next step.

### Pseudo code
{: #pseudo-code}

```bash
Check whether evidence for reuse exists for (asset, tool, evidence-type) by using check-evidence-for-reuse
  If evidence is found
    Skip the scan or test
    Save the reused result file (optional)
  Else
    Continue with your existing scan process and collect new evidence as usual
```

### Check whether evidence is available for reuse
{: #check-reuse-evidence}

Use the `check-evidence-for-reuse` command with the same parameters that you use in the `collect-evidence` script. For details, see [check-evidence-for-reuse](/docs/devsecops?topic=devsecops-check-evidence-for-reuse).

The command exits with `0` when reusable evidence is found and saved, and with `1` when it is not found.

### Example usage
{: #example-usage}

```bash
evidence_params=(
  --tool-type "my-tool"
  --evidence-type "com.ibm.unit_tests"
  --asset-type "repo"
  --asset-key "${repo_key}"
)
reused_evidence_json="$(mktemp)"

if check-evidence-for-reuse "${evidence_params[@]}" --output-path "${reused_evidence_json}"; then
  echo "Reusable evidence found. Skipping the test."
else
  # No reusable evidence: run the test and collect new evidence as usual
  run_my_tests
  collect-evidence "${evidence_params[@]}" --status "success"
fi
```
{: codeblock}

---

copyright: 
  years: 2021, 2024
lastupdated: "2024-08-13"

keywords: DevSecOps, compliance evidence, IBM Cloud, evidence reuse

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Reuse of evidences across PR and CI pipelines
{: #devsecops-evidence-reuse}

By default, when a PR or CI pipeline is executed, all the configured scans and tests always run even when the repository content or image that 
is being scanned or tested has not changed since the last scan or test. As a result new evidences are collected with every run. This results 
in wastage of time and resources.

One-pipeline provides an opt-in feature for adopters called <b>Evidence Reuse</b> to skip the scan/test in pipeline stages when the repository 
content or image that is being scanned or tested has not changed since the last scan or test. In this case, as no scan or test is run, no new evidence is collected.
Instead the previously collected evidence for the scan/test are fetched and reused in the CI, CD and CC summaries. This helps in speeding-up the PR and CI pipelines 
and optimizing resource usage. With the evidence reuse feature enabled, one-pipeline validates if what is being tested/scanned has not changed since the previous run.
On successful validation, the pipeline stages can skip the test or scan and rely on the previous test/scan and the evidence and results collected for the same.

Some of the typical use-cases for evidence reuse are:
- Usecase 1:
  - Run unit tests in PR pipeline with evidence collection.
  - Merge the PR.
  - If the source has not changed between the PR and CI pipeline runs, the unit-test run in CI pipleine can be skipped and unit-test evidence from the PR pipeline run 
    can be fetched and reused for the CI summary.

- Usecase 2:
  - Run the PR pipeline with evidence collection or CI pipeline
  - One of the pipeline stages for example, compliance-check fails due to an intermittent issue.
  - Re-run the PR or CI pipeline on the same commit
  - The successful scans and tests will be skipped in the re-run and their evidences from the previous run will be fetched and reused.
  - Only the failed scan will be run and evidence will be collected.

To enable evidence collection in PR pipeline, please refer to the [documentation on [PR pipeline evidence collection](/docs/devsecops?topic=devsecops-cd-devsecops-pr-pipeline#cd-devsecops-pr-evidence-collection)
To opt-into the evidence reuse feature in PR or CI pipeline, set the environment property `evidence-reuse` to 1. By default this property is not enabled.

Pre-requisites for evidence-reuse feature to be enabled:
- COS integration should be enabled
- Evidence batching should be enabled

The tool `check-evidence-for-reuse` checks if an evidence exists for the combination of `asset-key`, `tool-type` and `evidence-type` and 
validates if the evidence meets the conditions for reuse. For more info, please refer to the documentation of [check-evidence-for-reuse](/docs/devsecops?topic=devsecops-check-evidence-for-reuse)

Conditions for reusing an evidence that are validated by the `check-evidence-for-reuse` tool:
- The evidence should be collected on the same commit asset (or) repo-content (or) image-asset

- The evidence should not be stale. The evidence collection date should be within the specified validity period. The default validity period is 24 hrs.
The value is configurable for a pipeline run using the environment property `evidence-validity-period` (in number of hours). The value is bound by an upper limit (30 days)

- The evidence should optionally match any configuration checks such as tool version, step base image version, etc that were saved in the details section.

The scanning scripts need to be updated in-order to allow evidence reuse when enabled. One-pipeline will update the default scanning scripts to allow evidence reuse.
Adopters need to update their custom scripts such as unit tests, acceptance tests, etc to allow evidence reuse when enabled. For detailed steps, please refer to the documentation
for [Adopting evidence reuse in scripts](/docs/devsecops?topic=devsecops-adopting-evidence-reuse-in-scripts).



---

copyright:
  years: 2021, 2022
lastupdated: "2022-03-23"

keywords: DevSecOps, context layer, compliance status

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Pipeline status
{: #cd-devsecops-pipeline-status}

The reference pipelines separate compliance status and pipeline run status into separate context layers to provide information about the overall behavior of the pipeline.
{: shortdesc}

## Compliance status context layer
{: #pipeline-compliance-status-layer}

From a compliance point of view, a failed unit test result is not a barrier to deployment. If a test, scan, or check fails, the pipeline does not stop. In this scenario, the task that runs the test is marked `amber` to signal that a failure was detected in that stage, while still allowing the compliance flow to continue without blocking the pipeline. This feature is important in an outage, where you might need to release a fix that contains a failed check to get the site back online, and then resolve the test failure later. Although you can deploy artifacts with failed checks, the process preserves the evidence from those failures.

The following task statuses are used within the compliance status context layer:

- `green` — The stage ran successfully and no issues were detected. This corresponds to an exit code of `0`.
- `amber` — The stage ran but detected one or more failures (for example, a failing scan or unit test). This corresponds to an exit code of `1`. The pipeline continues running despite the amber status.

When the status of a unit test task that identified failing tests is marked `amber`, it means the task ran but found specific issues that should be reviewed and resolved.

To keep the final pipeline status synchronized with the compliance results, a task is used at the end of the pipeline to check the compliance results. Then, the pipeline run status is set to `red`, or `green` based on the aggregated stage outcomes.

## Pipeline status context layer
{: #pipeline-status-layer}

The overall pipeline status is the summation of all individual stage statuses (`green`, `amber`, and `red`) accumulated across the pipeline run.
When a pipeline task stops with a status of `red`, the pipeline cannot continue. This status indicates one of the following:
- There is an error in a task's definition.
- One of the core pipeline operations is failing. For example, a failure occurs while it is building the output artifacts, which defeats the purpose of the continuous integration itself.

---

copyright:
  years: 2021, 2022
lastupdated: "2022-03-23"

keywords: DevSecOps, context layer, compliance status

subcollection: devsecops

---

{:shortdesc: .shortdesc}
{:table: .aria-labeledby="caption"}
{:external: target="_blank" .external}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:download: .download}
{:help: data-hd-content-type='help'}
{:support: data-reuse='support'}

# Pipeline status
{: #cd-devsecops-pipeline-status}

The reference pipelines separate compliance status and pipeline run status into separate context layers to provide information about the overall behavior of the pipeline.
{: shortdesc}

## Compliance status context layer
{: #pipeline-compliance-status-layer}

From a compliance point of view, a failed unit test result is not a barrier to deployment. If a test, scan, or check fails, the pipeline does not stop. In this scenario, the task that runs the test is marked `green` so that the compliance flow does not prevent the pipeline from completing. This feature is important in an outage, where you might need to release a fix that contains a failed check to get the site back online, and then resolve the test failure later. Although you can deploy artifacts with failed checks, the process preserves the evidence from those failures.

When the status of a unit test task that identified failing tests is marked `green`, it means only that the task ran successfully, even if it found specific issues.

To keep the final pipeline status synchronized with the compliance results, a task is used at the end of the pipeline to check the compliance results. Then, the pipeline run status is set to either `red` or `green`.

## Pipeline status context layer
{: #pipeline-status-layer}

When a pipeline task stops with a status of `red`, the pipeline cannot continue. This status indicates one of the following:
- There is an error in a task's definition.
- One of the core pipeline operations is failing. For example, a failure occurs while it is building the output artifacts, which defeats the purpose of the continuous integration itself.

---

copyright:
  years: 2021
lastupdated: "2021-06-29"

keywords: DevSecOps

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

From a compliance point of view, a failed unit test result is not a barrier to deployment. If a test, scan, or check fails, the pipeline does not stop. In this scenario, the task that runs the test is marked as green so that the compliance flow does not prevent the pipeline from completing. This feature is important in an outage, where you might need to release a fix that contains a failed check to get the site back online, and then resolve the test failure later. Although you can deploy artifacts with failed checks, the process preserves the evidence from those failures. 

When the status of a unit test task that identified failing tests is marked as `green`, it means only that the task ran successfully, even if it found specific issues.

To keep the final pipeline status synchronized with the compliance results, a task is used at the end of the pipeline to check the compliance results. Then, the pipeline run status is set to either `red` or `green`.

## Pipeline status context layer
{: #pipeline-status-layer}

When a pipeline task stops with a status of `red`, the pipeline cannot continue. This status is either because of an error in a task's definition, or because one of the core operations that the pipeline is running fails. For example, a failure while it is building the output artifacts, defeating the purpose of the continuous integration itself.

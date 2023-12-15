---

copyright:
  years: 2022
lastupdated: "2023-12-04"

keywords: DevSecOps, compliance evidence, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Evidence summary
{: #devsecops-evidence-summary}

Evidence summary refers to a collection of evidence pieces that are relevant for a set of artifacts. The pipeline currently uses two approaches to collect and summarize evidence, `v1` and `v2`. The intention is that `v1` is superseded by `v2`.
{: shortdesc}

## v2 evidence collection
{: #evidence-v2}

The evidence locker stores three kinds of entities: _Assets_, _Evidence_, and _Attachments_. For more information, see [v2 evidence format](/docs/devsecops?topic=devsecops-devsecops-evidence#devsecops-v2-evidence-format).

### Asset
{: #evidence-asset}

An asset represents something that you can test and scan, such as a Git commit in a repository or a docker image.

### Evidence
{: #evidence-evidence}

A piece of evidence represents the outcome of a scan or test. Evidence is always connected to at least a single asset. Multiple assets are allowed. For example, a single end-to-end test suite probably tests multiple assets together.

### Attachment
{: #evidence-attachment}

An attachment is arbitrary data that can be attached to a piece of evidence. It's usually the raw output of the tool that performed the scan or test.

### Collecting evidence
{: #evidence-collect}

Use the [`collect-evidence`](/docs/devsecops?topic=devsecops-devsecops-collect-evidence) script to collect evidence.

### Evidence summary
{: #evidence-v2-summary}

In contrast to `v1`, `v2` evidence is stored in a flat hierarchy, where each piece of evidence is identified by its own hash. This hash provides a layer of integrity protection. That is, any modification of the evidence content can be detected. As each piece of evidence is related to one or more assets, the evidence summarization algorithms discover the relevant evidence based on assets (as opposed to pipelines run IDs in `v1`).

Evidence is summarized, based on the following.

- **Pipeline Run ID**: Evidences are scoped based on the pipeline run ID. Scoping sets the context of an evidence.
- **Asset**: An asset is a fundamental entity that is subjected to rigorous testing and scanning. These assets encompass various forms, such as existing Git commits in repositories to Docker images.

In the CI pipeline, evidence is summarized, based on the following.

- The scope is `pipeline_run_id` of the **current CI run** for the evidence summarization.
- The asset list gets computed by using the [list_artifact](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#list_artifacts) command that list all the saved artifacts in that run.

In the CD pipeline, evidence is summarized, based on the following.

- The `provenance` field of each inventory item acts as the `asset` for the summarization.
  We have three types of asset lists: 
    - **Delta asset list**: Assets which **got modified** from the last deployment in the deployment environment.
    -	**Non-delta asset list**: Assets which are **not modified** from the last deployment in the deployment environment.
    -	**Full asset list**: Assets from all the inventory entries coresponding to the deployment environment.

- The `pipeline_run_id` field of each inventory item acts as the `scope` for the summarization.
    - The CI scope read from the `pipeline_run_id` of the inventory entries.
    - Thw current `pipeline_run_id` as the CD pipeline scope.

In the CC pipeline, evidence is summarized, based on the following.

- The `provenance` field of each inventory item as the `asset` for the summarization. Assets from all the inventory entries that corespond to the latest on the deployed environment.

- The `pipeline_run_id` field of each inventory item acts as the `scope` for the summarization
    - The CI scope read from the `pipeline_run_id` of the inventory entries.
    - The CD scope read from the `CD-pipeline_run_id` tag that is created at the [inventory conclude](/docs/devsecops?topic=devsecops-cd-devsecops-inventory#inventory-conclude).
    - The current `pipeline_run_id` as the CC pipeline scope.

For more information, see [Inventory](/docs/devsecops?topic=devsecops-cd-devsecops-inventory).
For more information on `prod` summarization based on pre-prod evidences, see [Collecting evidence summary](/docs/devsecops?topic=devsecops-cd-devsecops-cd-pipeline#cd-devsecops-pipeline-collect).

The `v2` evidence summary is calculated by default. 

## v1 evidence collection 
{: #evidence-summary-v1}
{: deprecated}
Evidence is organized in the locker on a per-pipeline-run basis, that is, every piece of evidence that was collected in a pipeline run is stored next to each other. This organization makes evidence collection easy when you have a pipeline run ID. However, it's difficult to tell which piece of evidence is relevant for an asset, such as evidence that is related to a docker image. This difficulty is because evidence can span across multiple pipeline runs, such as a CI pipeline, a staging CD pipeline, or a production CD pipeline.
For more information, see [v1 evidence format](/docs/devsecops?topic=devsecops-devsecops-evidence#devsecops-v1-evidence-format).

Because of these difficulties, `v1` evidence is superseded by `v2`.

The v1 evidence collection is the legacy evidence collection. To opt out of v1 evidence collection, see [Turning off legacy v1 evidence collection](/docs/devsecops?topic=devsecops-turn-off-v1-evidence).
{: important}

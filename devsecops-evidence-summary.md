---


copyright:
  years: 2022
lastupdated: "2022-05-19"

keywords: DevSecOps, evidence summary, ibm cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Evidence summary
{: #devsecops-evidence-summary}

_Evidence summary_ refers to a collection of evidence pieces that are relevant for a set of artifacts. The pipeline currently uses two approaches to collect and summarize evidence, `v1` and `v2`. The intention is that `v1` is superseded by `v2`.
{: shortdesc}

## v1 evidence collection
{: #evidence-summary-v1}

Evidence is organized in the locker on a per-pipeline-run basis, that is, every piece of evidence that was collected in a pipeline run is stored next to each other. This organization makes evidence collection easy when you have a pipeline run ID. However, it's difficult to tell which piece of evidence is relevant for an asset, such as evidence that is related to a docker image. This difficulty is because evidence can span across multiple pipeline runs, such as a CI pipeline, a staging CD pipeline, or a production CD pipeline.

Because of these difficulties, `v1` evidence is superseded by `v2`.

## v2 evidence collection
{: #evidence-v2}

The evidence locker stores three kinds of entities: _Assets_, _Evidence_, and _Attachments_.

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

Evidence is summarized in the CD pipeline, based on the `provenance` field of each inventory item. For more information, see [Inventory](/docs/devsecops?topic=devsecops-cd-devsecops-inventory).

The `v2` evidence summary is calculated, unless you explicitly opt out by setting `skip-v2-summary` to `1`.

The change management system uses the `v1` evidence summary as the basis for the change request, unless you explicitly opt in by setting `use-v2-summary` to `1`.

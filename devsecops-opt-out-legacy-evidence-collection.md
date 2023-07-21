---

copyright:
  years: 2022
lastupdated: "2022-06-01"

keywords: DevSecOps, compliance evidence, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Turning off legacy v1 evidence collection
{: #turn-off-v1-evidence}

`v1` evidence summary is deprecated from the release `v9.19.1`, if you are still using v1 evidence then use the version before `v9.19.1`
{: deprecated}

The v9 pipeline version introduces a new evidence locker model and evidence collection mechanism, called asset-based evidence. This new version works in parallel to the legacy, pipeline-based evidence model. The current configuration is redundant and fails to benefit from the v2 asset based evidence features. To take the next steps, and fully leverage asset based evidence, you can opt out from v1 evidence collection.
{: shortdesc}

To opt out from v1 evidence collection, set the pipeline variable `opt-out-v1-evidence` to `1`. This can be applied to both CI and CD pipelines. The PR pipeline does not collect evidence because the CC and Async pipelines already rely on v2 evidence.

The opt out does the following:

- Disables the current v1 pipeline based evidence collection at the end of pipelines.
- Disables the creation of legacy incident issues that are related to pipeline and task runs, and do not provide any details.
- Disables the current pipeline evaluator at the end of pipelines. This evaluator marks pipelines as failed or passing at the end based on v1 evidence.
- Enables the v2 pipeline evaluator, which evaluates pipeline runs based on collected evidence in that pipeline.
- enable pipeline log and run-data collection as a v2 evidence (collects to COS only).
- Turns on the creation and usage of v2 summary in CD.
- Turns on the collection of v2 evidence on CR operations in CD. 

## Important
{: #evidence-important}

Make sure both CI and CD pipelines are switched to use v2 evidence together.

If the CI pipeline is opted out from legacy v1, but CD is not, the CD pipeline cannot create a v1 summary. Setting the `use-v2-summary` flag to `1` is a safe option because opting out uses the same summary generation.

The following is the correct order to opt out a CI/CD flow from v1 legacy evidence collection:

1. Opt out from legacy evidence collection in CI.
1. Create a build.
1. Promote that build to CD.
1. Opt out from legacy evidence collection in CD.
1. Run CD to deploy the promoted inventory content.

The same order is required for any further CD promotions. For example, CD runs to promote from staging to production. You also need to opt out from using legacy evidence in production CD.

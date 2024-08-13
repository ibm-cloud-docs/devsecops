---

copyright:
  years: 2023, 2024
lastupdated: "2024-08-13"

keywords: DevSecOps pipeline definitions, pipeline version, pipeline variant, devsecops templates, devsecops template

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Pipeline definitions provided by DevSecOps
{: #cd-devsecops-pipeline-defs}

Different versions of pipeline definitions are available to use with DevSecOps templates. You might need to use a specific version, depending on your needs.
{: shortdesc}

The following table lists the different versions of pipeline definitions that are used by DevSecOps templates:

|Version |Description |
|:----------|:------------------------------|
|`open-v9`| Default version of DevSecOps pipelines. The stages run in sequential order.|
|`open-v10`| DevSecOps pipeline version where some pipelines run concurrently. This version will have performance improvement as [tasks run concurrently](/docs/devsecops?topic=devsecops-devsecops-conc). |
{: caption="Table 1. Continuous compliance pipeline versions" caption-side="top"}

## Deprecating compliance-baseimage environment property
{: #cd-devsecops-pipeline-defs-compliance-baseimage-deprication}

Adopters using `compliance-baseimage` environment property are advised to remove this environment property. If you want to try out certain features, use the pipeline definition. Users are advised to remove usage of `compliance-baseimage` environment property from their pipelines before EoQ Q3-2024. Warning will keep appearing until the end of this quarter and will start breaking the pipeline at the start of next quarter.
{: shortdesc}

Support for this environment property will be discontinued in the future. This will allow users of DevSecOps pipelines to continue receiving the latest updates through the default value set within the compliance-pipeline definition.
We recommend removing the compliance-baseimage environment property well in advance so that you’ll have sufficient time to make adjustments if you encounter any breaking changes.

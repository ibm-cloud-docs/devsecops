---

copyright:
  years: 2022
lastupdated: "2022-05-02"

keywords: DevSecOps, IBM Cloud, BOM

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

# Generating the software bill of materials (SBOM) in `cyclonedx` format
{: #generate-cyclonedx-sbom}

The Continuous Integration (CI) pipeline generates the Software Bill of Materials (`BOM`) with the help of Code Risk Analyzer. By default, this `BOM` is generated in the `standard` format.
{: shortdesc}

## Prerequisites
{: #generate-cyclonedx-sbom-prereq}

This feature is based on V2 evidence collection. Make sure your custom scripts already collect v2 evidence in order to be able to use the CycloneDX SBOM generation.

## CI pipeline
{: #generate-cyclonedx-sbom-ci-pipeline}

To configure the pipelines to also generate the `BOM` in `cyclonedx` format, create a new pipeline environment property that is named `cra-generate-cyclonedx-format`, and set its value to `1`.

After the pipeline is run, the `BOM` is added as an attachment to the `com.ibm.code_bom_check` evidence in both `standard` and `cyclonedx` format.
{: note}

## CD Pipeline

The Continuous Delivery (CD) pipeline is capable of collecting the CycloneDX SBOM attachment of every asset that is being deployed by the run. The pipeline will merge these CycloneDX SBOMs and will upload the aggregated CycloneDX SBOM to the change request as an attachment.

To enable this feature, create a new pipeline environment property that is named `merge-cra-sbom`, and set its value to `1`.

After the pipeline is run, the change request will have an attachment named `CycloneDX SBOM` which can be inspected on the change management providers UI.
{: note}

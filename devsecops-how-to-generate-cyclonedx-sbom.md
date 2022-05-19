---
copyright:
  years: 2022
lastupdated: "2022-05-19"

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

OWASP CycloneDX is a lightweight Software Bill of Materials (SBOM) standard that is designed for use in application security contexts and supply chain component analysis. The Continuous Integration (CI) pipeline generates the Software Bill of Materials (`BOM`) with the help of Code Risk Analyzer. By default, this `BOM` is generated in the `standard` format.
{: shortdesc}

To learn more about `BOM` generation, see [Bill of Materials (BOM)](/docs/code-risk-analyzer-cli-plugin?topic=code-risk-analyzer-cli-plugin-cra-cli-plugin#bom-generate-command).

## Before you begin
{: #generate-cyclonedx-sbom-prereq}

This feature is based on V2 evidence collection. Make sure that your custom scripts already collect v2 evidence to be able to use the CycloneDX SBOM generation.

## CI pipeline
{: #generate-cyclonedx-sbom-ci-pipeline}

To configure the pipelines to also generate the `BOM` in `cyclonedx` format, create a new pipeline environment property that is named `cra-generate-cyclonedx-format`, and set its value to `1`.

After the pipeline is run, the `BOM` is added as an attachment to the `com.ibm.code_bom_check` evidence in both `standard` and `cyclonedx` format.
{: note}

## CD pipeline
{: #generate-cyclonedx-sbom-cd-pipeline}

The Continuous Delivery (CD) pipeline can collect the CycloneDX SBOM attachment of every asset that is being deployed by the run. The pipeline merges these CycloneDX SBOMs and uploads the aggregated CycloneDX SBOM to the change request as an attachment.

To enable this feature, create a new pipeline environment property that is named `merge-cra-sbom`, and set its value to `1`.

After the pipeline is run, the change request has an attachment that is named `CycloneDX SBOM` that can be inspected on the change management providers UI.
{: note}

---
copyright:
  years: 2022
lastupdated: "2022-09-07"

keywords: DevSecOps, IBM Cloud, BOM

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Generating the software bill of materials (SBOM) in `cyclonedx` format
{: #generate-cyclonedx-sbom}

OWASP CycloneDX is a lightweight Software Bill of Materials (SBOM) standard that is designed for use in application security contexts and supply chain component analysis. The Continuous Integration (CI) pipeline generates the Software Bill of Materials (`BOM`) with the help of Code Risk Analyzer. By default, this `BOM` is generated in the `standard`  as well as `cyclonedx` format.
{: shortdesc}

To learn more about `BOM` generation, see [Bill of Materials (BOM)](/docs/code-risk-analyzer-cli-plugin?topic=code-risk-analyzer-cli-plugin-cra-cli-plugin#bom-generate-command).

## Before you begin
{: #generate-cyclonedx-sbom-prereq}

This feature is based on V2 evidence collection. Make sure that your custom scripts already collect v2 evidence to be able to use the CycloneDX SBOM generation.

## Continuous integration pipeline
{: #generate-cyclonedx-sbom-ci-pipeline}

When the pipeline is run, the `BOM` is added as an attachment to the `com.ibm.code_bom_check` evidence in both `standard` and `cyclonedx` format.
{: note}

The `sbom-utility` tool validates the software bill of materials (SBOM) by completing a series of default and custom checks to see whether the metadata contains:
    * A component at the root level of the SBOM that contains data
    * A timestamp
    * A `name` and `bom-ref` for each component field
    
In order to collect evidence for sbom validation, set `sbom-validation-collect-evidence` to `1`.The evidence is collected as an attachment to `com.ibm.code_bom_check` as evidence type and tool type as `sbom-utility`. 
{: note}

For more information, see the [`sbom-utility` tool docs](https://github.com/CycloneDX/sbom-utility/blob/main/sbom-validation-tests.md). 

## Continuous deployment pipeline
{: #generate-cyclonedx-sbom-cd-pipeline}

The continuous deployment pipeline can collect the CycloneDX SBOM attachment of every asset that is being deployed by the run. The pipeline merges these CycloneDX SBOMs and uploads the aggregated CycloneDX SBOM to the change request as an attachment.

After the pipeline is run, the change request has an attachment that is named `CycloneDX SBOM` that can be inspected on the change management providers UI.
{: note}

## Continuous compliance pipeline
{: #generate-cyclonedx-sbom-cc-pipeline}

During the CC summarization the SBOM attachment is treated as follows:

- If an SBOM attachment is found for the evidence type `com.ibm.code_bom_check`, the attachment is downloaded and it is used for the CC evidence for `com.ibm.code_bom_check`. Fresh SBOM regeneration does not happen.
- If an SBOM attachment is not found for the evidence type `com.ibm.code_bom_check`, then `ibmcloud cra bom-generate` is run to generate the fresh BOM at that point in both `standard` and `cyclonedx` format. This freshly-created `BOM` is used for the CC evidence for `com.ibm.code_bom_check`.

In order to collect evidence for sbom validation, set `sbom-validation-collect-evidence` to 1. The evidence will be collected as an attachment to `com.ibm.code_bom_check` as evidence type and tool type as `sbom-utility`.  
{: note}

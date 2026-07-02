---

copyright:
  years: 2026
lastupdated: "2026-05-21"

keywords: DevSecOps, IBM Cloud, compliance, SBOM, Syft, Grype

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Configuring Syft and Grype scans
{: #cd-devsecops-syft-gryp-scans}

## Overview
{: #cd-devsecops-syft-gryp-overview}

With Code Risk Analyzer (CRA) being [deprecated](https://cloud.ibm.com/status/announcement?component=continuous-delivery&query=cra), Syft and Grype scans are alternatives that provide most of the CRA features.

This scan is part of the compliance checks stage available in the PR (app-preview), CI and CC pipelines.
{: note}

### Syft
{: #cd-devsecops-syft}

[Syft](https://oss.anchore.com/docs/guides/sbom/getting-started/) is a CLI tool for generating an SBOM from container images and filesystems.

A Software Bill of Materials (SBOM) is a detailed list of all libraries and components that make up software.

To learn more about SBOMs see [Software Bill of Materials](/docs/devsecops?topic=devsecops-devsecops-sbom).
{: shortdesc}

### Grype
{: #cd-devsecops-grype}

[Grype](https://oss.anchore.com/docs/guides/vulnerability/getting-started/) is a CLI tool for scanning container images, filesystems, and SBOMs for known vulnerabilities.

Vulnerability scanning is the process of identifying known security vulnerabilities in software packages and dependencies.
{: shortdesc}

### Enabling and configuring Syft and Grype scans
{: #cd-devsecops-enabling-configuring-syft-grype-scans}

To enable Syft SBOM generation and Grype SBOM validation, add `opt-in-syft-grype` as a text property to your pipeline or trigger properties, with a value set to a non-empty string (except `0`).

Enabling this feature runs both [Syft](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-commons/blob/master/syft-grype/run-syft.sh) and [Grype](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-commons/blob/master/syft-grype/run-grype.sh) scripts triggered from the compliance checks script [Commons Scripts Library documentation](https://us-south.git.cloud.ibm.com/open-toolchain/blob/eabe91b937279a6d3b83ad57e4511791c9ec179d/compliance-checks/run.sh#L201). The scripts automatically install Syft and Grype if not already present.

#### Syft parameters
{: #cd-devsecops-syft-params}

The pipeline environment properties that are listed in the following table are used to customize the Syft SBOM generation.

| Parameter name | Description |
|-|-|
| `app-name` | Name of the target being analyzed by Syft. This value is used as the source name in the generated SBOM. |
| `syft-exclude` | Comma-separated list of paths to exclude from SBOM generation. Example: `node_modules,test,*.log`. |
| `syft-print-summary-as-table` | Set to any non-empty string to print the Syft scan summary as a table in the log output. |
| `syft-<XXX>` | Any pipeline or trigger text property prefixed with `syft-` (except the reserved properties above) is exported as a Syft Configuration Environment variable `SYFT_XXX`. For example, `syft-file-metadata-cataloger-enabled` becomes `SYFT_FILE_METADATA_CATALOGER_ENABLED`. See [Syft Configuration](https://oss.anchore.com/docs/reference/configuration/#using-environment-variables) for available options. |
{: caption="Syft parameters" caption-side="top"}

#### Syft evidence and attachments
{: #cd-devsecops-syft-evid-attach}

The DevSecOps pipeline uploads evidence to the locker and includes the evidence in the evidence summary for Change Requests.

| Field | Value |
| ----- | ----- |
| `tool type`     | `syft` |
| `evidence type` | `com.ibm.code_bom_check` |
| `asset type`    | `repo` |
| `attachments`   | `syft_sbom.json` (CycloneDX JSON format) |
{: caption="Syft evidence fields and values" caption-side="top"}

#### Grype parameters
{: #cd-devsecops-grype-params}

The pipeline environment properties that are listed in the following table are used to customize the Grype vulnerability scanning.

| Parameter name | Description |
|-|-|
| `grype-fail-on-severity` | Set the return code to 1 if a vulnerability is found with a severity greater than or equal to the given severity level. Valid values: `negligible`, `low`, `medium`, `high`, `critical`. Default: `low`. |
| `grype-<XXX>` | Any pipeline or trigger text property prefixed with `grype-` (except the reserved properties above) is exported as a Grype Configuration Environment variable `GRYPE_XXX`. For example, `grype-db-auto-update` becomes `GRYPE_DB_AUTO_UPDATE`. See [Grype Configuration](https://oss.anchore.com/docs/reference/configuration/#using-environment-variables) for available options. |
{: caption="Grype parameters" caption-side="top"}

#### Grype evidence and attachments
{: #cd-devsecops-grype-evid-attach}

The DevSecOps pipeline uploads evidence to the locker and includes the evidence in the evidence summary for Change Requests. Compliance incidents are created for vulnerabilities found during the scan.

| Field | Value |
| ----- | ----- |
| `tool type`     | `grype` |
| `evidence type` | `com.ibm.code_vulnerability_scan` |
| `asset type`    | `repo` |
| `attachments`   | `<repo-key>_grype_report_extended.json` (Grype vulnerability report with SBOM dependencies) |
{: caption="Grype evidence fields and values" caption-side="top"}

## Accessing your scan results
{: #cd-devsecops-syft-gryp-results}

You can access your scan results by using the following method:

- Using the [DevSecOps/CoCoa CLI](/docs/devsecops?topic=devsecops-cd-devsecops-cli) command line tool to download your scan results from the evidence locker by using the information printed in the stage log.  For more information, see the following resources:
   - [`cocoa locker evidence get`](/docs/devsecops?topic=devsecops-cd-devsecops-cli#locker-evidence-get)
   - [`cocoa locker attachment get`](/docs/devsecops?topic=devsecops-cd-devsecops-cli#locker-attachment-get)

## Related links
{: #devsecops-syft-grype-links}

* [Syft](https://oss.anchore.com/docs/guides/sbom/getting-started/){: external}
* [Grype](https://oss.anchore.com/docs/guides/vulnerability/getting-started/){: external}
* [Opting out of Code Risk Analyzer scans](/docs/devsecops?topic=devsecops-cd-devsecops-cra-scans#optout-cra-scans)


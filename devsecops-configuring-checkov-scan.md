---

copyright:
  years: 2026
lastupdated: "2026-05-21"

keywords: DevSecOps, IBM Cloud, compliance, Checkov

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Configuring Checkov scans
{: #cd-devsecops-checkov-scans}

## Overview
{: #cd-devsecops-checkov-overview}

[Checkov](https://www.checkov.io){: external} is a static code analysis tool for infrastructure-as-code (IaC). It scans cloud configurations for security and compliance misconfigurations. It ensures:
- Infrastructure-as-code is aligned with security best practices.
- Scans for issues like open ports, hardcoded secrets, excessive privileges, and insecure configurations.
- Works across cloud platforms and container orchestration systems, helping to avoid security risks in the deployment process.

This scan is part of the compliance checks stage available in the PR (app-preview), CI, and CC pipelines.
{: note}

### Enabling and configuring Checkov scans
{: #cd-devsecops-enabling-configuring-checkov-scans}

You can run Checkov scans using two different frameworks:
- **Terraform Plan**: Run Checkov scan on a computed Terraform plan. To enable this, add `opt-in-checkov` as a text property to your pipeline or trigger properties, with a value set to a non-empty string (except `0`).
- **Kubernetes**: Run Checkov scan on Kubernetes manifests. To enable this, add `opt-in-checkov-kubernetes` as a text property to your pipeline or trigger properties, with a value set to a non-empty string (except `0`). 
**Note**: With Code Risk Analyzer (CRA) being [deprecated](https://cloud.ibm.com/status/announcement?component=continuous-delivery&query=cra), Checkov Kubernetes scan is an alternative for `cra-deploy-analysis` that produces `com.ibm.code_cis_check` evidences.


Enabling these features runs the following scripts from the compliance checks stage:
- [Checkov Scan](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-commons/blob/master/compliance-checks/compliance-check-scans/checkov-scan.sh) (runs Terraform plan scan)
- [Checkov Kubernetes Scan](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-commons/blob/master/compliance-checks/compliance-check-scans/checkov-kubernetes-scan.sh) (runs Kubernetes scan)

These scripts automatically install Checkov if it is not already present in the environment.

#### Checkov parameters
{: #cd-devsecops-checkov-params}

The pipeline environment properties and secrets listed in the following table are used to customize the Checkov scans.

| Parameter name | Description |
|-|-|
| `checkov-args` | Additional arguments provided directly to the `checkov` command. |
| `tf-dir` | Location or path in the source repository where `main.tf` is located. (Defaults to `.`) |
| `checkov-version` | Checkov version to install if not already available in the environment. (Defaults to installing the latest version) |
| `checkov-prisma-api-url` | The Prisma Cloud API URL. Must be a `*.prismacloud.io`, `*.prismacloud.cn` or `*.bridgecrew.cloud` domain. |
| `checkov-bc-api-key` | Bridgecrew API key or Prisma Cloud Access Key. Retrieve this using `get_secret`. |
{: caption="Checkov parameters" caption-side="top"}

#### Checkov evidence and attachments
{: #cd-devsecops-checkov-evid-attach}

The DevSecOps pipeline uploads evidence to the locker and includes the evidence in the evidence summary for Change Requests.

##### Checkov Terraform Plan Evidence
{: #cd-devsecops-checkov-tf-evidence}

The following table lists the evidence details for the Terraform Plan checkov scan.

| Field | Value |
| ----- | ----- |
| `tool type`     | `checkov` |
| `evidence type` | `com.ibm.code_vulnerability_scan` |
| `asset type`    | `repo` |
| `attachments`   | Checkov results JSON file |
{: caption="Checkov Terraform Plan evidence fields and values" caption-side="top"}

##### Checkov Kubernetes Evidence
{: #cd-devsecops-checkov-k8s-evidence}

The following table lists the evidence details for the Kubernetes checkov scan.

| Field | Value |
| ----- | ----- |
| `tool type`     | `checkov` |
| `evidence type` | `com.ibm.code_cis_check` |
| `asset type`    | `repo` |
| `attachments`   | Checkov results JSON file |
{: caption="Checkov Kubernetes evidence fields and values" caption-side="top"}

## Accessing your scan results
{: #cd-devsecops-checkov-results}

You can access your scan results by using the following method:

- Using the [DevSecOps/CoCoa CLI](/docs/devsecops?topic=devsecops-cd-devsecops-cli) command line tool to download your scan results from the evidence locker by using the information printed in the stage log. For more information, see the following resources:
   - [`cocoa locker evidence get`](/docs/devsecops?topic=devsecops-cd-devsecops-cli#locker-evidence-get)
   - [`cocoa locker attachment get`](/docs/devsecops?topic=devsecops-cd-devsecops-cli#locker-attachment-get)

## Related links
{: #devsecops-checkov-links}

* [Checkov Documentation](https://www.checkov.io){: external}
* [Bridgecrew / Prisma Cloud](https://prisma.pan.dev/api/cloud/api-urls){: external}
* [Opting out of Code Risk Analyzer scans](/docs/devsecops?topic=devsecops-cd-devsecops-cra-scans#optout-cra-scans)

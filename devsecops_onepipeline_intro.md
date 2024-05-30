---

copyright:
  years: 2021, 2024
lastupdated: "2024-05-30"

keywords: DevSecOps

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}


# OnePipeline and DevSecOps
{: #OnePipeline_intro}

[OnePipeline]{: tag-blue} [Internal]{: tag-red}

`OnePipeline` helps streamline internal compliance audits by standardizing CI/CD processes for services adhering to the IBM Cloud Service Framework (see also the [reference implementation workflow](/docs/devsecops?topic=devsecops-cd-devsecops-arch)). OnePipeline provides a set of predefined CI/CD templates kept current as the Service Framework addresses FS-Cloud, then FedRAMP and more in the future.


## DevSecOps vs OnePipeline {{site.data.keyword.contdelivery_short}}
{: #OnePipeline_vs_devsecops}

### Overview
{: #OnePipeline_overview}

`OnePipeline` and `DevSecOps` both use the **same** reference implementation. Process and tools are mostly **identical**, except for repositories, change management tool, and artifact signing.

This means that both flavors are equivalent in terms of **FS-Cloud** and **FedRAMP compliance**.

### Details
{: #devsecops_onepipeline_difference}

|                    | OnePipeline (Internal)                                                      | DevSecOps (External)                |
|--------------------|--------------------------------------------------------------------------------------|-----------------------------------------------------|
| **Change management**          | ServiceNow                                   | Git Repo and Issues Tracking (GRIT)  |
| **Repository**     | github.ibm.com (*)                        |  Git Repo and Issues Tracking (GRIT) (*)                                   |
| **Artifact signing**       |   Garasign   |GPG (GNU Privacy Guard) |
{: row-headers}
{: caption=" Table 1. Features differences between OnePipeline (Internal) and DevSecOps(External)" caption-side="bottom"}
{: summary="The first row of the table describes separate options that you can choose to your required service. The remaining cells provide the major differences between DevSecOps and OnePipeLine."}
(*) Adopters may use a mix of other repository providers like Gitlab, Bitbucket and more. Refer to the [documentation](/docs/ContinuousDelivery?topic=ContinuousDelivery-integrations&interface=ui).
{: #diffdevsecopsstable1}



## Security and Compliance Features of OnePipeLine / DevSecOps
{: #scc-features-onepipeline-table}

| Feature | Pull Request(PR) Pipeline  | Continuous Integration (CI) Pipeline  | Continuous Delivery (CD) Pipeline   |Continuous Compliance (CC) Pipeline |
|-----|-----|-----|-----|-----|
| Branch Protection | ![Checkmark icon](../icons/checkmark-icon.svg) | ![Checkmark icon](../icons/checkmark-icon.svg) |  |  |
| Secret Detection | ![Checkmark icon](../icons/checkmark-icon.svg) | ![Checkmark icon](../icons/checkmark-icon.svg) |  |  |
| Code Peer Review| ![Checkmark icon](../icons/checkmark-icon.svg) | ![Checkmark icon](../icons/checkmark-icon.svg) |  |  |
| Open Source Scanning | ![Checkmark icon](../icons/checkmark-icon.svg) [^tabletext] | ![Checkmark icon](../icons/checkmark-icon.svg) [^tabletext1a] | | ![Checkmark icon](../icons/checkmark-icon.svg) [^tabletext1b] |
| Static Scan (SAST) | ![Checkmark icon](../icons/checkmark-icon.svg) | ![Checkmark icon](../icons/checkmark-icon.svg) [^tabletext2] | | ![Checkmark icon](../icons/checkmark-icon.svg) [^tabletext2a]  |
| Dynamic Scans (DAST) | ![Checkmark icon](../icons/checkmark-icon.svg) | ![Checkmark icon](../icons/checkmark-icon.svg) [^tabletext3]  | | ![Checkmark icon](../icons/checkmark-icon.svg) [^tabletext3a]  |
| Interactive Scan (IAST) | | ![Checkmark icon](../icons/checkmark-icon.svg) [^tabletext2b]  | | ![Checkmark icon](../icons/checkmark-icon.svg) [^tabletext2c]  |
| Generate Individual SBOM | ![Checkmark icon](../icons/checkmark-icon.svg) | | |  |
| Container Image Scans | | ![Checkmark icon](../icons/checkmark-icon.svg) [^tabletext5]| | ![Checkmark icon](../icons/checkmark-icon.svg) [^tabletext5a] |
| Artifact Signing | | ![Checkmark icon](../icons/checkmark-icon.svg) | | |
| Signature Validation | | | ![Checkmark icon](../icons/checkmark-icon.svg) |  |
| Generate Aggregate SBOM | | | ![Checkmark icon](../icons/checkmark-icon.svg) |  |
| Change Management| | | ![Checkmark icon](../icons/checkmark-icon.svg) |  |
| Vulnerability Remediation | | | | ![Checkmark icon](../icons/checkmark-icon.svg) |
| Issue Management | | ![Checkmark icon](../icons/checkmark-icon.svg)| | ![Checkmark icon](../icons/checkmark-icon.svg) |

[^tabletext]:Code Risk Analyzer, Mend

[^tabletext1a]:Code Risk Analyzer, Mend

[^tabletext1b]:Code Risk Analyzer, Mend

[^tabletext2]: Contrast or SonarQube*

[^tabletext2a]: Contrast or SonarQube*

[^tabletext2b] : Contrast**

[^tabletext2c] : Contrast**

[^tabletext3]: OWASP ZAP

[^tabletext3a]: OWASP ZAP

[^tabletext5]: AquaTrivy, Twistlock, Vulnerability Advisor

[^tabletext5a]: AquaTrivy, Twistlock, Vulnerability Advisor
{: caption="Security and Compliance features available in OnePipeline "}
{: summary="This table has row and column headers. The row headers identify the various components. The column headers identify the features."}
{: #table01}
{: class="comparison-tab-table"}
{: row-headers}

Refer to this [DevSecOps Pipelines stages and custom scripts explained](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-stages) for a complete list of `OnePipeline` and `DevSecOps` Pipelines stages and custom scripts.

## Supported scanning tools
{: #supported-scanning-tools}

Refer to this [Supported scanning tools](/docs/devsecops?topic=devsecops-cd-devsecops-supported-scanning-tools) for a complete list of supported scanning tools.

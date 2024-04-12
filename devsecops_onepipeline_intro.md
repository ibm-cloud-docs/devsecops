---

copyright:
  years: 2022, 2024
lastupdated: "2024-04-12"

keywords: IBM Cloud DevOps, DevSecOps

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# OnePipeline and DevSecOps
{: #OnePipeline_intro}

OnePipeline helps streamline internal compliance audits by standardizing CI/CD processes for services adhering to the IBM Cloud Service Framework (see also System architecture). OnePipeline provides a set of predefined CI/CD templates kept current as the Service Framework addresses FS-Cloud, then FedRAMP and more in the future.


## DevSecOps vs OnePipeline {{site.data.keyword.contdelivery_short}}
{: #devsecops_onepipeline_difference}

|                    | OnePipeline (Internal)                                                      | Devsecops (External)                |
|--------------------|--------------------------------------------------------------------------------------|-----------------------------------------------------|
| **Tools**          | Change management tool used here is Service Now                                   | Change management tool used here is  Github  |
| **Repository**     | github.ibm.com                         |  Git Repo and Issues Tracking (GRIT)                                    |
| **Artefact sign-in**       |   Garasign   |GPG (GNU Privacy Guard) key|
{: row-headers}
{: caption=" Table 1. Features differences between OnePipeline (Internal) and DevSecOps(External)" caption-side="bottom"}
{: summary="The first row of the table describes separate options that you can choose to your required service. The remaining cells provide the major differences between DevSecOps and OnePipeLine."}
{: #diffdevsecopsstable1}



## Security and Compliance Features of OnePipeLine
{: #scc-features-onepipeline-table}

| Features | Pull Request(PR) Pipeline  | Continuous Integration (CI) Pipeline  | Continuous Delivery (CD) Pipeline   |Continuous Compliance (CC) Pipeline |
|-----|-----|-----|-----|-----|
| Branch Protection | ![Checkmark icon](../icons/checkmark-icon.svg) | ![Checkmark icon](../icons/checkmark-icon.svg) |  |  |
| Secret Detection | ![Checkmark icon](../icons/checkmark-icon.svg) | ![Checkmark icon](../icons/checkmark-icon.svg) |  |  |
| Code Peer Review| ![Checkmark icon](../icons/checkmark-icon.svg) | ![Checkmark icon](../icons/checkmark-icon.svg) |  |  |
| Open Source Scanning | ![Checkmark icon](../icons/checkmark-icon.svg) [^tabletext] | ![Checkmark icon](../icons/checkmark-icon.svg) [^tabletext] | | ![Checkmark icon](../icons/checkmark-icon.svg) [^tabletext] |
| Static Scan (SAST) | ![Checkmark icon](../icons/checkmark-icon.svg) | ![Checkmark icon](../icons/checkmark-icon.svg) [^tabletext2]**  | | ![Checkmark icon](../icons/checkmark-icon.svg) [^tabletext2]**  |
| Dynamic Scans (DAST) | ![Checkmark icon](../icons/checkmark-icon.svg) | ![Checkmark icon](../icons/checkmark-icon.svg) [^tabletext3]**  | | ![Checkmark icon](../icons/checkmark-icon.svg) [^tabletext3]**  |
| Interactive Scan (IAST) | | ![Checkmark icon](../icons/checkmark-icon.svg) [^tabletext2]**  | | ![Checkmark icon](../icons/checkmark-icon.svg) [^tabletext2]** | |  |
| Generate Individual SBOM | ![Checkmark icon](../icons/checkmark-icon.svg) | | |  |
| Container Image Scans | | ![Checkmark icon](../icons/checkmark-icon.svg) [^tabletext4]** | | ![Checkmark icon](../icons/checkmark-icon.svg) [^tabletext4]** |
| Artifact Signing | | ![Checkmark icon](../icons/checkmark-icon.svg) | | |
| Signature Validation | | | ![Checkmark icon](../icons/checkmark-icon.svg) |  |
| Generate Aggregate SBOM | | | ![Checkmark icon](../icons/checkmark-icon.svg) |  |
| Change Management| | | ![Checkmark icon](../icons/checkmark-icon.svg) |  |
| Vulnerability Remediation | | | | ![Checkmark icon](../icons/checkmark-icon.svg) |
| Issue Management | | ![Checkmark icon](../icons/checkmark-icon.svg)| | ![Checkmark icon](../icons/checkmark-icon.svg) |

[^tabletext]:Code Risk Analyzer, Mend.

[^tabletext2]: Contrast or SonarQube*

[^tabletext3]: OWASP ZAP

[^tabletext4]: AquaSec, Twistlock, Vulnerability Advisor
{: caption="Security and Compliance features available in OnePipeline "}
{: summary="This table has row and column headers. The row headers identify the various components. The column headers identify the features."}
{: #table01}
{: class="comparison-tab-table"}
{: row-headers}

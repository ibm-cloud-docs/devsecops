---

copyright:
  years: 2023, 2025
lastupdated: "2025-02-25"

keywords: DevSecOps, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Supported scanning tools
{: #cd-devsecops-supported-scanning-tools}

The following table lists the various scanning tools that are integrated into DevSecOps pipelines to provide continuous security checks and monitoring. These scans run at various stages of the Continuous Integration (CI), Continuous Development (CD), and Continuous Deployment (CC) pipelines.
{: shortdesc}



| Tool | Scan | Description | Scan type |
|-|-|-|-|
| [IBM Cloud Code Risk Analyzer](/docs/devsecops?topic=devsecops-cd-devsecops-cra-scans) | Code Risk Analyzer (CRA) analyzes your code for vulnerabilities and compliance with certain rules. | `compliance checks` stage of CI/CC pipelines | Dependency scan |

| [Syft and Grype](/docs/devsecops?topic=devsecops-cd-devsecops-syft-gryp-scans) | Syft and Grype analyze your code for vulnerabilities and compliance with certain rules. | `compliance checks` stage of PR app preview/CI/CC pipelines | Dependency scan |

| [Gosec](/docs/devsecops?topic=devsecops-devsecops-gosec) | Gosec scan can be used to inspect Golang source code in your scanned repositories. | `static scan` stage of CI/CC pipelines | Static scan (SAST) |

| [SonarQube](/docs/devsecops?topic=devsecops-sonarqube) | SonarQube provides an overview of the overall health and quality of your source code and highlights issues that are found in new code. | `static scan` stage of CI/CC pipelines | Static scan (SAST) |
| [OWASP ZAP](/docs/devsecops?topic=devsecops-cd-devsecops-zap-scans) | Zed Attack Proxy (ZAP) is a free and open source penetration testing tool that is maintained under the umbrella of OWASP. | `owasp zap` sub-pipeline in CI pipeline and `dynamic scan` stage of CI/CC pipelines | Dynamic scan (DAST) |

| [IBM Cloud Vulnerability Advisor](/docs/devsecops?topic=devsecops-cd-devsecops-va-scans) | The DevSecOps pipeline uses the Vulnerability Advisor (VA) to identify vulnerabilities (CVEs) within Docker images. | `scan artifact` stage of CI/CC pipelines | Container image scan |
| [Sysdig](/docs/devsecops?topic=devsecops-cd-devsecops-sysdig-scans) | Sysdig scan uses the Sysdig inline scanner to identify vulnerabilities (CVEs) within Docker images. | `scan artifact` stage of CI/CC pipelines | Containerized image scan |

| [Detect Secrets](/docs/devsecops?topic=devsecops-cd-devsecops-detect-secrets-scans) | Detect-secrets is a client-side security tool that detects secrets within a codebase to remediate and prevent secret leaks. | `detect secrets` stage of CI/CC pipelines | Secrets scan |
| [SLSA attestation](/docs/devsecops?topic=devsecops-cd-devsecops-slsa) | SLSA attestation is provided to validate the supply chain security and provide provenance of build. | `build artifact` stage of CI/CC pipelines | Supply chain security |
{: caption="List of supported scanning tools" caption-side="top"}

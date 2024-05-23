---

copyright:
  years: 2023, 2024
lastupdated: "2024-05-22"

keywords: DevSecOps, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Supported scanning tools
{: #cd-devsecops-supported-scanning-tools}

The following table lists the various scanning tools that are integrated into DevSecOps pipelines to provide continuous security checks and monitoring. These scans run at various stages of the Continuous Integration (CI), Continuous Development (CD), and Continuous Deployment (CC) pipelines.
{: shortdesc}

| Type of scan | Scan | Description | Stage |
|-|-|-|-|
| [IBM Cloud Code Risk Analyzer](/docs/devsecops?topic=devsecops-cd-devsecops-cra-scans) | Code Risk Analyzer (CRA) analyzes your code for vulnerabilities and compliance with certain rules. | `compliance checks` stage of CI/CC pipelines | Static scan |<staging>
| [IBM Cloud Code Risk Analyzer](/docs/devsecops?topic=devsecops-cd-devsecops-cra-scans) | Code Risk Analyzer (CRA) analyzes your code for vulnerabilities and compliance with certain rules. | `compliance checks` stage of CI/CC pipelines | Static scan |
| [Contrast SAST](/docs/devsecops?topic=devsecops-devsecops-contrast-sast-scans) | The Contrast static application security testing (SAST) script runs the source code scanning in your DevSecOps pipeline | `static scan` stage of CI/CC pipelines | Static scan |
| [Detect Secrets](/docs/devsecops?topic=devsecops-cd-devsecops-detect-secrets-scans) | Detect-secrets is a client-side security tool that detects secrets within a codebase to remediate and prevent secret leaks. | `detect secrets` stage of CI/CC pipelines | Static Scan |
| [FIPS](/docs/devsecops?topic=devsecops-cd-devsecops-fips-scans) | Federal Information Processing Standard (FIPS) scanner is a static code scanner that scans the source code | `static scan` stage of CI/CC pipelines | Static scan |
| [Gosec](/docs/devsecops?topic=devsecops-devsecops-gosec) | Gosec scan can be used to inspect Golang source code in your scanned repositories. | `static scan` stage of CI/CC pipelines | Static scan |
| [Mend](/docs/devsecops?topic=devsecops-cd-devsecops-mend-scans) | The Mend script runs the Mend Unified Agent dependency scan in your DevSecOps pipeline. | `compliance checks` stage of CI/CC pipelines | Static scan |
| [Sonarqube](/docs/devsecops?topic=devsecops-sonarqube) | SonarQube provides an overview of the overall health and quality of your source code and highlights issues that are found in new code. | `static scan` stage of CI/CC pipelines | Static scan |
| [Owasp Zap](/docs/devsecops?topic=devsecops-cd-devsecops-zap-scans) | Zed Attack Proxy (ZAP) is a free and open source penetration testing (PEN) tool that is maintained under the umbrella of OWASP. | `owasp zap`sub pipeline in CI pipeline and `dynamic scan` stage of CI/CC pipelines | Dynamic Scan |
| [Sysdig](/docs/devsecops?topic=devsecops-cd-devsecops-sysdig-scans) | Sysdig scan uses the Sysdig inline scanner to identify vulnerabilities (CVEs) within Docker images. | `scan artifact` stage of CI/CC pipelines | Container Image scan |
| [IBM Cloud Vulnerability Advisor](/docs/devsecops?topic=devsecops-cd-devsecops-va-scans) | The DevSecOps pipeline uses the Vulnerability Advisor (VA) to identify vulnerabilities (CVEs) within Docker images. | `scan artifact` stage of CI/CC pipelines | Container Image scan |
| [CIMS](docs/devsecops?topic=devsecops-cd-devsecops-cims#results-filtering-properties) | The Container Image Multi-scanner integrates multiple third-party and open source container image scanning tools and correlates their results to identify vulnerabilities (CVEs) within Docker images. | `scan artifact` stage of CI/CC pipelines | Container Image scan |
| [SLSA attestation](/docs/devsecops?topic=devsecops-cd-devsecops-slsa) | SLSA attestation is provided to validate the supply chain security and provide provenance of build | `build artifact` stage of CI/CC pipelines | Container Image scan |
| [Xray](/docs/devsecops?topic=devsecops-cd-devsecops-xray-scans) | XRay scan is used to identify vulnerabilities of container images that are in the artifactory | `scan artifact` stage of CI/CC pipelines | Artifactory Image scan |
{: caption="Table 1. List of supported scanning tools" caption-side="top"}

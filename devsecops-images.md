---

copyright:
  years: 2022, 2023
lastupdated: "2023-02-07"

keywords: DevSecOps, images, IBM Cloud, OWASP ZAP, Sonarqube

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Images used by DevSecOps pipelines
{: #devsecops-images}

The DevSecOps Continuous Integration (CI), Continuous Deployment (CD), and Continuous Compliance (CC) pipelines use Docker images for different tools to run the scans within the pipeline runs. The images are available in the Global IBM Container Registry (ICR) and are pulled and run during the pipeline runs.
{: shortdesc}

1. DevSecOps Baseimage - Used to run the compliance pipelines. This image runs all the stages within the pipelines, triggering different scans, collecting evidence, and creating issues.
1. OWASP ZAP API Scanner - Used to trigger OWASP ZAP API scans against the API endpoints of a deployed application to detect vulnerabilities.
1. OWASP ZAP UI Scanner - Used to trigger OWASP ZAP UI scans against the URL of a deployed application to detect vulnerabilities exposed by the UI.
1. OWASP ZAP UI Proxy - Used with the OWASP ZAP UI scan image as a proxy service to trigger ZAP UI scans against a deployed application.
1. Detect Secrets - Used to detect secrets like passwords or secret keys that are exposed by the application source code.
1. Docker In Docker (DinD) - Used to run a docker container within another docker container. DinD runs various images that belong to tools within the DevSecOps Baseimage. 
1. Sonarqube - Used to run static scan against application source code to detect vulnerabilities.
1. Sonarqube Scanner CLI - Command-line utility to trigger SonarQube scans.

|Tool Type | IBM Cloud Container Registry Repository | Latest version	| Vulnerability Status | Vulnerability Description |
|:----------|:----------|:----------|:----------|:----------|
|Baseimage| icr.io/continuous-delivery/toolchains/devsecops/devsecops-baseimage| 2.70.0_commons-0.18.0 | Vulnerable | CVE-2022-40304, CVE-2022-40303, CVE-2022-35737, CVE-2021-46848, CVE-2022-3821, CVE-2022-43680 |
|DevSecOps Baseimage| icr.io/continuous-delivery/toolchains/devsecops/devsecops-baseimage| 2.70.0_commons-0.18.0 | Vulnerable | CVE-2021-46848, CVE-2022-40303, CVE-2022-40304, CVE-2022-35737, CVE-2022-43680, CVE-2022-3821 |
|OWASP ZAP API Scanner|icr.io/continuous-delivery/toolchains/devsecops/owasp-zap-api-scanner| 30-08-2022-13-13 | Vulnerable |   |
|OWASP ZAP UI Scanner|icr.io/continuous-delivery/toolchains/devsecops/owasp-zap-ui-scanner| 13-04-2022-11-16 | Vulnerable |  |
|OWASP ZAP UI Proxy|icr.io/continuous-delivery/toolchains/devsecops/owasp-zap-ui-proxy| 14-04-2022-13-40 | Vulnerable |  |
|Detect Secrets|icr.io/continuous-delivery/toolchains/devsecops/detect-secrets| 0.13.1.ibm.55.dss| Vulnerable | CVE-2022-43680, CVE-2022-47629, CVE-2022-40303, CVE-2022-40304, CVE-2022-23521, CVE-2022-24765, CVE-2022-29187, CVE-2022-39253, CVE-2022-39260, CVE-2022-41903, CVE-2022-32221, CVE-2022-43552, |
|Docker In Docker|icr.io/continuous-delivery/toolchains/devsecops/docker| 20.10.21-dind  | Vulnerable | CVE-2022-42898 |
|SonarQube|icr.io/continuous-delivery/toolchains/devsecops/sonarqube| 9.7.1-community  | Vulnerable | CVE-2022-21540, CVE-2022-21541, CVE-2022-21549, CVE-2022-21619, CVE-2022-21624, CVE-2022-21626, CVE-2022-21628, CVE-2022-25647, CVE-2022-34169, CVE-2022-39399, CVE-2023-21835, CVE-2023-21843, CVE-2021-46848 |
|SonarQube Scanner CLI|icr.io/continuous-delivery/toolchains/devsecops/sonar-scanner-cli | 4.7.0 | Vulnerable | CVE-2021-46848, CVE-2022-43551, CVE-2022-43552, CVE-2022-23521, CVE-2022-41903, CVE-2023-21835, CVE-2023-21843, CVE-2022-45061 |
{: caption="Table 1. Sonarqube Scanner CLI" caption-side="bottom"}

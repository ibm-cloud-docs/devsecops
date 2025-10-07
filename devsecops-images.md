---

copyright: 
  years: 2021, 2025
lastupdated: "2025-10-07"

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
|Baseimage| icr.io/continuous-delivery/toolchains/devsecops/baseimage| 3.118.1_commons-1.56.0 | Vulnerable |  |
|DevSecOps Baseimage| icr.io/continuous-delivery/toolchains/devsecops/devsecops-baseimage | 3.118.1_commons-1.56.0 | Vulnerable | |
|OWASP ZAP API Scanner|icr.io/continuous-delivery/toolchains/devsecops/owasp-zap-api-scanner| 06-08-2025-11-21 | Vulnerable |   |
|OWASP ZAP UI Scanner|icr.io/continuous-delivery/toolchains/devsecops/owasp-zap-ui-scanner| 15-05-2025-05-43 | Vulnerable |  |
|OWASP ZAP UI Proxy|icr.io/continuous-delivery/toolchains/devsecops/owasp-zap-ui-proxy| 15-05-2025-05-36 | Vulnerable |  |
|Detect Secrets|icr.io/git-defenders/detect-secrets| 0.13.1.ibm.62.dss-redhat-ubi | Vulnerable | |
|Docker In Docker|icr.io/continuous-delivery/base-images/multiarch-dind| v1.8.0 | Vulnerable |  |
|SonarQube|icr.io/continuous-delivery/toolchains/devsecops/sonarqube| 25.5.0.107428-community  | Vulnerable | |
|SonarQube Scanner CLI|icr.io/continuous-delivery/toolchains/devsecops/sonar-scanner-cli | 09-05-2025-12-26 | Vulnerable | |
{: caption="DevSecOps images" caption-side="bottom"}

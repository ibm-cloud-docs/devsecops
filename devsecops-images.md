---

copyright:
  years: 2022
lastupdated: "2022-04-25"

keywords: DevSecOps, images, IBM Cloud, OWASP ZAP, Sonarqube

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Images used by DevSecOps pipelines
{: #devsecops-images}

The DevSecOps Continuous Integration (CI), Continuous Deployment (CD), and Continuous Compliance (CC) pipelines use Docker images for different tools to run the scans within the pipeline runs. The images are available in the Global IBM Container Registry (ICR) and are pulled and run during the pipeline runs.
{: shortdesc}

1. DevSecOps Baseimage - Used to run the compliance pipelines. This image runs all the stages within the pipelines, triggering different scans, collecting evidences and creating issues.

2. OWASP ZAP API Scanner - Used to trigger OWASP ZAP API scans against the API endpoints of a deployed application to detect vulnerabilities.

3. OWASP ZAP UI Scanner - Used to trigger OWASP ZAP UI scans against the URL of a deployed application to detect vulnerabilities exposed by the UI.

4. OWASP ZAP UI Proxy - Used with the OWASP ZAP UI scan image as a proxy service to trigger ZAP UI scans against a deployed application.

5. Detect Secrets - Used to detect secrets like passwords or secret keys that are exposed by the application source code.

6. Docker In Docker (DinD) - Used to run a docker container within another docker container. DinD runs various images that belong to tools within the DevSecOps Baseimage. 

7. Sonarqube - Used to run static scan against application source code to detect vulnerabilities.

8. Sonarqube Scanner CLI - Command-line utility to trigger SonarQube scans.

|Tool Type | IBM Cloud Container Registry Repository | Latest version	| Vulnerability Status | Vulnerability Description |
|:----------|:----------|:----------|:----------|:----------|
|Baseimage| icr.io/continuous-delivery/toolchains/devsecops/devsecops-baseimage| 2.50.0_commons-0.5.4 | Not Vulnerable | |
|OWASP ZAP API Scanner|icr.io/continuous-delivery/toolchains/devsecops/owasp-zap-api-scanner| 31-03-2022-10-30 | Not Vulnerable | |
|OWASP ZAP UI Scanner|icr.io/continuous-delivery/toolchains/devsecops/owasp-zap-ui-scanner| 13-04-2022-11-16 | Not Vulnerable | |
|OWASP ZAP UI Proxy|icr.io/continuous-delivery/toolchains/devsecops/owasp-zap-ui-proxy| 14-04-2022-13-40 | Not Vulnerable | |
|Detect Secrets|icr.io/continuous-delivery/toolchains/devsecops/detect-secrets| 0.13.1.ibm.48.dss| Vulnerable | CVE-2021-28544, CVE-2022-24070, CVE-2018-25032 |
|Docker In Docker|icr.io/continuous-delivery/toolchains/devsecops/docker| 20.10.14-dind | Vulnerable | CVE-2022-1271 |
|SonarQube|icr.io/continuous-delivery/toolchains/devsecops/sonarqube| 9.4.0-community | Vulnerable | CVE-2022-28391 |
|SonarQube Scanner CLI|icr.io/continuous-delivery/toolchains/devsecops/sonar-scanner-cli | 4.7.0 | Vulnerable | CVE-2022-24765, CVE-2022-1271 |
{: caption="Table 1. Sonarqube Scanner CLI" caption-side="bottom"}

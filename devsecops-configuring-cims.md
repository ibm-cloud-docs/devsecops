---

copyright:
  years: 2022, 2023
lastupdated: "2023-12-11"

keywords: DevSecOps

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}


# Configuring Container Image Multi-Scanner scans
{: #cd-devsecops-cims}

To scan container images to access discovery and reports on the vulnerabilities that are present in those images, you can use the IBM-developed tool Container Image Multi-Scanner (CIMS). This scan is run as part of the scan-artifact stage of CI and CC pipelines. This scan runs for each [container image](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#pipelinectl-container-images) you have saved to the pipeline by using the save_artifact method. For more information, see [save_artifact](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#save_artifact).

The following keys are required for save_artifact to scan each image with the Container Image Multi-Scanner:

- `type`: must be set to the image
- `name`: A fully qualified name for the container image. For example, names used for pulling the image using `docker pull`
- `digest`: The sha256 digest for the container image

## Prerequistes
{: #cims-required-pipeline-environment-properties}

You're required to do the following configuration to succesfully multiscan your images:

-Set `multiscan` to `true` (or any nonempty string) to enable the image multiscan scanning.

-You can control what image scans the image multiscanner tool runs by using the following optional environment property:
 - `multiscan-tools` - List the image scan types that are run and included in the multiscan results with this property. The default value is `twistlock,va`.

   - Valid values:
     - `twistlock`
     - `aqua`
     - `va`

   -For example, in `twistlock,va` environment property- If you include the `va` scan type, CIMS pulls Vulnerability Advisor (VA) scan results from the IBM Container Registry for images that are stored in ICR (icr.io, us.icr.io, cp.icr.io, cp.stg.icr.io, and others).  VA scan results are not available for images that are in non-ICR registries such as Artifactory, Quay, and others.

When you enable the CIMS feature, VA scans are not run separately in the pipeline.
{: note}


## Overview
{: #cims-overview}

[CIMS](https://w3.ibm.com/w3publisher/cims){: external} integrates multiple third-party and open source scanning tools and correlates their results. With CIMS scans, you can view a complete picture of the vulnerabilities in an image that is typically obtained by scanning with a single tool.

The image multiscan feature uses a combined image scanning tool that is run within its own Docker container within the pipeline. CIMS combines the results of the selected image scans into an SQLite database, which it then uses to de-duplicate and correlate results. Review the following sections for details about the CIMS features.

## Onboarding
{: #cims-onboarding}

To onboard to CIMS, you need access to the CIMS container image repository. If you plan to scan with Twistlock, you must onboard to the Supply Chain Security (SCS) Twistlock service.

### Access to CIMS container scanning image
{: #cims-onboarding-image}


During each pipeline run, the pipeline pulls the CIMS container image that is hosted in Artifactory. Request access to the CIMS image repository by posting to the `#cims-onboarding` Slack channel. In the post, include the Artifactory user ID, which is to be set to the `multiscan-artifactory-user` pipeline environment property when you request access.

### Twistlock
{: #cims-onboarding-twistlock}

The [Twistlock container scanning service](https://pages.github.ibm.com/Supply-Chain-Security/ConSec-External-Docs/abouttwlk/){: external} is a Red Hat OpenShift certified scanner. This certification means that it uses Red Hat OVAL feeds (including CVE scoring data) to report the severity of Common Vulnerabilities and Exposures (CVE) and similar vulnerabilities.

Twistlock is powered by the Palo Alto Networks Prisma Cloud Compute Edition offering. The service is fully self-hosted on IBM public cloud and supported by the SCS Container Security team.

Refer to the SCS Container Security documentation to [onboard to the Twistlock service](https://pages.github.ibm.com/Supply-Chain-Security/ConSec-External-Docs/onboardtwlk/){: external}.

Twistlock server access requires a connection to the 9.x internal IBM network. IBM Managed Workers cannot be used, as they are public workers which do not have access to the 9.x network
{: note}

### Aqua Trivy
{: #cims-onboarding-aqua-trivy}

The [Aqua Trivy container scanning service](https://pages.github.ibm.com/Supply-Chain-Security/ConSec-External-Docs/aqua-vnext/){: external} is a Red Hat OpenShift certified scanner. This certification means that it uses Red Hat OVAL feeds (including CVE scoring data) to report the severity of Common Vulnerabilities and Exposures (CVE) and similar vulnerabilities.

The service is fully self-hosted on IBM public cloud and supported by the SCS Container Security team.

Access to the Aqua Trivy scanning service is limited to Cloud Pak teams. Other teams might request access on a case-by-case basis by [opening a ServiceNow request](https://ibm.service-now.com/ciso_container_security?id=ciso_container_security_home_page){: external} and following the [documentation to onboard to the Aqua Trivy service](https://pages.github.ibm.com/Supply-Chain-Security/ConSec-External-Docs/aqua-vnext/#aqua-trivy-onboarding){: external}.

Aqua Trivy server access requires a connection to the 9.x internal IBM network. IBM Managed Workers cannot be used, as they are public workers, which do not have access to the 9.x network.
{: note}

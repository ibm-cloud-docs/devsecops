---

copyright:
  years: "2024"
lastupdated: "2024-05-13"

keywords: DevSecOps, IBM Cloud, compliance, cims, multiscan, container image

subcollection: devsecops-working

---

{{site.data.keyword.attribute-definition-list}}

# Configuring scan credentials
{: #cd-devsecops-scan-credentials-config}

It is important to configure your scan credentials after onboarding. This step adds enhanced securtiy during image scans.
To configure your image scan, you need to configure the following:
- CIMS container scanning image pull credentials
- Scanning tools and scanning tools credentials
{: #shortdesc}


## CIMS container scanning image pull credentials
{: #cims-scann-image-pull}

During each pipeline run, the pipeline pulls the CIMS container image that is hosted in Artifactory. Specify the following pipeline environment properties to enable the pipeline to pull the CIMS container scanning image:

 - `multiscan-artifactory-user` - The Artifactory user email address. Set this property to the Artifactory user ID that you specified when you requested access to the CIMS container scanning image. The default value of this property is the `User ID` value from the toolchain's Artifactory tool integration.
 - `multiscan-artifactory-api-key` - The Artifactory API key. Set this property to pull the public multiscanner Docker container image from Artifactory. You can also use it to create credentials to perform a Docker pull on any Artifactory images that need to be scanned. The default value of this property is the `Access token` value from the toolchain's Artifactory tool integration.

You must successfully complete the CIMS container scanning image pull credentials onboarding step before you can run the pipeline.
{: note}


## Scanning tools and scanning tools credentials
{: #cims-scanning-tool-credentials}

With CIMS, you can configure Twistlock, Red Hat certified Aqua, and Vulnerability Advisor (VA).

### Twistlock
{: #cims-scanning-tool-credentials-twistlock}

To configure Twistlock, you must include the required properties and set the Twistlock control group.

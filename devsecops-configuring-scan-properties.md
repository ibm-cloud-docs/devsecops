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

With CIMS, you can configure Twistlock, Red Hat certified AquaTrivy, and Vulnerability Advisor (VA).

### Twistlock
{: #cims-scanning-tool-credentials-twistlock}

To configure Twistlock, you must include the required properties and set the Twistlock control group.

#### Required pipeline environment properties
{: #cims-scanning-twistlock-properties}

If you include Twistlock in `multiscan-tools`, you must define the following pipeline environment properties:

   - `twistlock-user-id` - Set this value as a full w3 username. For example, `common.cicd.for.cpp@ibm.com`.
   - `twistlock-api-key` - Set this value as the Twistlock API key for your Twistlock username, or your W3 password. After you [onboard to the Supply Chain Security (SCS) Twistlock service](https://pages.github.ibm.com/Supply-Chain-Security/ConSec-External-Docs/container-security/onboardtwlk/){: external}, you can retrieve your Twistlock API key token by [downloading the Twistlock tt CLI binary](https://pages.github.ibm.com/Supply-Chain-Security/ConSec-External-Docs/quickstart/#download-tt-client){: external}. Then, run the [`tt api-key show`](https://pages.github.ibm.com/Supply-Chain-Security/ConSec-External-Docs/usetwlk/#tt-api-key-show-h){: external} command.

#### Setting the Twistlock image type
{: #cims-scanning-twistlock-image-type}

Twistlock requires each scanned image to be designated as Production or Non-Production as part of the scan.

Set the `twistlock-image-type` pipeline environment property to set the default Twistlock image type for all container images scanned by the pipeline. The default value of this property is 'nonprod'.

Optionally, you may set a different Twistlock image type for one or more container images to be scanned. This per-image setting overrides the Twistlock image type from the `twistlock-image-type` pipeline environment property. To provide a different control group name for any images that you want to include, use the `image_type` property. Set this property to the desired Twistlock image type when you are saving the container image into the pipeline by using the [save_artifact](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#save_artifact) method.

- Valid values for the `twistlock-image-type` pipeline environment property and `image_type` property:
    - `prod`
    - `nonprod`

#### Setting the Twistlock control group
{: #cims-scanning-twistlock-control-group}

Set the `twistlock-group-id` pipeline environment property to set the default Twistlock control group for all container images scanned by the pipeline. Set this value as the Twistlock control group name that you created during the process to [onboard to the Twistlock service](https://pages.github.ibm.com/Supply-Chain-Security/ConSec-External-Docs/onboardtwlk/#container-security-boarding-portal){: external}.

Optionally, you may set a different Twistlock control group name for one or more container images to be scanned. This per-image setting overrides the Twistlock control group name from the `twistlock-group-id` pipeline environment property. To provide a different control group name for any images that you want to include, use the `twistlock_group` property. Set this property to the desired Twistlock control group name when you are saving the container image into the pipeline by using the [save_artifact](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#save_artifact) method.

If you do not set the `twistlock-group-id` pipeline environment property to set default Twistlock control group, you must set the `twistlock_group` property using [save_artifact](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#save_artifact) for every container image in your pipeline.
{: note}

### AquaTrivy
{: #cims-scanning-tool-credentials-aqua}

If you include Red Hat certified AquaTrivy scanning in `multiscan-tools`, you must define the following pipeline environment properties:

   - `aqua-user-id`
   - `aqua-password`

### Vulnerability Advisor (VA)
{: #cims-scanning-tool-credentials-va}

If you enable Vulnerability Advisor (VA) scanning, you might choose to specify a different {{site.data.keyword.cloud_notm}} API key from the `ibmcloud-api-key` that is used to run your pipeline to pull the images and access the VA scan results. The `ibmcloud-api-key` is valid only for IBM Container Registry, IBM Container Staging Registry, {{site.data.keyword.cloud_notm}} Pak Registry, or {{site.data.keyword.cloud_notm}} Pak Staging Registry. To specify the different {{site.data.keyword.cloud_notm}} API key, use the appropriate pipeline environment property from the following list that corresponds to the registry for your images.

- IBM Container Registry:
    - `multiscan-icr-registry-key`
- IBM Container Staging Registry:
    - `multiscan-icr-stg-registry-key`
- {{site.data.keyword.cloud_notm}} Pak Registry:
    - `multiscan-cp-registry-key`
- {{site.data.keyword.cloud_notm}} Pak Staging Registry:
    - `multiscan-cp-stg-registry-key`

You can't pull VA scan results for images on {{site.data.keyword.cloud_notm}} Pak Registry or {{site.data.keyword.cloud_notm}} Pak Staging Registry by using an entitlement key. You must use an {{site.data.keyword.cloud_notm}} API key that is authorized to access the images.
{: note}

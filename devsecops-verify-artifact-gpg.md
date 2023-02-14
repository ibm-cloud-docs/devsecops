<staging>---

copyright: 
  years: 2023, 2023
lastupdated: "2023-02-14"

keywords: DevSecOps, gara signing, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Verify GPG signature in CD pipeline
{: #devsecops-verifyArtifact}

In the CI pipeline, all application images that are built will be signed using user provided GPG certificates. Before deploying these images to your production environment, it is important to verify their signatures to ensure their validity.

To accomplish this, a new stage has been added to the CD pipeline. This stage will go through all the images that are intended for deployment and check their signatures against the certificates.

Upon completion of the verification process, evidence will be collected to document the results, including the status of the verification.

Please note that only images signed with GPG certificates will be eligible for signature verification. If you have images signed using a property signing solution, you may need to customize this stage with your custom script.
{: attention}

## Prerequisites

The following are the prerequisites for verifying image signature:

* `signing-key` - The GPG private key which is used to sign images in your CI pipeline. You may need to refer your environment properties in your ci pipeline to get the private key.


If you don't have keys or need help, see [Generating a GPG key.](https://cloud.ibm.com/docs/devsecops?topic=devsecops-devsecops-image-signing).

## Environment variables
{: #devsecops-garasign-vars}

Add the following variables to your `CI-pipeline` toolchain properties to enable the GaraSign signing service:

| Name | Type | Description |
|--|--|--|
| `signing-key` | SECRET | The base64 encoded GPG certificate that is used in your CI pipeline. Alternatively, select the secrets store where the GPG certificate is stored. |

## Frequently asked questions

### What is the default client used for verification? I signed my images using the Skopeo client, is it supported?

The default method for verifying images will be through the use of the Skopeo client.

### I have signed my images using custom client, how can I verify their signatures?

Currently clients other then skopeo are not supported. It is necessary to modify the default script and supply a personalized implementation for the image verification scripts.
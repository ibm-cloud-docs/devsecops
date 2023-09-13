---

copyright: 
  years: 2023, 2023
lastupdated: "2023-09-13"

keywords: DevSecOps, IBM Cloud, public key, private key, GPG

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}


# Verifying image and non-image artifact signatures
{: #devsecops-image-verify}

Maintain the integrity of images that are built in the Continuous Integration (CI) pipeline before deployment by verifying image signatures.
{: shortdesc}

## Before you begin
{: #devsecops-image-verify-before}

Before you start working with image verification, be sure that you have the following prerequistes.

* You must have the GPG public key. For help generating your GPG key, see [the documentation]({[link]}-devsecops-publickey).
* You must add the environment variable `code-signing-certificate` with the base 64-encoded GPG public key. 

## Verifying images
{: #devsecops-image-verify-verify}

A new `prod-verify-artifact` stage verifies the signature of an image in the Continuous Delivery (CD) pipeline. This stage performs the following steps:

1. Decodes the user-provided encoded GPG public key into a temporary file.
2. Creates a Docker container policy (`/etc/containers/policy.json`) with the public key.

   ```JSON
    {
      "default": [
        {
          "type": "reject"
        }
      ],
      "transports": {
        "docker-daemon": {
          "": [
            {
              "type": "reject"
            }
          ]
        },
        "docker": {
          "": [
            {
              "type": "signedBy",
              "keyType": "GPGKeys",
              "keyPath": "/tmp/GPGPublicKey"
            }
          ]
        }
      }
    }
   ```
   {: codeblock}

3. Retrieves the list of artifacts for each artifact by using [Skopeo](https://github.com/containers/skopeo){: external} to pull the image with the container policy.  

    ```bash
        skopeo copy docker://"${image}" dir:"${tmp_sign_dir}" --src-creds iamapikey:"${ibmcloud_api_key}"
    ```
    {: codeblock}

If the signature is valid and verified by the public key that is provided by the user, the image pull is successful.



## Verifying non-image artifacts
{: #devsecops-non-image-artifact-verify}

In DevSecOps pipelines, the signature of signed artifacts is subject to verification. This section outlines the prerequisites that users must meet before proceeding with artifact verification.

### Before you begin
{: #devsecops-non-image-artifacts-verify-before}

Before you can work with image verification, be sure that you have the following prerequisites.

1. Modify your pipeline configuration to enable the verifcation process. Add the following code snippet to your `.pipeline-config.yaml` file.

   ```bash
   verify-artifact::
   image: icr.io/continuous-delivery/pipeline/image-signing:1.0.0@sha256:e9d8e354668ba3d40be2aaee08298d2aa7f0e1c8a1829cca4094ec93830e3e6a
   image_pull_policy: IfNotPresent
   abort_on_failure: false
   dind: true
   script: |
      #!/usr/bin/env bash
      source /opt/commons/verify-artifact/verify_non_image_artifact.sh
   ```
   {: codeblock}

2. Download the artifact and store it in the pipeline runtime.

   Before invoking the `verify-artifact` stage, download and store the required artifacts in the Cocoa runtime. By default, all artifacts listed in teh `list_artifacts` collection are verified. The following sample code demonstrates how to download an artifact from Cloud Object Storage and save it in the Cocoa runtime:

   ```bash
   #!/usr/bin/env bash

   export SECRET_PATH="/config/ibmcloud-api-key"
   . "${ONE_PIPELINE_PATH}"/iam/get_token

   SKIP_SIGN_ARTIFACTS_TYPE="$(get_env skip-sign-artifact-type "")"
   IFS=';' read -ra ARTIFACT_LIST <<<"$SKIP_SIGN_ARTIFACTS_TYPE"

   list_artifacts | while IFS= read -r artifact; do

      type="$(load_artifact "$artifact" "type")"

      if [[ ! "${ARTIFACT_LIST[@]}" =~ "$type" ]] && [[ "$type" != "image" ]]; then
         
         inventory_entry="$(load_artifact "$artifact" "inventory-entry")"
         inventory_entry=${inventory_entry///artifacts/$WORKSPACE}
         artifact_url=$(jq -r .'app_artifacts.artifact_url' ${inventory_entry})
         echo "downloading the artifact  ${artifact_url}"
         curl -H "Authorization: bearer ${IAM_ACCESS_TOKEN}" --output outputfile ${artifact_url}
         save_file "${artifact}" outputfile

      fi
   done
   ```
   {: codeblock}

### Verifying artifacts
{: #devsecops-non-image-artifact-verifcation}

In the artifact verification process, the user-provided GPG public key is imported into the GPG keyring. All artifacts listed in the `list_artifacts` collection will be verified. The stage attempts to retrieve the signature from the inventory and performs the following verification:

```bash
gpg --verify  "${signature}" "${artifactName}"
```
{: codeblock}

If the signature is valid and verified using the provided public key, the stage will record the evidence and mark the stage as successful.


---

copyright: 
  years: 2023, 2023
lastupdated: "2023-03-23"

keywords: DevSecOps, IBM Cloud, public key, private key, GPG

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Verifying image signatures
{: #devsecops-image-verify}

Maintain the integrity of images that are built in the Continuous Integration (CI) pipeline before deployment by verifying image signatures.
{: shortdesc}

## Prerequisites
{: #devsecops-image-verify-preq}

To verify the signature of an image, you must have the GPG public key. Follow the steps in [Generating a GPG key](/docs/devsecops?topic=devsecops-devsecops-publickey).

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

3. Retrieves the list of artifacts for each artifact by using [Skopeo](https://github.com/containers/skopeo){: external} to pull the image with the container policy.  

    ```bash
        skopeo copy docker://"${image}" dir:"${tmp_sign_dir}" --src-creds iamapikey:"${ibmcloud_api_key}"
    ```  

If the signature is valid and verified by the public key that is provided by the user, the image pull is successful.

## Enabling image verification
{: #devsecops-image-verify-enable}

To enable image verification, add the environment variable `code-signing-certificate` with the base64-encoded GPG public key.

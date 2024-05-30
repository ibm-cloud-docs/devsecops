---

copyright:
  years: 2023, 2024
lastupdated: "2024-05-30"

keywords: DevSecOps, garasign, IBM Cloud, DevSecOps, IBM certificate chain

subcollection: devsecops, public key one pipeline

---

{{site.data.keyword.attribute-definition-list}}

# Getting an IBM certificate chain for signature verification by using GaraSign
{: #onepipeline-get-publickey}

[OnePipeline]{: tag-blue} [Internal]{: tag-red}

Use an IBM certificate chain to verify the signature of signed images in a continuous deployment (CD) pipeline.
{: shortdesc}

You can get an IBM certificate chain in the following ways:

* From the continuous integration (CI) pipeline.
* From your local computer.

## Getting an IBM certificate chain from the CI pipeline
{: #onepipeline-get-publickey-ci}

Take the following steps:

1. To get the certificate, add the environment variable `print-code-signing-certificate` with a value of `1` to your CI pipeline. After you run the CI pipeline, the IBM certificate chain is printed on the console in the `build-sign-artifact` stage.

1. Copy the public key into a file and encode it in `base64` format by running the following command:

    ```bash
    cat copied_key.txt | base64
    ```

1. Then, copy the encoded key and use it as a value for the `code-signing-certificate` environment property in your Continuous Deployment (CD) pipeline.

## Getting an IBM certificate chain from your local computer
{: #onepipeline-get-publickey-local}

Take the following steps:

1. Export your certificates. If you have the GaraSign client along with certificates in your local machine, see [Exporting and publishing the public key and IBM certificate](https://pages.github.ibm.com/Supply-Chain-Security/AppSec-External-Docs/appsec/CodeSigningService/LocalSign/WhatDoYouWantToSign/SigningContainers/imagesigningwithskopeo/#exporting-and-publishing-the-public-key-and-ibm-certificate){: external} for information on how to export your certificates.

1. When you have the certificates, copy the encoded key and use it as a value for the `code-signing-certificate` environment property in your CD pipeline. Run the following command:

```bash
 cat /Path/ToStorePublicKeys/*.pem.chain | base64
```

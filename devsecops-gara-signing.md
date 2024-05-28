---

copyright:
  years: 2024
lastupdated: "2024-05-24"

keywords: DevSecOps, inventory model, inventory, IBM Cloud

subcollection: devsecops

---
{{site.data.keyword.attribute-definition-list}}

# Signing artifacts by using GaraSign
{: #devsecops-garasign}

[OnePipeline]{: tag-blue} [Internal]{: tag-red}

Artifact signing is important as it helps protect the integrity of the artifacts. The internal tool used to sign the artifacts is Skopeo or Cosign.
{: shortdesc}

For additional assistance, contact:
   1.  akshaykumar.vijapur@ibm.com for OnePipeline queries.
   1. thrinath@ibm.com for the CISO queries.
{: tip}

## Prerequistes
{: #devsecops-garasign-prereq}

Before you get started with GaraSign, be sure that you have the following required prerequistes.

* A GaraSign key (`client_auth_xxx_xxxxxxxxxxx.pfx`) certificate.
* A GaraSign credential.
* `product-type` - The PSIRT/EAL that is used to on-board to the GaraSign service.
* `PGP Key Certificate` - PGP key certificate that you get from the SOS CSS team by using the `#scs_code-signing` Slack channel for your PSIRT boarding ID or TenantName. When your certificates are approved, they are automatically downloaded by the pipeline, and do not require explicit provision with pipeline environment variables.
* Remove the environment properties `signing-key` and `vault-secret` from the pipeline properties before you implement GaraSign.

If you don't have keys or need help, see [Setting up image signing and Gara code signing service](/docs/devsecops?topic=devsecops-devsecops-imagesigning).

The signing process might encounter an error when getting the GaraSign fingerprint for signing. If you get an error, contact the SOS CSS team to request a PGP key certificate for your PSIRT boarding ID or TenantName by using the `#scs_code-signing` Slack channel. This action is not necessary for EAL ID because that ID is already provided.
{: important}


## Signing images by using GaraSign
{: #devsecops-garasign-images}

1. Disable your existing CISO based signing solution by removing the following variables from your toolchain properties.

   * `signing-key`
   * `vault-secret`

   You cannot keep the existing CISO based signing solution with the GaraSign solution. You must also remove the environment property when signing with the Skopeo client.
   {: note}

2. Enable the GaraSign service by including the following variables to your `CI-pipeline` toolchain.

   | Name | Type | Description |
   |--|--|--|
   | `product-type` | TEXT | The product registration type for the product that is used to on-board the GaraSign service. Example: If your ID looks like `PRDXXXXX`, then provide `prd` as the value. If your ID looks like `EAL-XXX`, then provide `eal` as the value. |
   | `gara-signing-key` | SECRET | The base64 encoded GaraSign certificate (`client_auth_XXX_XXXXXXXX.pfx`) that is downloaded along with client bundle. Alternatively, select the secrets store where the GaraSign certificate is stored. |
   | `gara-signing-credential` | SECRET | Unencoded `P12PASSWORD` credential (`credentials.txt`) that is downloaded along with client bundle. Provide the password only. Example: If your credentials file contains `P12PASSWORD=******`, then provide the password only, do not include `P12PASSWORD=` or select the secrets store where the `P12Password` is stored. |
   {: caption="Table 1. Environment variables" caption-side="top"}


By default, images are signed by using the Skopeo client, which is the client that is recommended by CISO because of its compatibility with Portieris admission controllers on Kubernetes clusters. To sign your images by using Cosign, add the environment property `gara-cosign` with a value of `1`.
{: note}


### Customizing the implementation of the `sign-artifact` stage
{: #devsecops-garasign-cust-impl}

Ensure that you are using the latest image `icr.io/continuous-delivery/toolchains/devsecops/csso-image-sign:8.0.0@sha256:4fa72947d3b97c029b035f5c0b458184808f294a417b847b4935015d3c0744d3`, which includes all the necessary clients for GaraSign. For more information, see [signing scripts](https://github.ibm.com/one-pipeline/compliance-commons-internal/tree/master/ciso){: external}.

These scripts include the following, which you must incorporate into your custom implementation:

* `sign_icr.sh` - Signs and pushes the images to IBM Cloud Container Registry.
* `sign_artifactory.sh` - Signs and pushes the images to TaaS artifactory.

## Signing images that are stored in a TaaS Artifactory
{: #devsecops-garasign-taas-art}

You can sign images that are stored in a TaaS Artifactory by using either the Cosign or Skopeo client.

* To use Cosign to sign images, add the `gara-cosign` toolchain environment property set to `1` in your pipeline.

* To use the Skopeo client, you can use the following steps.

Take the following steps to sign images by using the Skopeo client:

   1. Modify `.pipeline-config.yaml` with the following artifact:

      ```bash
      sign-artifact:
         image: icr.io/continuous-delivery/toolchains/devsecops/csso-image-sign:8.0.0@sha256:4fa72947d3b97c029b035f5c0b458184808f294a417b847b4935015d3c0744d3
         image_pull_policy: IfNotPresent
         abort_on_failure: false
         dind: true

         sources:
            - repo: https://github.ibm.com/one-pipeline/compliance-commons-internal.git
            sha: 75339ac5b0dde7b764bd7e17bb57d5b64c3302c5
            path: ciso
            - repo: https://github.ibm.com/open-toolchain/compliance-commons.git
            alias: https://us-south.git.cloud.ibm.com/open-toolchain/compliance-commons
            sha: eabe79d3f0567752387563cf36edc09cac226846
            path: sign-artifact

         script: /opt/commons/ciso/sign_artifactory.sh
      ```
      {: codeblock}

   2. In addition to the environment variables listed in table 1, add the environment variables from table 2.

      | Name | Type | Description |
      |--|--|--|
      | `taas-artifactory-user` | TEXT | Username of the TaaS Artifactory. |
      | `taas-artifactory-token` | SECRET | Token of the TaaS Artifactory. |
      | `artifactory-docker-repo-name` | TEXT | Artifactory repository that stores your images. For more information, see [TaaS code signing](https://taas.cloud.ibm.com/getting-started/codesigning/codesigning-jenkins-artifactory.md) |
      | `artifactory-sigstore-repo-name` | TEXT | Generic artifactory repository that stores signatures. For more information, see [TaaS code signing](https://taas.cloud.ibm.com/getting-started/codesigning/codesigning-jenkins-artifactory.md) |
      | `artifactory-primary-service` | TEXT | Location of the artifactory [Default: `eu`]. For more information, see [TaaS announcement](https://taas.cloud.ibm.com/blog/2022-11-24-artifactory-cse.md) |
      {: caption="Table 2. TaaS and Artifactory environment variables" caption-side="top"}

## Signing non-image artifacts
{: #devsecops-garasign-artifacts}

One pipeline provides the capability to sign non-image artifacts in file formats such as `tar` and `zip`. This process invovles using a script in conjunction with the default signing script. If you intend to sign artifacts other than those types of files, you must customize your implementation of the script.


### Before you begin
{: #devsecops-before-garasign-artifacts}

Before you get started with GaraSign, be sure that you have the following prerequistes in place.

1. Be sure that the required runtime environment is available within the CISO image to support the signing process. To implement this, modfy your `.pipeline-config.yaml` with the following stage configuration.

   ```bash
   sign-artifact:
   image: icr.io/continuous-delivery/toolchains/devsecops/csso-image-sign:8.0.0@sha256:4fa72947d3b97c029b035f5c0b458184808f294a417b847b4935015d3c0744d3
   image_pull_policy: IfNotPresent
   abort_on_failure: false
   dind: true
   script: |
      #!/usr/bin/env bash
      source /opt/commons/ciso/sign_artifacts.sh
   ```
   {: codeblock}

2. By default, the script can sign all artifacts that are listed in the `list_artifacts` collection. Be sure that your artifacts are properly stored in the Cocoa pipeline runtime. This can be achieved in one of the two following ways.

   * Use the `save_file` tool to save the artifact tar file, which allows retrieval in subsequent stages.

      ```bash
      save_file "${APP_NAME}" "${APP_NAME}-${VERSION}.tgz"
      ```
      {: codeblock}

   * Store artifacts in a designated location, with the artifact name that matches your app name.

      ```bash
      location="$(basename $(pwd))"
      ```
      {: codeblock}

3. Select your signing client. By default, the script uses the `GPG` client for signing. To use the `CoSign` client, set your pipeline environment variable `gara-cosign` to the value `1`.

### Post-signing steps
{: #devsecops-garasign-artifact-post-sign}

After the artifact is signed, the script stores both the artifact and the signature in the Cocoa pipeline runtime. To retrieve the signed artifact and its corresponding signature, you can use the following approach.

```bash
signed_artifact=$(mktemp)
if load_file "${artifact}_signed_artifact" >"${signed_artifact}" 2> /dev/null; then
    echo "Loaded the signed artifact ${artifact} from load_file at the ${signed_artifact}"
else
    artifact_location="$(load_artifact "$artifact" "location")"
    cp "${WORKSPACE}/${artifact_location}/${name}" "${signed_artifact}"
    echo "Loaded the artifact at the ${WORKSPACE}/${artifact_location}/${name}"
fi
artifact_signature=$(mktemp)
if load_file "${artifact}_signature" >"${artifact_signature}" 2> /dev/null; then
    echo "Loaded the signature of ${artifact} from load_file at the ${artifact_signature}"
fi
```
{: codeblock}

If you need to store the signed artifact, you can use a storage solution of your choice or you can use IBM Cloud Object Storage. The following code snippet shows an example of how to upload the signed artifacts and signatures to Cloud Object Storage and update your inventory repository.

```bash
 # Upload the artifact and signature to COS.
        namespace="$(get_env pipeline_namespace)"
        local cos_params=(
            --pipeline-run-id="${PIPELINE_RUN_ID}"
            --namespace="${namespace}"
            --backend cos
        )
        # Upload the artifact
        artifact_cos_url=$(cocoa artifact upload "${signed_artifact}" "${cos_params[@]}")
        # Upload the artifact
        artifact_signature_cos_url=$(cocoa artifact upload "${artifact_signature}" "${cos_params[@]}")

        APP_ARTIFACTS='{ "app": "'${APP_NAME}'", "artifact_url": "'${artifact_cos_url}'", "artifact_signature": "'${artifact_signature_cos_url}'" }'
        cocoa inventory add \
            --artifact="${name}@${digest}" \
            --name="${APP_REPO_NAME}" \
            --app-artifacts="${APP_ARTIFACTS}" \
            --signature="${signature}" \
            --provenance="${name}@${digest}" \
            --sha256="${digest}" \
            --type="image" \
            "${params[@]}"
```
{: codeblock}

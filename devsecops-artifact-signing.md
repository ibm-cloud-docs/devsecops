---

copyright: 
  years: 2023, 2023
lastupdated: "2023-09-13"

keywords: DevSecOps, IBM Cloud, public key, private key, GPG

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Signing non-image artifacts
{: #devsecops-artifacts-verify}

The DevSecOps toolchain provides the capability to sign non-image artifacts, restricted to file formats such as `tar` and `zip`. This process involves using a script in conjunction with the default signing script. If you need to sign artifacts other than `tar` and `zip` files, you must provide a customized implementation of the script.
{: shortdesc}

The required runtime environment must be available within the image to support the signing process. 
{: important}

## Before you begin
{: #devsecops-artifact-sign-preq}

Before you get started with non-image artifact signing, you must have the following prerequistes.

1. To sign an artifact, you must have the GPG private key. To generate a key, you can use the [documentation](/docs/devsecops?topic=devsecops-devsecops-image-signing). 
2. Your `.pipline-config.yaml` file must be updated with the following stage configuration.

   ```bash
   sign-artifact:
      image: icr.io/continuous-delivery/pipeline/image-signing:1.0.0@sha256:e9d8e354668ba3d40be2aaee08298d2aa7f0e1c8a1829cca4094ec93830e3e6a
      image_pull_policy: IfNotPresent
      abort_on_failure: false
      dind: true
      script: |
         #!/usr/bin/env bash
         source /opt/commons/sign-artifact/sign-artifacts.sh
   ```
   {: codeblock}

3. By default, the script can sign all artifacts that are listed in the `list_artifacts` collection. Be sure that your artifacts are properly stored and integrated. 

   1. Ensure that you store built artifacts in the Cocoa pipeline runtime. This can be achieved in one of the two following ways.

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

   2. Integrate the saved artifact into the Cocoa runtime by using the following codesnippet as a guide.

      ```bash
      save_artifact "${APP_NAME}" \
         type=tar \
         "name=${APP_NAME}-${VERSION}.tgz" \
         "digest=${DIGEST}" \
         "location=${location}" \ 
         "provenance=${APP_NAME}-${VERSION}.tgz@${DIGEST}"
      ```
      {: codeblock}



## Signing non-image artifacts
{: #devsecops-artifact-signing-non-image}

The default script employs the GPG client for signing purposes. During this stage, all artifacts undergo the signing process. After the artifact is signed, the script stores both the artifact and the signature in the Cocoa pipeline runtime. 



## Post-signing steps
{: #devsecops-artifact-signing-non-image-post}

To retrieve the signed artifact and its corresponding signature, you can use the following approach.

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
 # Upload the artifact and signature to Cloud Object Storage.
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
            --type=$type" \
            "${params[@]}"
```
{: codeblock}


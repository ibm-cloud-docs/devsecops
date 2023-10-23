---

copyright:
  years: 2021
lastupdated: "2021-10-12"

keywords: DevSecOps

subcollection: devsecops

---

{:shortdesc: .shortdesc}
{:table: .aria-labeledby="caption"}
{:external: target="_blank" .external}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:download: .download}
{:help: data-hd-content-type='help'}
{:support: data-reuse='support'}

# Building Docker images
{: #cd-devsecops-build-docker}

You can build all of the Docker images in the `containerize` stage that you need. If you wish to override the default built-in GPG Image signing for those images, you need to let the pipeline be aware of these images.
{: shortdesc}

## Adding image artifacts
{: #cd-devsecops-image-artifacts}

You can add image artifacts by using the `pipelinectl` interface. In the containerize stage, add the images as artifacts to the pipeline:

```bash
save_artifact <image-alias> type=image "name=${IMAGE}" "digest=${DIGEST}" [<prop>=<value>]
```

Where the value of name must be a fully qualified image name, for example: `your-docker-repository.namespace/imagename:imagetag` and `digest` must be the `sha256` digest of the image, for example,  `sha256:c43e802dcc7485590b54614006ced9338d5bd9a045115c89d5570bb95eeb52d9`.

`<image-alias>` can be any string, its purpose to provide a human-readable name for your image among the other artifacts. You can add any properties to the artifact, like tags, timestamp, etc.

The **type**, **name**, and **digest** fields  are required!
{: important}

### Example
{: #cd-devsecops-artifacts-example}

```bash
save_artifact service type=image \
  name="${SERVICE_IMAGE}" \
  digest="${SERVICE_IMAGE_DIGEST}" \
  tags="${SERVICE_IMAGE_TAGS}"

save_artifact service_debug type=image \
  "name=${SERVICE_DEBUG_IMAGE}" \
  "digest=${SERVICE_DEBUG_IMAGE_DIGEST}"
```
{: screen}

By using this interface, the default built-in GPG Image signing will be able to pick up these images and process them.

## Loading image artifacts
{: #cd-devsecops-load-artifacts}

At the end of the pipeline in the release stage, you can access these artifacts using the pipelinectl interface:

```text
list_artifacts
load_artifact <image-alias> <property>
```
{: screen}

`list_artifacts` lists the image aliases available, where `<property>` is any property that you or the pipeline added previously. Using these together you can iterate over your artifacts, for example:

```text
list_artifacts | while IFS= read -r artifact; do

  $image=$(load_artifact "$artifact" name)
  $digest=$(load_artifact "$artifact" digest)
  $signature=$(load_artifact "$artifact" signature)

  # update your inventory using these 

done
```
{: screen}

The `signature` property is added by the default built-in GPG Image signing task.
{: tip}

## Working with ICR
{: #cd-devsecops-work-with-icr}

[IBM Cloud® Container Registry](/docs/Registry?topic=Registry-registry_overview) provides a multi-tenant private image registry that you can use to store and share your container images with users in your IBM Cloud account.


### Working with ICR having private endpoint enabled
{: #cd-devsecops-work-with-icr-private-endpoints}

The environment variable `registry-domain` needs to be added to the pipeline to work with registries enabled with private endpoints.
 
#### Example
{: #cd-devsecops-icr-private-endpoints-example}
 
 The value of `registry-domain` can be set to `private.de.icr.io`
 
 To use the environment variable, your code might look similar to the following sample:
 
 ```text
 ICR_REGISTRY_DOMAIN="$(get_env registry-domain "")"
 if [ -z "$ICR_REGISTRY_DOMAIN" ]; then
   # Default to icr domain from registry-region
   ICR_REGISTRY_REGION="$(get-icr-region "$(get_env registry-region "")")"
   ICR_REGISTRY_DOMAIN="$ICR_REGISTRY_REGION.icr.io"
 fi
 IMAGE="$ICR_REGISTRY_DOMAIN/$ICR_REGISTRY_NAMESPACE/$IMAGE_NAME:$IMAGE_TAG"
 docker login -u iamapikey --password-stdin "$ICR_REGISTRY_DOMAIN" < /config/api-key
 ```
 {: codeblock}
 
 The previous sample code is taken from [sample hello-compliance-app](https://us-south.git.cloud.ibm.com/open-toolchain/hello-compliance-app/-/blob/master/scripts/build_setup.sh).


For more information, check out the following documentation:

* [Documentation on stages for user-defined scripts](/docs/devsecops?topic=devsecops-custom-scripts)
* [API documentation for pipelinectl](/docs/devsecops?topic=devsecops-cd-devsecops-pipelinectl)
* [The default built-in GPG Image sign script](https://us-south.git.cloud.ibm.com/open-toolchain/hello-compliance-app/-/blob/master/scripts/sign_image.sh){: external}

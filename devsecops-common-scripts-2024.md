---

copyright:
  years: 2024, 2026
lastupdated: "2026-01-21"

keywords: DevSecOps, common scripts, scripts, pipeline stages, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Understanding Commons Scripts in DevSecOps
{: #devsecops-common-scripts-revise}


In DevSecOps, some stages run default scripts if they are not specified in the config YAML file. These default scripts are part of a commons scripts library, which provides a set of reusable scripts that can be used as a starting point for customization of the pipeline configuration.

## Commons Scripts Library
{: #devsecops-common-scripts-library}

- The commons scripts library is a collection of reusable scripts that can be used in DevSecOps pipelines.
- The scripts and functions in this commons scripts library can be used to customize the pipeline configuration in order to meet your specific pipeline requirements.

Refer to the [Commons Scripts Library documentation](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-commons#tool-inventory) to learn more about the commons scripts library and its usage.


### Viewing Default Scripts
{: #devsecops-common-scripts-view}

To view the default script running in a stage, examine the logs from that stage. For example, in the `scan-artifact` stage:

```text
=== Executing custom script for stage 'scan-artifact' ===

The custom script can be viewed using the following link: '<SCRIPT URL>'

You can customize this stage in your pipeline config. For more information, see [Custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize).

The script(s) used in this stage can be found at the following location(s):

- path: scan-artifact
  repo: <REPO URL>
  sha: b0fff29d715e0f9a33b3c84e649777cfb9d40738
- path: icr-va
  repo: <REPO URL>
  sha: de693814ba2bd992941b432fc71111efb54e2b27

Using the following defaults for stage "scan-artifact":

scan-artifact:
  image: icr.io/continuous-delivery/pipeline/pipeline-base-image:2.12@sha256:ff4053b0bca784d6d105fee1d008cfb20db206011453071e86b69ca3fde706a4
  dind: false
  abort_on_failure: false
  image_pull_policy: IfNotPresent
  script: |
    #!/bin/sh

    "/opt/commons/scan-artifact/scan.sh"

```

This message has three parts:

1. The first line shows the exact script URL currently running, pointing to it on a commit level.

2. The second part is a list of paths, repos, and commit hashes that are imported to the pipeline base image during the build. They are not pulled from these repos in runtime. This information improves public traceability of what exactly is included in the pipeline.

3. The third part is the default configuration that is used as the config YAML. You can take this YAML snippet and include it in your DevSecOps config YAML file. This action overrides the default config with yours but still uses the default script from the base image. This method is helpful if you want to implement some other checks before the default ones.


This information is displayed to help adopters identify the script and function from the [commons scripts library](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-commons#tool-inventory) that are running in stages by default. Eventually, they can inspect the scripts, understand what they do, copy the script into their script collection, and update them to fit their use cases.

### Customizing using Commons Scripts Library
{: #devsecops-common-scripts-customize}

The commons library can also serves as a source to copy code from and customize for specific edge cases. Use the library as a starting point to create custom scripts according to various requirements.

Refer [here](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize) to learn more about pipeline customization

## Best Practices
{: #devsecops-common-scripts-best-practices}

- Use the commons library as a reference for pipeline mechanics.
- Customize default scripts to meet specific edge cases.
- Use the `collect-evidence` script to collect evidence in custom scripts.

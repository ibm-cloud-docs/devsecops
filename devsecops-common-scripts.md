---

copyright:
  years: 2022
lastupdated: "2022-05-09"

keywords: DevSecOps, common scripts, scripts, pipeline stages, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Common scripts
{: #devsecops-common-scripts}

Some stages run default scripts if they are not specified in the DevSecOps config YAML file.

These scripts are part of a commons library, which can be used as a source to copy code from and 
customize them for specific edge cases for adopters. This library also serves as an example 
of some pipeline mechanics.

Every script that is included in the commons library performs the following actions:

- Iterates over assets (repos or artifacts).
- Runs the scan / check job.
- Collects evidence by using the [`collect-evidence` script](/docs/devsecops?topic=devsecops-devsecops-collect-evidence).

To see which script is running, examine the logs from a stage that is running a default script. For example, the default scripts that are running in `scan-artifact`:

```text
=== Executing custom script for stage 'scan-artifact' ===

The custom script can be viewed using the following link: '<SCRIPT URL>'

You can customize this stage in your pipeline config. For more information, see [Custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-stages#cd-devsecops-pipelines-custom-customize).

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

This information is displayed to help adopters identify the scripts that are running in stages by default. Then, they can inspect the scripts, understand what they do, copy the script into their script collection, and update them to fit their use cases.

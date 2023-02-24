---

copyright:
  years: 2023
lastupdated: "2023-02-20"

keywords: DevSecOps, IBM Cloud, compliance, FIPS 140-2

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Configuring FIPS Scanner

{: #fips-scanner}

The FIPS scanner is static code scanner that scans the source code in your DevSecOps pipeline and can highlight possible deviations from FIPS 140-2 requirements. 

{: shortdesc}

It currently provides two types of scanning:

- Source code scanning for use of non-approved NIST FIPS 140-2 libraries [crypto](https://pages.github.ibm.com/ibmcloud/governed-content-site/cryptography_1) libraries
  - Currently supported languages:
    - JavaScript (NodeJS)
    - Java
    - Golang
    - Python
- Dockerfile scanning for use of non-approved base images ([ARCH004](https://test.cloud.ibm.com/docs/service-framework?topic=service-framework-architecture-arch-#arch004-container-provenance))

The scanner runs for each source repository in your pipeline that is returned by the `list_repos` method and it generates evidence about any findings. (For more information on `list_repos` see [here](/docs/devsecops?topic=devsecops-pipelinectl.md#list_repos)).

The list of rules implemented in the scanner can be found:

- For Dockerfile scanning see [here](https://github.ibm.com/ibmcloud/fips-scanning-rules/blob/master/dockerfiles/RULES.md).
- For Source code scanning see [here](https://github.ibm.com/ibmcloud/fips-scanning-rules/blob/master/semgrep/RULES.md).

## Adding FIPS Scanner to the continuous integration pipeline

{: #fips-scanner-ci-pipeline}

To enable the FIPS scanner for your pipeline, simple set the pipeline property `opt-in-fips-scan` to a non-empty value, similar to what is done for other scanners, such as gosec.

There are some optional properties that can be set to further customize the behavior of the scanning and are listed in the next sections.

### Available parameters for FIPS Scanner

{: #fips-scanner-ci-parameters}

| Name                                                         | Description                                                  | Comments                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `fips-scanner-config`                                        | The scanner config file name for customizing either the source or dockerfile scanning. See [next](#fips-scanner-config) section for more details. | Defaults to `.fips-scanner-config.json`                      |
| `fips-scanner-image`                                         | The image registry and repository of the FIPS scanner image to use, e.g. `docker-na.artifactory.swg-devops.com/ibmcloud-fedramp-docker-local/fips-scanner`.<br/>The default image is included in the One-Pipeline container registry in Artifactory: `docker-eu-public.artifactory.swg-devops.com/wcp-compliance-automation-team-docker-local/fips-scanner` | Not required. Only needed if there is a need to "override" the default image used. |
| `fips-scanner-tag`                                           | The image tag to use, e.g. `1.0.0`<br/>When not specified, the default image tag defined in the One-Pipeline integration scripts is used (i.e. the latest version) | Not required. Only needed if there is a need to "override" the default image tag used (e.g. for trying out a newer image) |
| `fips-scanner-registry-username` and `fips-scanner-registry-apiKey` | The container registry credentials to use to pull the image specified by the `fips-scanner-image` (and or `fips-scanner-tag`) properties. | Not required. Only needed if the image to be used is "overriden" and is in a non-Artifactory based container registry |

#### FIPS Scanner Config

{: #fips-scanner-config}

You can provide a configuration file to set some "global" parameters to customize the scanner. The default name of this file is `.fips-scanner-config.json`. The filename can be overriden using the pipeline property `fips-scanner-config`.

The search path for this file is listed below:

- Top level folder of the repository configured with `pipeline-config-repo` (if specified)
- Top level folder of the each source repository included in the pipeline

For more details and content of the config file, see [here](https://github.ibm.com/ibmcloud/fips-scanning-rules#customizing-the-scaning).

In addition, you can use "inline" comment tags to provide some more fine-grained options for both the dockerfile and source code scanners:

- For source code scanning, see [here](https://github.ibm.com/ibmcloud/fips-scanning-rules/blob/master/semgrep/README.md#dealing-with-false-positive-semgrep-findings).
- For dockerfile scanning, see [here](https://github.ibm.com/ibmcloud/dockerfile-scanner#excluding-dockerfile-instructions-from-scanning).

#### FIPS Scanner image 

{: #fips-scanner-image}

The FIPS Scanner is packaged in a docker image and can be used even without One-Pipeline. For more information see [here](https://github.ibm.com/ibmcloud/fips-scanning-rules#using-the-rules-and-scripts-from-this-repository).

Generally, there would no need to provide the pipeline properties `fips-scanner-image`, `fips-scanner-tag`, and even less need for  `fips-scanner-registry-username` and `fips-scanner-registry-apiKey`.

One scenario that might require you to set the `fips-scanner-image` and `fips-scanner-tag` is if you are trying out a new image with fixes or new rules implemented.

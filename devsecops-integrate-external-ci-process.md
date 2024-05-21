---

copyright:
  years: 2023, 2024
lastupdated: "2024-05-21"

keywords: DevSecOps, IBM Cloud, public key, private key, GPG

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Integrating the DevSecOps continuous deployment process with an external continuous integration process
{: #devsecops-int-ext-ci-process}

A new `build-inventory-evidence` script integrates an external continuous integration (CI) process and pipelines that are built that uses Jenkins and Travis, for example, with the IBM reference implementation of the DevSecOps continuous deployment (CD) process and pipeline.
{: shortdesc}

In typical [DevSecOps workflows](/docs/devsecops?topic=devsecops-cd-devsecops-arch), the [CI process](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines#cd-devsecops-pipelines-ci-pipeline) scans the source code for vulnerabilities and builds the deployable artifacts from source code. The [CD process](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines#cd-devsecops-pipelines-cd-pipeline) then uses scan results available from the CI process to determine the deployment readiness of the artifacts before deployment.

This workflow requires a common storage media where the CI process can store scan details like scan results, build results, and scan and build logs. The CI Process stores the build metadata of the deployable artifact like container image URLs, and deployment yamls  in the version system like Git known as the [Inventory repository](/docs/devsecops?topic=devsecops-inventory.md). The evidence of scans and builds is stored by the CI process in a version system like Git that is known as [Evidence Repository](/docs/devsecops?topic=devsecops-evidence.md). The evidence collected of scans is later retrieved by the CD process.  To compute the deployment eligibility of the inventory state associated with the build.

The `build-inventory-evidence` script automates the process of building the inventory and evidence data as required by the IBM Reference Implementation, the DevSecOps CD process. An external CI system or automation can run the `build-inventory-evidence` script with an appropriate set of inputs to populate the inventory repository with deployable artifact information. An external CI system or automation can also populate the evidence repository with information about evidence of scans that run during the CI process.

## Using the `build-inventory-evidence` script
{: #devsecops-build-inventory-evidence-script}

The `build-inventory-evidence` script is a Python script that is contained within the DevSecOps base image container image. The DevSecOps container image, which is developed and maintained by IBM, implements the core functions of the DevSecOps CI, CD, CC pipeline. The image is maintained within {{site.data.keyword.registryshort}} and is pulled by the Tekton pipelines during pipeline execution. Like Tekton, any CI process or automation that has appropriate permission to access {{site.data.keyword.registryshort}} can also pull the DevSecOps base image container image. The `build-inventory-evidence` script runs within the DevSecOps base image container and hence requires a Container Runtime like Docker or Podman.

The `build-inventory-evidence` script requires two inputs:

* An input file that contains structured details about built artifacts and evidence that are collected by the CI process. For more information, see [DevSecOps input file](#devsecops-build-inventory-evidence-script-input).
* An attachments directory that contains scan results of different tools that are supported by DevSecOps pipelines. For more information, see [DevSecOps evidence attachment directory](#devsecops-build-inventory-evidence-script-evidence-att).

A sample that is run of the `build-inventory-evidence` script with the Docker command line is as follows. Notice the DevSecOps baseimage container image that is used to spin up the Docker container. The sample shows pulling up a specific tag `2.76.1_commons-0.29.0` and digest `sha256:6f639e188e9acc228034ef8fcfa3ca9ba6c5af61b90ed87fcd9f04493451c664` of the `devsecops-baseimage` image. You must use the latest version of the DevSecOps base image that contains updated code for the `build-inventory-evidence` script. Please refer to the [DevSecOps Release Page](https://github.ibm.com/one-pipeline/compliance-pipelines/releases) to determine the version of DevSecOps base image container image used in the **latest** pipeline definitions.


```bash

podman run -it -e GHE_TOKEN=<> -e COS_API_KEY=<> -v /path/to/host/directory:/path/to/container/directory icr.io/continuous-delivery/toolchains/devsecops/baseimage:3.7.0_commons-1.5.1@sha256:db9500494679e351d55505d584b584657fae775744db6d7486d7a1bcf9b235e5 python3 /opt/one-pipeline/tools/build-inventory-evidence.py --input-path /path/to/container/directory/devsecops-inputs.yaml

```
{: codeblock}

Where:

[`-it`] option allows the standard output of the container to be made available to the host in an interactive manner.

[`-e`] option is to pass the environment variable to the context of the container. From the security standpoint, the script execution expects **GHE_TOKEN (mandatory)** and **COS_API_KEY (optional)** to be passed via the podman/docker command line.

[`-v`] option mounts a directory path from host file system to the container file system. This option allows the scan results and inventory data that is collected by the host system to be made available for processing by the `build-inventory-evidence` script that runs within the container. Also, the input file is, for example: devsecops-input.yaml.

[`/opt/one-pipeline/tools/build-inventory-evidence.py`] is the fully qualified file system path where the `build-inventory-evidence` script is located within the DevSecOps base image container image.

[`--input-path`] option of the Python `build-inventory-evidence` script is the fully qualified file system path to the DevSecOps input file. By default, the `build-inventory-evidence` script processes both the inventory entries and evidence.

[`--inventory-only`] option of the Python `build-inventory-evidence` script is a directive to process the input file to build only the inventory entries.

[`--evidences-only`] option of the Python `build-inventory-evidence` script is a directive to process the input file to build the evidence only.

The `build-inventory-evidence` script runs within the context of container runtimes like Docker, Podman. Therefore, it is required that the container host must use a mechanism to mount the host file-system path where the DevSecOps Input File is located and the host file-system directory that contains scan results from various tools.

## DevSecOps input file
{: #devsecops-build-inventory-evidence-script-input}

The YAML format input file contains details that are required to populate the **inventory** and **evidence** data that is required by the DevSecOps CD Process.

The file contains information that is structured in four key sections:

1. **artifacts** - Information about build artifacts (container images, binaries, or jars) that are required to be updated in the inventory.
2. **sourceRepositories** - Information about source code that was built resulting in the build artifacts.
3. **evidences** - Information about various scans that were carried out over the source code or build artifacts.
4. **environmentProperties** - Information that is required by the `build-inventory-evidence` script to carry out the process (evidence repository URL, or inventory repository URL.)

The following code snippet shows the contents of the input file:

```json
artifacts:

- fullname: us.icr.io/my-registry/my-sampleapp-image:my-sampleapp-image-tag@my-sampleapp-image-digest
  type: image
  digest: my-sampleapp-image-digest
  name: app-image-1
  tag: my-sampleapp-image-tag
  provenance: us.icr.io/my-registry/my-sampleapp-image:my-sampleapp-image-tag@my-sampleapp-image-digest
  signature: e915a91a91677c0afb4518960e737fa640bba1bdfc87aba1cbaa711271927f4b
  version: 1
  sourceRepositoryNameRef: source-repository-1
​
sourceRepositories:
​
-  url: https://us-south.git.cloud.ibm.com/my-org/my-sampleapp
   branch: master
   ref: commit-hash
   name: source-repository-1
​
evidences:

- evidenceType: com.ibm.cloud.image_vulnerability_scan
  tool: va
  artifactNameRefs:
  - app-image-1
  status: failure
  attachments:
  - /path/to/container/directory/cr-va-1.json
  - /path/to/container/directory/cr-va-2.json

- evidenceType: com.ibm.static_scan
  tool: gosec
  sourceRepositoryNameRefs:
  - source-repository-1
  status: success
  attachments:
  - /path/to/container/directory/gosec-1.json
  - /path/to/container/directory/gosec-2.json

environmentProperties:

  PIPELINE_RUNTIME: Tekton
  PIPELINE_RUN_URL: https://cloud.ibm.com/devops/pipelines/tekton/c74e89a3-35a1-436a-893c-ca5c8469cf04/runs/e4059243-9128-4259-bce6-63a0b1230bde?env_id=ibm:yp:us-south
  PIPELINE_RUN_ID: e4059243-9128-4259-bce6-63a0b1230bde
  GITHUB_API_URL: https://github.ibm.com/api/v3
  EVIDENCE_REPO_URL: https://github.ibm.com/myorg/evidence-repository
  INVENTORY_REPO_URL: https://github.ibm.com/myorg/inventory-repository
  INCIDENT_REPO_URL: https://github.ibm.com/myorg/incident-repository
  PIPELINE_LOGS_PATH: /path/to/container/directory/
  COS_BUCKET_NAME: cos-evidence-locker
  COS_ENDPOINT: s3.us-south.cloud-object-storage.appdomain.cloud

```
{: codeblock}

### Artifact
{: #devsecops-build-inventory-evidence-script-artifact}

|Key | Description |
|:----------|:----------|
|`fullname`| Full name of the generated artifact `<repository>/<repository-namespace>/<artifact-name>:<artifact-tag>@<artifact-digest>`|
|`type`| Type of the artifact. Possible values: image, generic|
|`digest`| Digest of the artifact (for example, sha256 or sha512)|
|`name`| Unique name to identify the artifact object within the context of the `build-inventory-evidence` script |
|`tag`| Tag of the artifact|
|`provenance`| URL pointing to the artifact (for example, built image)	|
|`signature`|  |
|`sourceRepositoryNameRef`| The name of the sourceRepository object within input YAML that is associated with the generated artifact |
{: caption="Table 1. Artifact" caption-side="top"}

### Source repositories
{: #devsecops-build-inventory-evidence-script-repos}

A source repository object is metadata that is related to the SCM (Source Code Management) repository that contains the application source code. The source repository also captures information that is related to the SCM branch and commit reference. The commit reference contains the latest changes to the application source code, which triggered the CI pipeline.

Each deployable artifact that is generated by the CI pipeline can be traced back to the changes to the application source code. These changes are uniquely identified from the branch name and commit reference that belongs to that repository.

|Key | Description |
|:----------|:----------|
|`url`| SCM Repository URL |
|`branch`| SCM Repository branch that triggered the CI Process  |
|`ref`| SCM Repository commit reference that triggered the CI Process |
|`name`| Unique name to identify the sourceRepository object within input YAML |
{: caption="Table 2. Source repository metadata" caption-side="top"}

### Evidence
{: #devsecops-build-inventory-evidence-script-evidence}

An evidence object is a set of information for the security scan or test that is performed on either an artifact or source code repository. For example:

- A scan or test that is run on source code (for example, unit-test or static-scan) creates evidence that relates to the source code repository.
- A scan or test that is run on container artifact or image that is built from the source (for example, image-vulnerability, image-signing) creates evidence that relates to the artifact.

|Key | Description |
|:----------|:----------|
|`evidenceType`| Refer to the Supported Evidence Types. |
|`tool`| Refer to the Supported Security Scan Tool Types. |
|`artifactNameRefs`| List of name of the artifact object within input YAML, the evidence relates or belongs to. |
|`sourceRepositoryNameRefs`| List of name of the sourceRepository object within input YAML, the evidence relates or belongs to. |
|`status`| Scan the status of the evidence. Possible values: success, failure, pending. |
|`attachments`| The path to the attachments related to the evidence (for example, scan result JSON, or scan logs. |
{: caption="Table 3. Evidence objects" caption-side="top"}

> An evidence can either be collected against a list of sourceRepositoryNameRefs or artifactNameRefs but not both.

#### Supported Evidence Types
{: #devsecops-build-inventory-evidence-script-evidence-type}

The `build-inventory-evidence` script currently supports the following evidence types along with the supported tool values. If an evidence entry contains a tool type that is unsupported, processing of the attachment and consequently issue processing is skipped. However, the `build-inventory-evidence` script continues the evidence collection without an attachment or issue processing. Similarly, if evidence contains a tool type that is supported but the `build-inventory-evidence` script fails to process the attachment, processing of the attachment and issue processing is skipped. The `build-inventory-evidence` script also continues the evidence collection without an attachment or issue processing.


|Evidence Type Value| Supported Tools Values|
|:----------|:----------|
|`com.ibm.unit_test`|  |
|`com.ibm.detect_secrets`| `detect-secrets` |
|`com.ibm.branch_protection`|  |
|`com.ibm.static_scan`| `gosec`, `sonarqube`, `checkov`, `tfsec`, `fips-scanner`, `contrast-sast` |
|`com.ibm.code_vulnerability_scan`| `cra`,  `mend`, `cra-tf`  |
|`com.ibm.code_bom_check`|  |
|`com.ibm.code_cis_check`| `cra-cis` |
|`com.ibm.cloud.image_vulnerability_scan`| `va`, `xray`, `cims`, `sysdig`|
|`com.ibm.cloud.image_signing`|  |
|`com.ibm.dynamic_scan`| `owasp-zap`, `owasp-zap-ui`  |
|`com.ibm.acceptance_tests`|  |


### Environment properties
{: #devsecops-build-inventory-evidence-script-envprop}

Table 4 lists the mandatory environment properties that are required by the `build-inventory-evidence` script to interact with the different SCM repositories.

|Key | Description | Required/Optional |
|:----------|:----------|:----------|
|EVIDENCE_REPO_URL| URL of the Evidence Git Repository | Required |
|INVENTORY_REPO_URL| URL of the Inventory Git Repository | Required |
|INCIDENT_REPO_URL| URL of Incident Issues Git Repository | Required |
|GITHUB_API_URL| URL to access GitHub API Endpoint. For e.g. https://github.ibm.com/api/v3 | Required |
|PIPELINE_RUNTIME| Name of the pipeline runtime where the CI subprocess was run. For for example Jenkins, Travis, Tekton | Required |
|PIPELINE_RUN_URL| URL of the CI subprocess to which the inventory and evidence belongs | Required |
|PIPELINE_RUN_ID| Unique identifier of the CI subprocess to which the inventory and evidence belongs| Required |
|PIPELINE_LOGS_PATH| Path within the directory containing pipeline logs | Optional |
|COS_BUCKET_NAME| Cloud Object Store Bucket Name to be used as Evidence Locker | Optional |
|COS_ENDPOINT| Cloud Object Store Instance Endpoint | Optional |
{: caption="Table 4. Environment properties" caption-side="top"}

- **PIPELINE_LOGS_PATH** - If provided, the script uploads the pipeline logs contained within the directory to the GIT/CLOUD OBJECT STORAGE Evidence Locker and collects the evidence of type `com.ibm.pipeline_run_data`. The script skips the collection of the evidence if the environment property is either not defined or is defined but not set.

- **COS_BUCKET_NAME** - If provided, the script uploads the evidence to Cloud Object Storage Evidence Locker. The script skips the upload of evidence to COS Evidence Locker if the environment property is either not defined or is defined but not set.

- **COS_ENDPOINT** - If provided, the script uploads the evidence to COS Evidence Locker. The script skips the upload of evidence to COS Evidence Locker if the environment property is either not defined or is defined but not set.

## DevSecOps evidence attachment directory
{: #devsecops-build-inventory-evidence-script-evidence-att}

The `build-inventory-evidence` script requires a path to a file system directory that contains files from different scan results like sonarqube, or icr-va. Each evidence object in the DevSecOps input File can reference one or more attachments. The `attachment` object within the `evidence` object is an array of file system paths (relative to the container file-system). Each path is a location of the scan results files that are produced after running the tool that is in the `tool` key.

```yaml
  - evidenceType: com.ibm.cloud.image_vulnerability_scan
    tool: va
    artifactRef: app-image-1
    status: failure
    pipeline_run_id: ci-process-id
    attachment:
      - /path/to/container/directory/cr-va-1.json
      - /path/to/container/directory/cr-va-2.json
```
{: codeblock}

## `build-inventory-evidence` script internals
{: #devsecops-build-inventory-evidence-script-int}

The `build-inventory-evidence` script allows users to populate the inventory rRepository and evidence repository as required by the DevSecOps Processes.

## Inventory processing
{: #devsecops-build-inventory-evidence-script-inv-proc}

The `artifacts` object of the DevSecOps input file is the key input for inventory processing. However, because each generated artifact is built from some application source code, there is an internal dependency of each artifact on a `sourceRepositories` object.

The artifacts object is an array of artifacts that composes the iInventory required by the CD process. During processing, the `build-inventory-evidence` script sequentially:

- Parses each of these artifact objects.
- Links the object to ca corresponding source repository object.
- Creates an inventory entry in the inventory repository as specified by `INVENTORY_REPO_URL` environment properties.

Sample resulting inventory entry:
```json
{
  "artifact": "us.icr.io/my-registry/my-sampleapp-image:my-sampleapp-image-tag",
  "buildnumber": "ci-process-subprocess-id",
  "commit_sha": "commit-hash", # pragma: allowlist secret
  "name": "app-image-1",
  "pipeline_run_id": "ci-process-id",
  "provenance": "us.icr.io/my-registry/my-sampleapp-image:my-sampleapp-image-tag@my-sampleapp-image-digest",
  "repository_url": "https://us-south.git.cloud.ibm.com/my-org/my-sampleapp",
  "sha256": "my-sampleapp-image-digest",
  "type": "image",
  "version": "20221201-132640-103"
}
```
{: codeblock}

## Evidence processing
{: #devsecops-build-inventory-evidence-script-evid-proc}

The `evidences` object of the DevSecOps input file is the key input for evidence processing. However, because each evidence refers to a scan that was carried out either on the application source code or the artifact, there is an internal dependency of each evidence to either an `artifacts` object or to a `sourceRepositories` object.

The `evidences` object is an array of evidence that creates evidence in the evidence repository as required by the CD process. During processing, the `build-inventory-evidence` script sequentially:

- Parses each of these `evidences` objects.
- Links the objects to the corresponding artifact or source repository object.
- Processes the attachments and creates incident issues in the Incident Repository as specified by `INCIDENT_REPO_URL` environment properties.
- Creates an evidence entry in the evidence repository as specified by `EVIDENCE_REPO_URL` environment properties.

Sample resulting evidence entry:
```json
{
  "__comment": "The following is an evidence example (this comment key is not part of the output)",
  "version": "2",
  "id": "5448379261ae15c9bbaaa4f028e4d0327ba95767c5c90467d41f7275cd1277b9", # pragma: allowlist secret
  "date": "2022-12-02T19:54:11.438Z",
  "evidence_type_id": "com.ibm.image_vulnerability_scan",
  "evidence_type_version": "20221202-135249-111",
  "details": {
    "result": "failure",
    "tool": "va"
  },
  "origin": {
    "toolchain_crn": "process-name",
    "pipeline_id": "process-id",
    "pipeline_run_id": "subprocess-id",
    "pipeline_run_url": "process-url"
  },
  "assets": [
    {
      "hash": "my-sampleapp-image-digest", # pragma: allowlist secret
      "uri": "us.icr.io/my-registry/my-sampleapp-image:my-sampleapp-image-tag:my-sampleapp-image-tag@my-sampleapp-image-digest",
      "url": "https://us-south.git.cloud.ibm.com/my-org/enotfound-evidence/blob/master/raw/assets/1b/776a507c64c4ee845b7f49084794eb510e48aeb370cf2775b671fb23ca9fed/index.json"
    }
  ],
  "issues": [
    "https://us-south.git.cloud.ibm.com/my-org/my-sampleapp-issues/issues/410",
    "https://us-south.git.cloud.ibm.com/my-org/my-sampleapp-issues/issues/411",
    "https://us-south.git.cloud.ibm.com/my-org/my-sampleapp-issues/issues/412",
    "https://us-south.git.cloud.ibm.com/my-org/my-sampleapp-issues/issues/413"
  ],
  "attachments": [
    {
      "hash": "421abcef3d99ef5b66f3ed5e0ce2c094d67e993097810ffb42959c734a477af1", # pragma: allowlist secret
      "url": "https://us-south.git.cloud.ibm.com/my-org/my-sampleapp-evidence/blob/master/raw/attachments/42/1abcef3d99ef5b66f3ed5e0ce2c094d67e993097810ffb42959c734a477af1/content"
    }
  ]
}
```
{: codeblock}

</staging>

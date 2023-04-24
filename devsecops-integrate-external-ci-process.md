---

copyright:
  years: 2021, 2023
lastupdated: "2023-01-10"

keywords: DevSecOps, jenkins, travis, ci

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Introduction
{: #devsecops-integrate-external-ci-process}

This article describes usage of the utility that allows users to integrate their CI Process/Pipelines (built using Jenkins, Travis etc.) with the IBM Reference Implementation - DevSecOps CD (Continuous Deployment) Process/Pipelines. 

In typical DevSecOps workflows, CI process scans the source code for vulnerabilities and builds the deployable artifacts from source code. The CD process uses scan results available from CI process to determine the deployment readiness of the artifacts before beginning the deployment. This workflow requires a common storage media where CI process can store scans details like scan/build results, scan/build logs etc. also commonly known as evidences and deployable artifact detail like container images urls, deployment yamls also commonly referred to as inventory entries. The evidences of scans and builds stored by CI process are gathered by CD process to compute the deployment eligibility of the inventory state associated with the build. The evidence and inventory information are stored in a versioned system like Git and are referred to as Evidence Repository and Inventory Repository within the context of CI and CD processes.

The utility described here automates the process of building the inventory and evidence data as required by the IBM Reference Implementation - DevSecOps CD process. An external CI system or automation can run the utility with appropriate set of inputs to populate the inventory repository with deployable artifact information and the evidence repository with information about evidences of scans run during CI process.

# Usage

The utility is a python script contained within the DevSecOps baseimage container image. DevSecOps container image, developed and maintained by IBM implements the core functionality of the DevSecOps CI/CD/CC pipeline/processes. The image is maintained within IBM Cloud Container Registry (ICR) and is pulled by the Tekton pipelines during the pipeline execution. Like Tekton, any CI process or automation that has appropriate permission to IBM Cloud Container Registry (ICR) can also pull the DevSecOps baseimage container image. The utility runs within the DevSecOps baseimage container and hence requires a Container Runtime like Docker, Podman etc.

The utility requires 2 set of inputs:

   1. An input file containing structured details about built artifacts and evidences built by CI system.
   2. An attachments directory containing scan results of different tools supported by DevSecOps pipelines.

Since the utility runs within the context of container runtime, the container host should utilize the mechanism to mount the file-system path where the DevSecOps Input File is located and the directory that contains scan results from various tools to process the evidences.

To illustrate, a sample run of the utility with Docker command line should look like below. Notice the DevSecOps Baseimage Container Image being used to spin up the docker container. The sample illustrates pulling up a specific tag `2.76.1_commons-0.29.0` and digest `sha256:6f639e188e9acc228034ef8fcfa3ca9ba6c5af61b90ed87fcd9f04493451c664` of the `devsecops-baseimage` image. It's always recommended to use latest version of the DevSecOps baseimage that contains updated code for the utility.

```bash
docker run -it -v /path/to/host/directory:/path/to/container/directory icr.io/continuous-delivery/toolchains/devsecops/devsecops-baseimage:2.76.1_commons-0.29.0@sha256:6f639e188e9acc228034ef8fcfa3ca9ba6c5af61b90ed87fcd9f04493451c664
 python3 /opt/one-pipeline/tools/build_inventory_evidence.py --input_path /path/to/container/directory/devsecops-inputs.yaml

where:

[-it] option allows the standard output of the container to be made available to the host in an interactive manner.

[-v] option mounts a directory path from host file system to the container file system. This allows the scan results collected by host system to be made available for processing by the utility running within the container. Also, the input file (for example: devsecops-input.yaml)

[/opt/one-pipeline/tools/build_inventory_evidence.py] is the fully qualified file system path where the utility is located within the DevSecOps baseimage container image.

[--input_path] option of the python utility is the fully qualified file system path to the DevSecOps Input File. By default, the utility process both the inventory entries and evidences. 

[--inventory-only] option of the python utility is a directive to process the input file to build only the inventory entries.

[--evidences-only] option of the python utility is a directive to process the input file to build only the evidences.
```

## DevSecOps Input File

The input file (YAML format) contains details required to populate the **inventory** and **evidence** data required by the DevSecOps CD Process.

In summary, the file is contains information structured within 4 key sections:
1. **artifact** - Information related to build artifacts (container images, binaries, jars etc.) required to be updated in the inventory
2. **sourceRepositories** - Information related to source code that was built resulting in the build artifacts 
3. **evidences** - Information related to various scans that were carried out over the source code or build artifacts
4. **environmentProperties** - Information required by the utility itself to carry out the process ( evidence repository url, inventory repository url etc.) 

Below code snippet provides a sample illustration of the contents of the input file.

```yaml

artifact:
​​
- fullname: us.icr.io/my-registry/my-sampleapp-image:my-sampleapp-image-tag@my-sampleapp-image-digest
  type: image
  digest: my-sampleapp-image-digest
  name: app-image-1
  tag: my-sampleapp-image-tag
  provenance: us.icr.io/my-registry/my-sampleapp-image:my-sampleapp-image-tag@my-sampleapp-image-digest
  signature: e915a91a91677c0afb4518960e737fa640bba1bdfc87aba1cbaa711271927f4b
  build-number: ci-process-subprocess-id
  pipeline-run-id: ci-process-id
  app: my-sampleapp
  sourceRepositoriesRef: https://us-south.git.cloud.ibm.com/my-org/my-sampleapp.git#commit-hash
​
sourceRepositories:
​
-  url: https://us-south.git.cloud.ibm.com/my-org/my-sampleapp
   branch: master
   ref: commit-hash  
   path: my-sampleapp
   buildnumber: ci-process-subprocess-id
   name: source-repository-1
​
evidences:
  - evidence-type: com.ibm.cloud.image_vulnerability_scan
    tool: va
    artifactRef: app-image-1
    status: failure
    pipeline_run_id: ci-process-id
    attachment:
      - /path/to/container/directory/cr-va-1.json
      - /path/to/container/directory/cr-va-2.json

  - evidence-type: com.ibm.static_scan
    tool: gosec
    sourceRepositoryRef: source-repository-1
    status: success
    pipeline_run_id: ci-process-id
    attachment:
      - /path/to/container/directory/gosec-1.json
      - /path/to/container/directory/gosec-2.json

environmentProperties:
​
  GHE_TOKEN: 
  EVIDENCE_REPO_URL:
  INVENTORY_REPO_URL:
  INCIDENT_REPO_URL: 

```
### artifact

|Key | Description | Optional |
|:----------|:----------|:----------|
|fullname| Full name of the generated artifact <repository>/<repository-namespace>/<artifact-name>:<artifact-tag>@<artifact-digest>|
|type| Type of the artifact. Possible values: image, generic|
|digest| Digest of the artifact (e.g sha256 or sha512)|
|name| Unique name to identify the artifact object within the context of the utility |
|tag| Tag of the artifact|
|provenance| URL pointing to the artifact (e.g built image)	|
|signature||
|build-number| The id of the CI subprocess that generated the artifact |
|pipeline-run-id| The id of the CI process that generated the artifact |
|app| The logical name of the application that the built artifact belongs to |
|sourceRepositoriesRef| The name of the sourceRepository object within input YAML that is associated with the generated artifact |


### sourceRepositories

A source repository object is metadata related to the SCM (Source Code Management) repository containing the application source code. Inaddition, it also captures information related to the SCM branch and commit reference that contains the latest changes to the application source code which triggerred the CI (Continuous Integration) process/pipeline.

Each deployable artifact generated by the CI process/pipeline has its origin that can be traced back to the changes to the application source code. These changes are uniquely identified from the branch name and commit reference belonging to that repository.

|Key | Description | Optional
|:----------|:----------|:----------|
|url| SCM Repository URL | No |
|branch| SCM Repository branch that triggered the CI Process  |
|ref| SCM Repository commit reference that triggered the CI Process |
|path| SCM Repository subpath to the source code that was built with the CI Process |
|name| Unique name to identify the sourceRepository object within input YAML |

### evidences

An evidence object is a set of information for the security scan or test performed on either an artifact or source code repository. For example:

- A scan/test that is run on source code (e.g unit-test or static-scan) creates an evidence that relates to the source code repository.
- A scan/test that is run on container artifact/image built from the source (e.g image-vulnerability, image-signing) creates an evidence that relates to the artifact.

An evidence object can either belong to a artifact object or to a 

|Key | Description | Optional |
|:----------|:----------|:----------|
|evidence-type| Refer to the Supported Evidence Types |
|tool| Refer to the Supported Security Scan Tool Types |
|artifactRef| Name of the artifact object within input YAML, the evidence relates or belongs to |
|sourceRepositoriesRef| Name of the sourceRepositories object within input YAML, the evidence relates or belongs to |
|status| Scan status of the evidence. Possible values: success, failure, pending |
|pipeline-run-id| The id of the CI process that generated the artifact |
|attachment| The path to the attachements related to the evidence (for e.g. scan result jsons, scan logs etc.) |

#### Supported Evidence Types

The utility currently supports the following evidence types:

- `com.ibm.unit_tests`
- `com.ibm.detect_secrets`
- `com.ibm.branch_protection`
- `com.ibm.static_scan`
- `com.ibm.code_vulnerability_scan`
- `com.ibm.code_bom_check`
- `com.ibm.code_cis_check`
- `com.ibm.cloud.image_vulnerability_scan`
- `com.ibm.cloud.image_signing`
- `com.ibm.dynamic_scan`
- `com.ibm.acceptance_tests`
- `com.ibm.prod_change_request`
- `com.ibm.close_change_request`

#### Supported Security Scan Tool Types

The utility currently supports the following security scanning tools:

- `cra` IBM Code Risk Analyzer
- `va` Vulnerability Advisor for IBM Cloud Container Registry
- `gosec` GoLang Security Scanner
- `xray` JFrog Xray - Vulnerability Scanning & Container Security
- `owasp-zap` OWASP Zed Attack Proxy (ZAP)
- `owasp-zap-ui` OWASP Zed Attack Proxy UI (ZAP UI)
- `sonarqube` 'SonarQube scan

Incase evidence contains a tool type that is unsupported, processing of the attachment and consequently issue processing is skipped. However, the utility continues the evidence collection without an attachment or issue processing. 

Similarily, incase evidence contains a tool type that is supported but the utility fails to process the attachment, processing of the attachment and issue processing is skipped. Likewise, the utility continues the evidence collection without and attachment or issue processing.

### environmentProperties

A list of mandatory environment properties required by the utility to interact with the different SCM Repositories

|Key | Description | Optional |
|:----------|:----------|:----------|
|GHE_TOKEN| The Personal Access Token with access to Evidence Repository, Inventory Repository and Incident/Issues Repository |
|EVIDENCE_REPO_URL| URL of the Evidence Repository |
|INVENTORY_REPO_URL| URL of the Inventory Repository |
|INCIDENT_REPO_URL| URL to Incident/Issues Repository |
|PIPELINE_RUN_ID| Unique Identifier of the CI Subprocess to which the Inventory and Evidences belongs to |
|PIPELINE_RUN_URL| URL of the CI Subprocess to which the Inventory and Evidences belongs to |
|PIPELINE_ID| Unique Identifier of the CI Process/Pipeline to which the Inventory and Evidences belongs to |
|TOOLCHAIN_CRN| Name of the CI Process/Pipeline |

## DevSecOps Evidences Attachment Directory

The utility requires path to a file-system directory that contains files from different scan results like sonarqube, icr-va etc. Each evidence object in the DevSecOps Input File can have reference to one or more attachments. The `attachment` object within the `evidence` object is an array of file-system paths (relative to the container file-system). Each of these paths is a location of the scan results file/s produced after running the tool mentioned within `tool` key.

```yaml
  - evidence-type: com.ibm.cloud.image_vulnerability_scan
    tool: va
    artifactRef: app-image-1
    status: failure
    pipeline_run_id: ci-process-id
    attachment:
      - /path/to/container/directory/cr-va-1.json
      - /path/to/container/directory/cr-va-2.json
```

# Utility Internals

As discussed earlier, the utility allows users to populate the Inventory Repository and Evidence Repository as required by the DevSecOps Processes.

## Inventory Processing

The `artifacts` object of the DevSecOps Input File is the key input for Inventory Processing. However, since each generated artifact is built from some application source code, there is an inherent internal dependency of each artifact to a `sourceRepositories` object. 

The artifacts object is an array of artifacts that composes the Inventory required by CD Process. During processing the utility sequentially:

- Parses each of these artifacts object.
- Links the object to corressponding sourceRepository object.
- Creates an inventory entry in the Inventory Repository as specified by `INVENTORY_REPO_URL` environmentProperties

A sample inventory entry in Inventory Repository looks like below:

```json
{
  "artifact": "us.icr.io/my-registry/my-sampleapp-image:my-sampleapp-image-tag",
  "build_number": "ci-process-subprocess-id",
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

## Evidence Processing

The `evidences` object of the DevSecOps Input File is the key input for Evidence Processing. However, since each evidence refers to a scan that was carried out either on the application source code or the artifact, there is an interent internal dependency of each evidence to either a `artifacts` object or to a `sourceRepositories` object. 

The evidences object is an array of evidences that creates evidences in the Evidence Repository as required by CD Process. During processing the utility sequentially:

- Parses each of these evidences object.
- Links the object to corressponding artifact or sourceRepository object.
- Process the attachments and from those results creates incident issues in the Incident Repository as specified by `INCIDENT_REPO_URL`
- Creates an evidence entry in the Evidence Repository as specified by `EVIDENCE_REPO_URL` environmentProperties

A sample inventory entry in Evidence Repository looks like below:

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
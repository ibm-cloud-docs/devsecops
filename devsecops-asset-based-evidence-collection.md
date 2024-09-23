---

copyright:
  years: 2023
lastupdated: "2023-11-07"

keywords: DevSecOps, evidence collection, generic asset, inventory, application, microservice

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Asset creation and Evidence collection in DevSecOps
{: #devsecops-asset-evidence-collection}

In the realm of DevSecOps process, asset creation and Evidence collection ensures that security seamlessly integrates with every phase of the software development lifecycle. This approach embodies the concept of "shift-left," where security is not an afterthought but an inherent part of the development process. DevSecOps, with its DevSecOps Continuous Integration and Continuous Delivery (CI/CD), empowers teams to identify vulnerabilities at the earliest stages, facilitating the release of compliant software.

Asset creation and evidence collection play a pivotal role in the Onepipeline ecosystem. To remain compliant with security standards, it's imperative to follow the guidelines outlined in this comprehensive guide. By doing so, you can ensure that your software development process is not only efficient but also secure from the ground up.

## Defining an asset in DevSecOps
{: #devsecops-asset-definition}

In the context of DevSecOps, an asset is a fundamental entity that is subjected to rigorous testing and scanning. These assets encompass various forms such as existing Git commits in repositories to Docker images. An asset is a focal point of the evidence collection process, representing the object on which a scan or test has been conducted. Remarkably, DevSecOps's flexibility extends beyond traditional code and images, including abstract entities like pipeline runs or COS (Cloud Object Storage) buckets, as long as evidence can be gathered for them.

[Asset Types](cloud-docs/devsecops/blob/source/images/asset-types.png){: caption="Figure 1. Types of Assets" caption-side="bottom"}

For optimal clarity, assets must be distinctly identified. Here are some examples:
- Commit Asset: Comprising the repository and commit hash: https://repo.url/org/repo#<COMMIT-SHA>
- Image Asset: Encompassing the Docker image and its digest: docker://registry.URL/name@sha256:<SHA256-DIGEST>
- Pipeline Run Asset: Denoting the pipeline and run ID:

pipelinerun://<PIPELINE_ID>/<RUN_ID>

- Other Assets: Require a fully qualified URL. For example, a commit asset with a file name:

https://repo.url/org/repo#<COMMIT-SHA>/<filename>
an artifact asset: <artifactory-url>/artifact.<filetype>

It is advisable not to use mutable labels like tags, branches, or other references, as they may be readdressed or moved, leading to potential complications.
{: note}

## Standardizing Inventory for Generic Assets
{: #devsecops-asset-inventory}

To maintain consistent and effective inventory management, it is crucial to validate specific fields when employing the `save_artifact` and `cocoa inventory` add commands. Adhering to the following guidelines will ensure proper actions based on the provided information:
1. Type: Reserved keywords to indicate the type of the artifact:
    o	commit for commit assets
    o	image for image assets
    o	generic for pipeline-run assets
For any other type of artifact you can choose a type which is a string that appropriately indicate it inherent type for eg. deployment for deployment files, tar for the tar files

2.	Name: Maintain a static artifact name throughout the pipeline run. Avoid incorporating dynamic elements like commit hashes in the name to ensure the correctness of the auto-closing issues.
    Example:
        o	us.icr.io/my-registry/      my-app:20230828074614-master-commit-1@sha256:sha256-1
        o	my-app-202304211845444721_IKS_deployment

3.	Provenance: Provide a fully qualified domain URL to establish the artifact's origin.
    Example:
        o	us.icr.io/my-registry/my-app:20230828074614-master-commit-1@sha256:sha256-1
        o	https://raw.github.ibm.com/org/my-app/commit-1/deployment_iks.yml

4.	Digest: Follow the defined regex pattern: Start with 'sha256,' followed by a colon (:), and then the actual SHA256 hash (sha256:<sha256>).
    Example:
        o	sha256:sha256-1 <actual string>

5.	Signature: If the artifact is signed, include the relevant signature information. Following these standardized guidelines enhances the inventory management process, ensuring accurate tracking and retrieval of assets.


## Steps to store the asset information
{: #devsecops-store-asset}

1.	[Use the save_repo command](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#save_repo):

    Use the following format to save the asset information for a commit in the repository. which is necessary for associating the build asset with the commit.

    Examples:

    ```sh
save_repo app-repo \
url=https://github.ibm.com/org/my-app \
path=my-app \
    commit=commit1 \
       branch=master \
    buildnumber=1
    ```
{: codeblock}

2.	[Use the Save_artifact command](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#save_artifact) to store information for various asset types, such as image and deployment, by using the `save_artifact` command

Examples:

     Use of `save_artifact` command for image asset:

    ```sh
    save_artifact app-image \
    type=image \
    name=us.icr.io/my-registry/my-app:20230828074614-master-commit-1@sha256:sha2561\
    digest=sha256:sha2561\
    tags=mytag1 \
    source=https://github.ibm.com/org/my-app/commit-1 \
    signature=sign-1
    ```
    {: codeblock}



	Use of `save_artifact` command for non-image asset:

    ```sh
    save_artifact artifact-1 \
    name=my-app_IKS_deployment \
    type=deployment \
    signature=sign2 \
    deployment_type=IKS \
    digest=sha256:sha2562\
    provenance=https://raw.github.ibm.com/org/my-app/commit-1/deployment_iks.yml`
    ```
    {: codeblock}

## Steps to create inventory entry from the asset information
{: #devsecops-inventory-asset-creation}

Use the [cocoa inventory add](/docs/devsecops?topic=devsecops-cd-devsecops-cli#inventory-add):

|Argument|	Description|	Required/Optional|	Used in Code/Remarks|
| ---------------- | ------------------------------------------------ | ---------- | ------------------------- |
|artifact |Artifact name.	|[string] [required]|	This field is used for the subject in the issue management
|version|	The version of the given application|	[string] [required]| Version of the artifact|
|repository-url|	The repository of the application|	[string] [required]|	URL of the repository where this asset is built|
|pipeline-run-id|	The id of the pipeline run|	[string] [required]|	Used to scope the evidences in CD and CC|
|commit-sha|	The exact commit of the application version|	[string] [required]	|commit of the repo URL where this asset is built|
|name|	The name of the application	|[string] [required]|	File name or basically the inventory entry for the asset|
|build-number|	The build number of the build|	[string] [required]|	Build number of the asset created, used for the insights update|
|type|	Type of the artifact: one among container image, virtual, image, file, package|	[string] [required]	| Type of the asset, should be same the type saved using the save_artifact command before the collect evidence call|
|app-artifacts|	To have the additional information of the asset	|[string] [optional]||
|sha256|	The sha256 hash of the artifact|	[string] [required]|	used to define the path of the asset in the locker|
|provenance	|The fully qualified URL where artifact is stored|	[string] [required]	||
|signature|	The artifacts signature	|[string] [required]|	Will be used to verify the signature of the asset, need more clarification|
|environment|	The name of the environment where the entry should be added	|[string] [optional]	|the inventory branch where the entry would be added which is default to master|
{: caption="Table 1. Argument information" caption-side="top"}


Examples:

1.	Cocoa inventory add for an image asset:

    ```bash
    cocoa inventory add \
    --artifact=us.icr.io/my-registry/my-app:20230828074614-master-commit-1@sha256:sha2561\
    --name=my-app \
    --app-artifacts='{ "app": "my-app", "tags": "20230828074614-master-commit1" }' \
    --signature=sign1 \
    --provenance=us.icr.io/my-registry/my-app:20230828074614-master-commit-1@sha256:sha2561\
    --sha256=sha256:sha2561 \
    --type=image \
    --repository-url=https://github.ibm.com/org/my-app \
    --commit-sha=commit1 \
    --version=commit1 \
    --build-number=1 \
    --pipeline-run-id=pipeline-run-1\
    --org=Org\
    --repo=my-inventory-20230421184544472 \
    --git-provider=github \
    --git-token-path=./inventory-token \
    --git-api-url=https://github.ibm.com/api/v3
    ```
    {: codeblock}

2.	Cocoa inventory add for a non-image asset:
    ```bash
    cocoa inventory add \
    --artifact=my-app_IKS_deployment \
    --name=my-app_IKS_deployment \
    --app-artifacts='{ "app": "my-app", "tags": "20230828074614-master-commit1" }' \
    --signature=sign2 \
    --provenance=https://raw.github.ibm.com/org/my-app/commit-1/deployment_iks.yml \
    --sha256=sha256:sha2562 \
    --type=deployment \
    --repository-url=https://github.ibm.com/org/my-app \
    --commit-sha=commit1 \
    --version=commit1 \
    --build-number=1 \
    --pipeline-run-id=pipeline-run-1\
    --org=Org\
    --repo=my-inventory-20230421184544472 \
    --git-provider=github \
    --git-token-path=./inventory-token \
    --git-api-url=https://github.ibm.com/api/v3
        ```
    {: codeblock}

### Defining an Evidence in DevSecOps
{: #devsecops-evidence-defintion}

Evidence is an entry that proves a scan check or test of a specific type and tool was performed or is about to be performed. The Evidence contains pointers to issues, Assets, Scopes, and attachments but minimal data.

## Steps to collect evidence by using the asset’s information
{: #devsecops-asset-based-evidence-collection}

Use [`collect-evidence`](/docs/devsecops?topic=devsecops-devsecops-collect-evidence) for evidence collection on the preceding asset information

Examples:

1.	Evidence collection for an artifact asset:
    ```bash
      	collect-evidence \
            --tool-type "jest" \
            --status "success" \
            --evidence-type "com.ibm.unit_tests" \
            --asset-type "artifact" \
            --asset-key "artifact-1"
    ```
    {: codeblock}

2.	Evidence collection for a repo asset:
    ```bash
    collect-evidence \
        --tool-type "jest" \
        --status "success" \
        --evidence-type "com.ibm.unit_tests" \
        --asset-type "repo" \
        --asset-key "repo-1"
    ```
    {: codeblock}

In the CD pipeline, the deployment should refer to a provenance field instead of an artifact to download the artifact
{: note}

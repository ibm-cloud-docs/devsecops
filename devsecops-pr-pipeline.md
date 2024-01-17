---

copyright:
  years: 2021, 2023
lastupdated: "2023-05-02"

keywords: DevSecOps

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Pull request pipeline
{: #cd-devsecops-pr-pipeline}

Pull request pipeline runs a set of compliance status checks on a pull request for the specified application repository.
{: shortdesc}

Attempts to merge a pull request into the master branch might be blocked because of failed compliance status checks. Opening or updating a pull request against the master branch triggers the pull request pipeline to run. You can run your own setup for the pipelines and tests in [Custom scripts](/docs/devsecops?topic=devsecops-custom-scripts).

## Stages and tasks
{: #cd-devsecops-pipeline-order}

The table below lists the tasks run in a PR Pipeline. In addition, the table also provides an overview of each of these stages:

- **Task or Stage**: This refers to the name of the stage as defined within the `.pipeline-config.yaml` configuration file.

- **Short description**: This provides a concise explanation of the actions performed during the execution of the stage.

- **Customisation permissible**: This indicates whether users have the flexibility to modify or replace the default behavior of the stage by inserting a custom script in the `.pipeline-config.yaml` file.

- **Default Reference Implementation**: This indicates whether the DevSecOps pipelines come with a pre-defined or default implementation for the stage. Notably, for certain stages like `unit-tests` or `setup`, the DevSecOps pipeline doesn't offer any out-of-the-box implementation. Instead, users are required to provide custom scripts or code tailored to their application's requirements.

- **Evidence Collection**: This indicates whether the stage performs the collection of standard evidence. When DevSecOps **Pipeline** provide a reference implementation for a stage, evidence collection is performed out-of-the-box. However, if **User** choose to modify or replace these predefined stages, they must ensure that their custom implementations include appropriate evidence collection. The same responsibility falls on users for stages where the DevSecOps pipeline doesn't provide an out-of-the-box implementation, necessitating them to perform evidence collection. The column indicates the entity (**User/Pipeline**) responsible for carrying out the evidence collection.

- **Skip permissible (applicable to version >= v10)**: This indicates whether users can opt out of running this stage by setting the skip property to true in the ``.pipeline-config.yaml``. However, caution is advised when using this feature, especially for stages designed to collect evidence. Skipping such stages might lead to missing essential evidences for the build.

|Task or stage |Short description	| Customisation permissible in `.pipeline-config.yaml` | Default Reference Implementation | Evidence Collection Requirement|Skip permissible|
|:----------|:------------------------------|:------------------|--|--|--|
|`start` 		 |Set up the pipeline environment. 		|No		|Yes | NA | No |
|`setup`		 |Set up your build and test environment.			|Yes| No | NA |No|
|`detect-secrets`		|Run detect secrets scan on the application code.		|Yes		| Yes | NA |No|
|`unit-tests`|Run unit tests and app tests on app code.		|Yes | No | NA |Yes|
|`compliance-checks` 	 |Run Code Risk Analyzer scans and other compliance checks on app repos.   	|Yes		| Yes | NA |Yes|
|`finish`| Consolidate the pipeline status. | Yes | Yes | NA |Yes|
{: caption="Table 1. Pipeline order" caption-side="top"}

For more information about how to customize stages by using the `.pipeline-config.yaml` file, see [Custom scripts](/docs/devsecops?topic=devsecops-custom-scripts) and [Pipeline parameters](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm).

## Detect Secrets scan
{: #cd-devsecops-pr-pipeline-detect-secrets}

The [IBM Detect Secrets](https://github.com/IBM/detect-secrets) tool identifies where secrets are visible in app code. More information on setting up your repo for the scan is available [here](https://cloud.ibm.com/docs/devsecops?topic=devsecops-cd-devsecops-detect-secrets-scans).

## Scans and checks in compliance checks
{: #cd-devsecops-cd-pipeline-compliancechecks}

| Scan or check |  Description | 
|---------|------------|
| Code Risk Analyzer vulnerability scan | Finds vulnerabilities for all of the app package dependencies, container base images, and operating system packages. Uses the Code Risk Analyzer tool. |
| Code Risk Analyzer CIS check |  Runs [configuration checks](/docs/cli?topic=cli-cra-cli-plugin#deployment-command) on Kubernetes deployment manifests. Uses the Code Risk Analyzer tool.| 
| Code Risk Analyzer Bill of Material (BOM) check | The BOM for a specified repo that captures the pedigree of all of the dependencies. This BOM is collected at different granularities. For example, the BOM captures the list of base images that are used in the build, the list of packages from the base images, and the list of app packages that are installed over the base image. The BOM acts as a ground truth for the analytic results and can potentially be used to enforce policy gates. Uses the Code Risk Analyzer tool. |
| Mend Unified Agent vulnerability scan | The [Mend Unified Agent scanning tool](https://docs.mend.io/bundle/unified_agent/page/overview_of_the_unified_agent.html){: external} scans app repos' open source components for vulnerable libraries and source files. For more information, see [Configuring Mend Unified Agent scans](/docs/devsecops?topic=devsecops-cd-devsecops-mend-scans). |
| Repository compliance checking | Checks that branch protection settings are correct. |
{: caption="Table 2. Compliance scans and checks" caption-side="top"}
    
These scripts are run on all of the app repos that the pipeline is aware of. To add repos to these scans, use the [`pipelinectl`](/docs/devsecops?topic=devsecops-pipelinectl) interface that is provided in your setup stage.

For more information about the expected output from user script stages, see [Custom scripts](/docs/devsecops?topic=devsecops-custom-scripts).

## Task jobs
{: #cd-devsecops-pipeline-jobs}

| Task job |  Description | 
|---------|------------|
| `code-pr-start` | Clones app and DevSecOps repos, and sets the initial pending state for status checks on {{site.data.keyword.gitrepos}} repos. |
| `code-setup` | The placeholder for a user-defined setup custom script where the user can complete their pipeline setup. |
| `code-detect-secrets` |  Runs the detect secrets scan to identify where secrets are visible in app code. | 
| `code-unit-tests` |  The placeholder for a user-defined test custom script where the user can run their own tests. | 
| `code-pr-finish` | Runs all of the required compliance checks, comments the results to the pull request, and sets their result on the {{site.data.keyword.gitrepos}} repos. |
{: caption="Table 3. Compliance scans and checks" caption-side="top"}

## Merge pull requests with issues
{: #cd-devsecops-merge-pr}

You can use Administrator rights to merge pull requests with failed status checks to the repo. However, these pull requests register a `failure` result in their evidence for the failing task. This result is included in the evidence summary and change request description, and impacts the final compliance score on the Security and Compliance Center.

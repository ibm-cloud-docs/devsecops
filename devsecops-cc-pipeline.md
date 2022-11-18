---

copyright:
  years: 2021, 2022
lastupdated: "2022-11-18"

keywords: DevSecOps, cc pipeline, continuous compliance pipeline

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Continuous compliance pipeline
{: #devsecops-cc-pipeline}

The continuous compliance pipeline (CC pipeline) periodically scans the deployed artifacts and their source repositories.
{: shortdesc}

The CC pipeline processes the entries from the [inventory](/docs/devsecops?topic=devsecops-cd-devsecops-inventory) repo by using the `environment-tag` value to determine which is the latest deployed state to look at. After scanning and running checks on artifacts and source repositories, the pipeline creates a new incident issue or updates the existing incident issues in the incident repository. Finally, using these issues and the results, the pipeline collects evidence and summarizes the evidence, so the [Security and Compliance Center](https://cloud.ibm.com/security-compliance/overview){: external} can update the compliance status of the found artifacts.

## Stages and tasks
{: #devsecops-cc-pipeline-stages}

|Task or stage |Short description	|Customizable in `.pipeline-config.yaml` |
|:----------|:------------------------------|:------------------|
|`start` 		|Set up the pipeline environment. 		|No		|
|`setup`		|Set up your build and test environment.		|Yes			|
|`static-scan`		|Run static scan code on the application code.		|Yes		|
|`dynamic-scan` 		|Run dynamic scan on the application. 		|Yes			|
|`compliance-checks` 		|Run Code Risk Analyzer scans and other compliance checks on app repos.   	|Yes		|
|`scan-artifact` 		|Scan the built artifacts.   	|Yes			|
|`finish` 		|Collect, create, and upload the logs files, artifacts, and evidence to the evidence locker.   	|No			|
{: caption="Table 1. Continuous compliance pipeline stages and tasks" caption-side="top"}

For more information about how to customize stages by using the `.pipeline-config.yaml` file, see [Custom scripts](/docs/devsecops?topic=devsecops-custom-scripts) and [Pipeline parameters](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm) lists.

## Processing inventory for artifacts and repositories
{: #devsecops-cc-pipeline-start}

The start stage clones the inventory and processes the latest entries in the production environment. You can specify this environment by providing the following pipeline parameters:

|Name |Type |Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|`environment-tag`     |text   |Tag that represents the latest target environment in the inventory. Example: `prod_latest` or `us-south_prod_latest`    |Required           |
|`environment-branch`     |text   |Branch name that represents the target environment in the inventory. Example: `prod`    |Deprecated - prefer `environment-tag` instead           |
|`region-prefix`          |text   |Region name as prefix for the `latest` tag for the target environment. Example: `us-south`   |Deprecated - prefer `environment-tag` instead             |
{: caption="Table 2. Continuous compliance pipeline artifacts and repositories" caption-side="top"}

The inventory entries contain deployed artifacts and repository sources. The pipeline processes and collects the inventory entries and registers them for the pipeline run by using the following `pipelinectl` commands:

* [save_repo](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#save_repo)
* [save_artifact](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#save_artifact)

The start stage also clones the repositories found, each repo, and each commit pair. So, for example, `repo1` with commit `sha1` are cloned in a folder, but the pipeline clones the same `repo1` with commit `sha2` in a separate folder.

The pipeline registers artifacts and repositories for the pipeline run by using a simple naming notation and incrementing index, such as the following examples:
* `repo-1`, `repo-2`, `repo-3`
* `artifact-1` `artifact-2`, `artifact-3`

You can list these entries in the custom stages by using the following `pipelinectl` commands:

* [list_repos](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#list_repos)
* [list_artifacts](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#list_artifacts)

> Note: If you are retrieving the branch information in your CC pipeline by using the `pipelinectl` command `load_repo "$repo" branch`, it always returns `master` as branch.  Therefore, use `commit hashes` rather than branches.

## Setup stage
{: #devsecops-cc-pipeline-setup}

The setup stage in the CC pipeline runs the script that is located in the `setup` stage that is defined by your `.pipeline-config.yaml`. You can determine which pipeline that the script is running in by using the following command:

```text
get_env pipeline_namespace
```

This command returns either `cc`, `cd`, `ci`, or `pr`, depending on which pipeline is running. This way, you can reuse the setup script between pipelines if necessary.

## Static code scan
{: #devsecops-cc-pipeline-codescan}

The static code scan stage runs a static code analyzer tool on the specified app repo codebases.

CC pipeline provides the repos that are found in the inventory for the scanner.

You can use any of the following methods to add static code to your pipeline:

* Provide an already running SonarQube instance name, URL, and credentials by adding the SonarQube tool to your toolchain. The `static-scan` task runs a scan on the specified repos.
* Add your code to the `static-scan` custom stage in your `.pipeline-config.yaml` file for a custom implementation.

## Dynamic scan
{: #devsecops-cc-pipeline-dynamic-scan}

The Dynamic scan stage runs a dynamic application security testing tool to find vulnerabilities in the deployed application.

* Add your own dynamic scan code to the dynamic-scan custom stage in your `.pipeline-config.yaml` file for a custom implementation.

To learn more about configuring dynamic scan by using OWASP-ZAP, see [Configuring ZAP scan for CC pipeline](/docs/devsecops?topic=devsecops-zap-scans#zap-scan-for-cc).

## Scans and checks in compliance checks
{: #devsecops-cc-pipeline-compliance-checks}

| Scan or check |  Description |
|---------|------------|
| Detect secrets | The [IBM Detect Secrets](https://github.com/IBM/detect-secrets) tool identifies where secrets are visible in app code. |
| Code Risk Analyzer vulnerability scan | Finds vulnerabilities for all of the app package dependencies, container base images, and operating system packages. Uses the Code Risk Analyzer tool. |
| Code Risk Analyzer CIS check |  Runs configuration checks on Kubernetes deployment manifests. Uses the Code Risk Analyzer tool. |
| Code Risk Analyzer Bill of Material (BOM) check | The BOM for a specified repo that captures the pedigree of all of the dependencies. This BOM is collected at different granularities. For example, the BOM captures the list of base images that are used in the build, the list of packages from the base images, and the list of app packages that are installed over the base image. The BOM acts as a ground truth for the analytic results and can potentially be used to enforce policy gates. Uses the Code Risk Analyzer tool. |
| Mend Unified Agent vulnerability scan | The [Mend Unified Agent scanning tool](https://docs.mend.io/bundle/unified_agent/page/overview_of_the_unified_agent.html){: external} scans app repos' open source components for vulnerable libraries and source files. For more information, see [Configuring Mend Unified Agent scans](/docs/devsecops?topic=devsecops-cd-devsecops-mend-scans). |
{: caption="Table 3. Compliance scans and checks" caption-side="top"}

These scripts are run on all of the app repos that the pipeline is aware of. CC pipeline uses the [`pipelinectl save_repo`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#save_repo) interface to register repos found in the inventory entries, then uses the [`list_repos`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#list_repos) and [`load_repo`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#load_repo) commands to iterate over repos and send them to scanners.

For more information about the expected output from user script stages, see [Custom scripts](/docs/devsecops?topic=devsecops-custom-scripts).

## Artifact scan and sign
{: #devsecops-cc-pipeline-artifactscan}

The artifact scan stage provides default behavior for Docker images, with a customizable step:

* Container Registry Vulnerability Advisor scanning

CC pipeline uses the [`pipelinectl save_artifact`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#save_artifact) interface to register artifacts found in the inventory entries, then iterates over these artifacts, by using the [`list_artifacts`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#list_artifacts) and [`load_artifact`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#load_artifact) commands.

To get started with this stage, provide your artifacts for the pipeline by using the [`pipelinectl`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl) interface. You are not required to update the build scripts and the `.pipeline-config.yaml` configuration.

To use a different scan process or to process artifacts other than Docker images in icr.io, you can customize these stages by using the `.pipeline-config.yaml` configuration in your project.

## Collecting compliance data on build
{: #devsecops-cc-pipeline-collectdata}

The CC pipeline attempts to process the results from checks and scans, breaking down results to individual issues per every found CVE, vulnerability, or alert. The issues that are found by the CC pipeline are marked with a `continuous-compliance-check` label that identifies issues that are discovered on the prod environment.

The pipeline collects [evidence](/docs/devsecops?topic=devsecops-devsecops-evidence) on all checks and scans and stores them in the evidence locker. The evidence collector also saves the pipeline log files, the pipeline data itself, containing the Tekton definitions. The issues that are created according to scan and check that results are also attached to the evidence.

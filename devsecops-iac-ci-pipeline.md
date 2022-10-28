---

copyright:
  years: 2021, 2022
lastupdated: "2022-10-26"

keywords: DevSecOps, scan, inventory, compliance, infrastructure as code, iac

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Continuous integration pipeline for Infrastructure as Code
{: #cd-devsecops-iac-ci-pipeline}

The continuous integration pipeline for Infrastructure as Code (IaC) builds the deployable configuration from the IaC repositories (Terraform content).
{: shortdesc}

Before it builds artifacts, the pipeline checks that the code is scanned and tested, in the same way that pull requests are processed. Built artifacts are also scanned for vulnerabilities and signed in the pipeline before they are marked ready for release and deployment in the inventory. For more information, see [Inventory](/docs/devsecops?topic=devsecops-cd-devsecops-inventory). Unlike the pull request pipeline, the continuous integration pipeline collects evidence and result artifacts on each stage of the build, such as testing, scanning, and signing. This data correlates to the built artifacts and can be tracked through the deployment process and change management.

## Stages and tasks
{: #devsecops-iac-ci-pipeline-stages}

|Task or stage |Short description |Customizable in `.pipeline-config.yaml` |
|:----------|:------------------------------|:------------------|
|`start` |Sets up the pipeline environment. |No |
|`setup` |Sets up your build and test environment. |Yes  |
|`test` |Runs unit tests on configuration, IaC.  |Yes |
|`static-scan` |Runs static scan code on the IaC. |Yes |
|`compliance-checks` |Runs Code Risk Analyzer scans and other compliance checks on app repos.   |Yes |
|`build-artifact`  |Builds the artifacts corresponding to the configuration.  |Yes |
|`sign-artifact`  |Signs the built artifacts.   |Yes |
|`deploy`|Deploys the configuration, by using the built artifacts, to the dev environment. |Yes|
|`acceptance-test`|Runs acceptance and integration tests on the deployed configuration on the dev environment.|Yes|
|`release` |Adds the built artifacts to the inventory. |Yes|
|`finish` |Collects, creates, and uploads the logs files, artifacts, and evidence to the evidence locker.   |No|
{: caption="Table 1. Continuous integration for IaC stages and tasks" caption-side="top"}

For more information about how to customize stages by using the `.pipeline-config.yaml` file, see [Custom scripts](/docs/devsecops?topic=devsecops-custom-scripts) and [Pipeline parameters](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm#cd-ci-parameters) lists.

## Static code scan
{: #devsecops-iac-ci-pipeline-static-codescan}

The static code scan stage runs a number of static code analyzer tools on the specified IaC repositories. The repos that are provided by the [`pipelinectl save_repo`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#save_repo) command and the default app repo are scanned.

You can use any of the methods that are defined for [static code scan](/docs/devsecops?topic=devsecops-devsecops-ci-pipeline#devsecops-ci-pipeline-static-codescan) configurable for application relate continuous integration pipeline. 

The IaC continuous integration pipeline defines more tools that are enabled by using the `opt-in-*` parameters from Table 2 set to `1`.

| Name | Type | Description | Required or optional |
|--|--|--|--|
| `opt-in-terraform-fmt-validate`     | text   | Runs the `terraform fmt` and `terraform validate` commands in `static-scan` stage. | optional |
| `opt-in-tflint`   | text   |Runs the `tflint` command in `static-scan` stage. | optional |
| `tflint-config` | text | The config file to use for `tflint`. | optional |
| `tflint-args` | text | The command arguments that are passed to `tflint` during tool invocation. | optional |
{: caption="Table 2. IaC static scan tools parameters" caption-side="top"}


## Scans and checks in compliance checks
{: #devsecops-iac-ci-pipeline-compliancechecks}

[Compliance checks](/docs/devsecops?topic=devsecops-devsecops-ci-pipeline#devsecops-ci-pipeline-compliancechecks) defined for application related continuous integration pipeline are also performed for the IaC continuous integration pipeline. 

The IaC CI pipeline performs some additional checks that are enabled by using `opt-in-` features.

The IaC CI pipeline defines more tools that are enabled by using the `opt-in-` parameters set to`1`.

| Scan or check |  Description | Enablement |
|---------|------------|---------|
| `cra-tf` | Use the `ibmcloud cra terraform-validate` command from [IBM Cloud CRA tool](/docs/code-risk-analyzer-cli-plugin){: external} to analyze a Terraform plan for compliance | `opt-in-cra-tf-validate` set to `1`. |
| `tfsec`| Use the [TFsec](https://aquasecurity.github.io/tfsec){: external} tool to find potential misconfigurations and create compliance issues. | `opt-in-tfsec` set to `1` | 
| `checkov`| Use the [Checkov](https://www.checkov.io){: external} tool to find misconfigurations and create compliance issues. | `opt-in-checkov` set to `1` | 
{: caption="Table 3. IaC additional compliance scans and checks" caption-side="top"}

| Property | Default | Description | 
| -------- | ----- | ----------- | 
| `opt-in-cra-tf-validate` | | Flag to run compliance check by using the `ibmcloud cra terraform-validate` tool. |
| `cra-tf-policy-file` | | Path to policy profile file. For more information, see [Terraform command options](/docs/cli?topic=code-risk-analyzer-cli-plugin-cra-cli-plugin#terraform-options). |
| `cra-tf-ignore-goals` | | Comma-separated list of goals to ignore from the `ibmcloud cra terraform-validate` report. |
| `cra-tf-ignore-goals-file` | | Path to the JSON file that contains the list of goals to ignore from the `ibmcloud cra terraform-validate` report. |
| `opt-in-tfsec` | | Flag to run compliance checks by using `tfsec` tool. |
| `tfsec-args` | | `tfsec` command arguments. |
| `opt-in-checkov` | | Flag to run compliance checks by using the `checkov` tool. |
| `checkov-image` | `bridgecrew/checkov`| Image that performs the `checkov` compliance check. |
| `checkov-args` | | `checkov` command arguments. |
{: caption="Table 4. IaC compliance scans and checks configuration parameters" caption-side="top"}

These scripts are run on all the repos that the pipeline is aware of. To add repos to these scans, use the `pipelinectl` interface that is provided in your setup stage. For more information, see [`pipelinectl`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl).

For more information about the expected output from user script stages, see [Custom scripts](/docs/devsecops?topic=devsecops-custom-scripts).

## Build artifact
{: #devsecops-iac-ci-pipeline-build}

In the build artifact stage, you can build your own artifacts. For the IaC CI pipeline, the default build feature creates a Tar file that contains the Terraform configuration.

The default build feature can be configured by using specific parameters from Table 5.

| Property | Default | Description | Required |
| -------- | ----- | ----------- | ------ |
| `configuration-name` | The "humanish" part of the Git repository name. | The name of the IaC configuration. Used for the artifact file name and inventory entry that is built by the IaC CI pipeline. | |
| `build-ignore-file` | | Path to an ignore list file (used for `tar --exclude-from`) | |
{: caption="Table 5. Build artifact configuration parameters" caption-side="top"}

For more information about how to access parameters and secrets in custom script stages, see [Custom scripts](/docs/devsecops?topic=devsecops-custom-scripts#cd-devsecops-scripts-secrets).

## Artifact sign
{: #devsecops-iac-ci-pipeline-artifactsign}

The sign artifact stage provides a default behavior to use a GPG key to create a detached signature files for one or more artifacts.

| Property | Description |
| -------- | ----------- |
| `signing-key` | GPG private key value that is used for the ascii version of detached signatures of one or more artifacts | 
{: caption="Table 6. Build artifact GPG key" caption-side="top"}

To use a different sign process, customize this stage by using the `.pipeline-config.yaml` configuration in your project.

## Deploy to dev
{: #devsecops-iac-ci-pipeline-devdeploy}

The deploy stage deploys configuration artifact into a dev environment. You can provide your variables and credentials for this stage from [variables in the pipeline UI](/docs/devsecops?topic=devsecops-custom-scripts#cd-devsecops-scripts-secrets) and the [pipeline trigger webhook payload](/docs/devsecops?topic=devsecops-cd-devsecops-webhook-payloads).


## Release to inventory
{: #devsecops-ci-pipeline-inventoryrel}

Use the release to inventory user script stage to add artifacts to the inventory by using the `cocoa inventory add` CLI command. For more information about `cocoa inventory add`, see [cocoa inventory add](/docs/devsecops?topic=devsecops-cd-devsecops-cli#inventory-add).

You can use the `pipelinectl` interface to access your repos and artifacts by using the `list_repos`, `load_repo`, `list_artifacts`, and `load_artifact` commands. For more information, see [pipelinectl](/docs/devsecops?topic=devsecops-devsecops-pipelinectl).

## Collecting compliance data on build
{: #devsecops-ci-pipeline-collectdata}

When the pipeline runs successfully, you can collect information about the build.

Evidence is collected on all checks, scans, tests, and artifact signings and placed into the evidence locker. The pipeline log files are also saved to the locker, along with the pipeline data itself, containing the Tekton definitions. Compliance data on peer reviews is also collected in this step. The pipeline uses `pipelinectl` to search for repos with pull requests that were merged since the last build. The pipeline also checks PR review status, saves it as an artifact, and creates evidence based on the result.

The final script is an evaluator, which marks the pipeline status green or red, based on the evidence statuses. If there are any failures, the continuous integration run is marked as red.


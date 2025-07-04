---

copyright:
  years: 2021, 2025
lastupdated: "2025-07-04"

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
|----------|------------------------------|------------------|
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
{: caption="Continuous integration for IaC stages and tasks" caption-side="top"}

For more information about how to customize stages by using the `.pipeline-config.yaml` file, see [Custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize).
e) and [Pipeline parameters](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm#cd-ci-parameters) lists.

## Parameters to configure Terraform context and variables
{: #devsecops-iac-ci-terra-var}

For Terraform definitions that define infrastructure-as-code source, the context and variables that are related to Terraform and Terraform-related scan and checks can be defined by using the parameters that are described in Table 1.

| Property | Default | Description |
| -------- | ----- | ----------- |
| `tf-dir`   | `.` | Location or path in the source repository where `main.tf` is located. |
| `TF_VAR_<XXXX>` |  | Pipeline or trigger property (secured or unsecured) that provides the value for Terraform variable `<XXXX>` |
| `tfvars-repository` | Infrastructure source code Git repository. | Git repository that contains `tfavars` files. The repository must be declared in the toolchain.|
| `tfvars-branch` | `main` | Branch of the Git repository that contains the `tfvars` files. |
| `tfvars-files` | | Files that contain Terraform variables values. |
| `terraform-version` | `1.2.9` | Terraform CLI tool version to install if not present in the image used for the stages.You can also provide versions like 1.5.0-1. Here is the [list of Terraform versions].(https://releases.hashicorp.com/terraform/)  |
{: caption="Parameters to configure Terraform context and variables" caption-side="top"}

The same parameters apply for the scripts that are used in an IaC CD deployment process. Because the CD process can process multiple inventory entries, you can scope a parameter for an inventory entry. To specify Terraform context and variables for a scope (inventory entry), prefix the property with the inventory entry name, for example: `<inventory_entry>_`. This prefix applies for the environment entries `tf-dir`, `TF_VAR_<XXXX>`, `tfvars-repository`, `tfvars-branch`, and `tfvars-files`.

Example:

```bash
hello-iac-sample_TF_VAR_resource_group : Default
```

## Scans and checks in Static code scan
{: #devsecops-iac-ci-pipeline-static-codescan}

The static code scan stage runs a number of static code analyzer tools on the specified IaC repositories. The repos that are provided by the [`pipelinectl save_repo`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#save_repo) command and the default app repo are scanned.

You can use any of the methods that are defined for [static code scan](/docs/devsecops?topic=devsecops-cd-devsecops-ci-pipeline#devsecops-ci-pipeline-static-codescan) configurable for application relate continuous integration pipeline.

The IaC continuous integration pipeline defines more tools that are enabled by using the `opt-in-*` parameters from Table 2 set to `1`.

| Scan or check |  Description | Enablement |
|---------|------------|---------|
| tflint |Runs `tflint $tflint_args --format=json` from [tflint](https://github.com/terraform-linters/tflint) to warn about deprecated syntax, unused declarations and enforce best practices, naming conventions. |`opt-in-tflint` set to 1 |
| fmt | Runs `terraform fmt -check` from [fmt](https://developer.hashicorp.com/terraform/cli/commands/fmt) to rewrite Terraform configuration files to a canonical format and style. | `opt-in-terraform-fmtvalidate` set to 1 |
| terraform-validate |Runs `ibmcloud cra terraform-validate` from [terraform-validate](https://cloud.ibm.com/docs/code-risk-analyzer-cli-plugin?topic=code-risk-analyzer-cli-plugin-cra-cli-plugin#terraform-command) to warn about specified Terraform plan file for compliance with rules that are specified in IBM Cloud® Security and Compliance Center | `opt-in-terraform-fmtvalidate` set to 1  |

| Name | Type | Default | Description | Required or optional |
|--|--|--|--|--|
| `opt-in-terraform-fmt-validate` | text  | | Runs the `terraform fmt` and `terraform validate` commands in `static-scan` stage. | optional |
| `opt-in-tflint`   | text | |Runs the `tflint` command in `static-scan` stage. | optional |
| `tflint-version` | text | `v0.53.0` |Indicate the `tflint` version to install if not provided in the image used for `static-scan` stage execution. | optional |
| `tflint-config` | text | | The config file to use for `tflint`. | optional |
| `tflint-args` | text | | The command arguments that are passed to `tflint` during tool invocation. | optional |
{: caption="IaC static scan tools parameters" caption-side="top"}


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
{: caption="IaC additional compliance scans and checks" caption-side="top"}

| Property | Default | Description |
| -------- | ----- | ----------- |
| `opt-in-cra-tf-validate` | | The flag to run compliance checks by using the `ibmcloud cra terraform-validate` tool. |
| `cra-tf-policy-file` | | The path to policy profile file. For more information, see [Terraform command options](/docs/cli?topic=code-risk-analyzer-cli-plugin-cra-cli-plugin#terraform-options). |
| `cra-tf-scc-instance-name`| Default to the [Security and Compliance Center](/docs/ContinuousDelivery?topic=ContinuousDelivery-scc) tool integration configured in the toolchain | The [Security and Compliance Center](/docs/ContinuousDelivery?topic=ContinuousDelivery-scc) tool integration name to be used to fetch profile and attachment to configure `cra terraform validate` command. |
| `cra-tf-ignore-rules` | | The comma-separated list of rules to ignore from the `ibmcloud cra terraform-validate` report. |
| `cra-tf-ignore-rules-file` | | The path to the JSON file that contains the list of rules to ignore from the `ibmcloud cra terraform-validate` report. For more information on the file format, see [Format for cra-tf-ignore-rules-file](#devsecops-iac-ci-pipeline-ignore-rules).  |
| `opt-in-tfsec` | | The flag to run compliance checks by using `tfsec` tool. |
| `tfsec-version` | `v1.21.0`` | The `tfsec` version to use. |
| `tfsec-args` | | The `tfsec` command arguments. |
| `opt-in-checkov` | | The flag to run compliance checks by using the `checkov` tool. |
| `checkov-version` | `` meaning the latest version | `checkov` version to install if not available in the environment. |
| `checkov-args` | | The `checkov` command arguments. |
{: caption="IaC compliance scans and checks configuration parameters" caption-side="top"}

These scripts are run on all the repos that the pipeline is aware of. To add repos to these scans, use the `pipelinectl` interface that is provided in your setup stage. For more information, see [`pipelinectl`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl).

For more information about the expected output from user script stages, see [Custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize).

### Format for `cra-tf-ignore-rules-file`
{: #devsecops-iac-ci-pipeline-ignore-rules}

The expected format for the file that is defined by the `cra-tf-ignore-rules-file format`. The format is similar to the format (without the `scc_parameters` field) that is in [Example SCC V2 classic profile file for the `terraform-validate` command](/docs/code-risk-analyzer-cli-plugin?topic=code-risk-analyzer-cli-plugin-cra-cli-plugin#terraform-example-v2-classicprofile).

Sample content for `cra-tf-ignore-rules-file` file:

```bash
{
    "scc_rules": [
        {
            "scc_rule_id": "rule-8cbd597c-7471-42bd-9c88-36b2696456e9"
        },
        {
            "scc_rule_id": "rule-c97259ee-336d-4c5f-b436-1868107a9558"
        }
    ]
}
```

## Build artifact
{: #devsecops-iac-ci-pipeline-build}

In the build artifact stage, you can build your own artifacts. For the IaC CI pipeline, the default build feature creates a Tar file that contains the Terraform configuration.

The default build feature can be configured by using specific parameters from Table 5.

| Property | Default | Description |
| -------- | ----- | ----------- |
| `configuration-name` | The "humanish" part of the Git repository name. | The name of the IaC configuration. Used for the artifact file name and inventory entry that is built by the IaC CI pipeline. |
| `build-ignore-file` | | Path to an ignore list file (used for `tar --exclude-from`) |
{: caption="Build artifact configuration parameters" caption-side="top"}

For more information about how to access parameters and secrets in custom script stages, see [Custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize).

## Artifact sign
{: #devsecops-iac-ci-pipeline-artifactsign}

The sign artifact stage provides a default behavior to use a GPG key to create a detached signature file for one or more artifacts.

| Property | Description |
| -------- | ----------- |
| `signing-key` | GPG private key value that is used for the ascii version of detached signatures of one or more artifacts |
{: caption="Build artifact GPG key" caption-side="top"}

To use a different sign process, customize this stage by using the `.pipeline-config.yaml` configuration in your project.

## Deploy to development
{: #devsecops-iac-ci-pipeline-devdeploy}

The deploy stage deploys configuration artifact into a development environment. You can provide your variables and credentials for this stage from [variables in the pipeline UI](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize) and the [pipeline trigger webhook payload](/docs/devsecops?topic=devsecops-cd-devsecops-webhook-payloads).

The scripts that are provided as part of the common base image can help perform a deployment by using schematics or Terraform CLI.
The parameters to configure the scripts for the deployment action are described in Table 8 and Table 9.

###  Configuration parameters to use Schematics as deployment tool
{: #devsecops-iac-ci-pipeline-schema-dep}

| Property | Default | Description |
| -------- | ----- | ----------- |
| `schematics-ibmcloud-api-key` | | Overwrites the `ibmcloud-api-key` that is used for the schematics related actions (schematics workspace retrieval/creation, plan & apply). |
| `schematics-workspace-name` | `<schematics-workspace-prefix><toolchain name>-<pipeline id>` | Workspace to use or to create if it does not exist. If the workspace exists, it must be a workspace created without a link to a Git repository. For more information, see [Schematics Workspace creation](/docs/schematics?topic=schematics-schematics-cli-reference#schematics-workspace-new). This restriction is because the script uploads the IaC configuration artifact as a `tar` file by using [Schematics Workspace upload](/docs/schematics?topic=schematics-schematics-cli-reference#schematics-workspace-upload).{: attention} |
| `schematics-workspace-prefix` | | Prefix used for workspace creation when no workspace is specified for the deployment action. |
| `schematics-workspace-resource-group` | Defaults to the resource group of the toolchain. | Resource group to be used for the schematics workspace creation. |
| `schematics-workspace-region` | Defaults to the region of the toolchain. | Region to be used for the schematics workspace creation. |
| `schematics-workspace-netrc` | Computed from known repositories. | Value for the `netrc` configuration of the schematics workspace to be created. For more information, see [Supporting to download modules from private remote host](/docs/schematics?topic=schematics-download-modules-pvt-git). |
| `schematics-workspace-terraform-version` | Defaults to the schematics terraform version retrieved using `ibmcloud schematics version --output JSON` | Terraform version used for the schematics workspace to be created. See [Overview of Schematics images and packaged Terraform providers](/docs/schematics?topic=schematics-version-constraints#schematics-image-ov). |
{: caption="Configuration parameters to use Schematics as deployment tool" caption-side="top"}

To configure the scripts in the IaC CD deployment process, define the parameter that is scoped for a given inventory entry. To specify `Schematics as deployment tool` related environment properties for a given scope (inventory entry), prefix the property with the inventory entry name, for example: `<inventory_entry>_`. This prefix applies for the all the schematics-related environment entries (except `schematics-ibmcloud-api-key`).
{: note}

Example:

```bash
hello-iac-sample_schematics-workspace-name : workspace-for-deployment-of-hello-iac-sample
```

#### Configuration to use Terraform CLI as deployment tool
{: #devsecops-iac-ci-pipeline-terra-cli}

| Property | Description |
| -------- | ----------- |
| `tf-backend-s3-bucket` | Bucket name that stores the state. |
| `tf-backend-s3-key` | Name to use to persist the state. |
| `tf-backend-s3-region` | Region of the Cloud Object Storage instance. |
| `tf-backend-s3-endpoint` | Cloud Object Storage endpoint. |
| `tf-backend-s3-access_key` | HMAC `access_key` subsection of the credentials. |
| `tf-backend-s3-secret_key` | HMAC `secret_key` subsection of the credentials. |
{: caption="Configuration to use Terraform CLI as deployment tool" caption-side="top"}

Note the following:

- For more information on how to use the Cloud Object Storage endpoint or bucket to store Terraform state, see: [Store Terraform states in Cloud Object Storage](https://www.ibm.com/products){: external}.
- To configure the scripts in the IaC CD deployment process, define the parameter that is scoped for an inventory entry. To specify `Terraform CLI as deployment tool`-related environment properties for a scope (inventory entry), prefix the property with the inventory entry name, for example: `<inventory_entry>_`. This prefix applies for the all the schematics-related environment entries.

 Example:

 ```bash
 hello-iac-sample_tf-backend-s3-bucket : bucket-to-store-tfstate-of-hello-iac-sample
 ```

## Release to inventory
{: #devsecops-iac-ci-pipeline-inventoryrel}

Use the release to inventory user script stage to add artifacts to the inventory by using the `cocoa inventory add` CLI command. For more information about `cocoa inventory add`, see [cocoa inventory add](/docs/devsecops?topic=devsecops-cd-devsecops-cli#inventory-add).

You can use the `pipelinectl` interface to access your repos and artifacts by using the `list_repos`, `load_repo`, `list_artifacts`, and `load_artifact` commands. For more information, see [pipelinectl](/docs/devsecops?topic=devsecops-devsecops-pipelinectl).

## Collecting compliance data on build
{: #devsecops-iac-ci-pipeline-collectdata}

When the pipeline runs successfully, you can collect information about the build.

Evidence is collected on all checks, scans, tests, and artifact signings and is placed into the evidence locker. The pipeline log files are also saved to the locker, along with the pipeline data itself, containing the Tekton definitions. Compliance data on peer reviews is also collected in this step. The pipeline uses `pipelinectl` to search for repos with pull requests that were merged since the last build. The pipeline also checks PR review status, saves it as an artifact, and creates evidence based on the result.

The final script is an evaluator, which marks the pipeline status green or red, based on the evidence statuses. If there are any failures, the continuous integration run is marked as red.

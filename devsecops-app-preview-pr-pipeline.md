---

copyright:
  years: 2021, 2025
lastupdated: "2025-05-05"

keywords: DevSecOps, app-preview pr pipeline

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}
# App Preview Pull Request (PR) Pipeline

{: #cd-devsecops-app-preview-pr-pipeline}

The App Preview pull request (PR) pipeline extends the normal PR pipeline by adding additional stages to containerize, scan, deploy, and preview the application before merging changes to the main branch.

## Overview
{: #devsecops-app-previe-pr-pipeline-overview}

This pipeline is designed to enhance your development workflow by enabling early feedback through preview environments. It adds custom stages to the base PR pipeline, such as deployment and acceptance testing, which are useful for functional validation before code is merged.

The App-Preview pipeline is used to:

- Build and containerize the application
- Perform artifact scanning
- Deploy the application to a preview environment
- Run acceptance tests to validate functionality

## App - Preview PR Pipeline Stages 
{: #devsecops-app-preview-pr-pipeline-stages}

| Stage Name                | Description |
|---------------------------|-------------|
| code-pr-start           | Initial stage that sets up the pipeline run, validates the pull request event, and prepares metadata for later steps. |
| code-setup             |  The placeholder for a user-defined setup custom script where the user can complete their pipeline setup. |
| code-detect-secrets     |  Scans the codebase for hardcoded secrets or sensitive data using secret detection tools |
| code-unit-tests         |  Runs unit tests to validate the functionality of application components. This ensures that new code doesn't break existing logic. |
| code-static-scan        |  Performs static code analysis to find bugs and vulnerabilities. |
| code-compliance-checks  |  Checks for policy compliance including licenses and security rules. |
| build-artifact         | Builds and packages the application as a deployable artifact. |
| build-scan-artifact    |  Scans the built artifact for security vulnerabilities. |
| deploy-app-preview      |  Deploys the app to a temporary preview environment. |
| deploy-acceptance-tests |  Runs acceptance tests against the deployed preview version. |
| app-preview-pr-finish   |  Finalizes the pipeline, updates PR status, logs results, and performs cleanup. |

## Comparison: PR Pipeline vs App Preview PR Pipeline
{: #comparison-pr-vs-app-preview-pr}

The App Preview PR Pipeline is an extension of the regular PR pipeline, it builds by adding stages for building, scanning, deploying, and previewing the application. 

The App Preview PR pipeline helps catch issues early and ensures that changes behave as expected in a simulated production-like setup.


| PR Pipeline Stages       | App Preview PR Pipeline Stages     |
|--------------------------|------------------------------------|
| `code-pr-start`          | `code-pr-start`                    |
| `code-setup`             | `code-setup`                       |
| `code-detect-secrets`    | `code-detect-secrets`              |
| `code-unit-tests`        | `code-unit-tests`                  |
| `code-compliance-checks` | `code-static-scan`                 |
| `code-pr-finish`         | `code-compliance-checks`           |
|                          | `build-artifact`                   |
|                          | `build-scan-artifact`              |
|                          | `deploy-app-preview`               |
|                          | `deploy-acceptance-tests`          |
|                          | `app-preview-pr-finish`            |

These additional stages allow:
- Static code scanning (`code-static-scan`)
- Building deployable artifacts (`build-artifact`)
- Scanning artifacts for vulnerabilities (`build-scan-artifact`)
- Deploying to a preview environment (`deploy-app-preview`)
- Running acceptance tests (`deploy-acceptance-tests`)


## Customising pipeline stages and tasks
{: #devsecops-app-preview-pr-pipeline-stages-tasks}

The table below lists the tasks run in a App-Preview PR Pipeline. In addition the table also provides an overview of each of these stages:

- **Task or Stage**: This refers to the name of the stage as defined within the  `.pipeline-config.yaml`  configuration file.
    
- **Short description**: This provides a concise explanation of the actions performed during the execution of the stage.
    
- **Customisation permissible**: This indicates whether users have the flexibility to modify or replace the default behavior of the stage by inserting a custom script in the  `.pipeline-config.yaml`  file.
    
- **Default Reference Implementation**: This indicates whether the DevSecOps pipelines come with a pre-defined or default implementation for the stage. Notably, for certain stages like  `unit-tests`  or  `setup`, the DevSecOps pipeline doesn’t offer any out-of-the-box implementation. Instead, users are required to provide custom scripts or code tailored to their application’s requirements.
    
- **Evidence Collection**: This indicates whether the stage performs the collection of standard evidence. When DevSecOps  Pipeline provide a reference implementation for a stage, evidence collection is performed out-of-the-box. However, if  User  choose to modify or replace these predefined stages, they must ensure that their custom implementations include appropriate evidence collection. The same responsibility falls on users for stages where the DevSecOps pipeline doesn’t provide an out-of-the-box implementation, necessitating them to perform evidence collection. The column indicates the entity (**User/Pipeline**) responsible for carrying out the evidence collection.
    
- **Skip permissible (applicable to version >= v10)**: This indicates whether users can opt out of running this stage by setting the skip property to true in the  `.pipeline-config.yaml`. However, caution is advised when using this feature, especially for stages designed to collect evidence. Skipping such stages might lead to missing essential evidences for the build.

| Task or Stage      | Short Description                                                                 | Customisation Permissible in `.pipeline-config.yaml` | Default Reference Implementation | Evidence Collection | Skip Permissible |
|--------------------|------------------------------------------------------------------------------------|-------------------------------------------------------|----------------------------------|---------------------|------------------|
| `start`            | Set up the pipeline environment.                                                   | No                                                    | Yes                              | Pipeline            | No               |
| `setup`            | Set up your build and test environment.                                            | Yes                                                   | No                               | NA                  | No               |
| `detect-secrets`   | Run detect secrets scan on the application code.                                   | Yes                                                   | Yes                              | Pipeline            | No               |
| `test`             | Run unit tests and application tests on application code.                          | Yes                                                   | No                               | **User**            | Yes              |
| `static-scan`      | Run static scan code on the application code.                                      | Yes                                                   | Yes                              | Pipeline            | Yes              |
| `compliance-checks`| Run Code Risk Analyzer scans and other compliance checks on app repos.             | Yes                                                   | Yes                              | Pipeline            | Yes              |
| `build-artifact`     | Build the artifacts.                                                               | Yes                                                   | No                               | NA                  | No               |
| `build-scan-artifact`    | Scans the built artifact for security vulnerabilities.                                                          | Yes                                                   | Yes                              | Pipeline            | Yes               |
| `deploy-app-preview`           | Deploys the app to a temporary preview environment.                                 | Yes                                                   | No                               | NA                  | No               |
| `deploy-acceptance-tests`     | Runs acceptance tests against the deployed preview version.                                               | Yes                                                   | No                              | **User**            | Yes              |
| `app-preview-pr-finish`  | Finalizes the pipeline, updates PR status and logs results.  | Yes                                                   | Yes                               | NA           | Yes              |
{: caption="Table 1. App-Preview Pipeline stages and tasks" caption-side="top"}

For more information about how to customize stages by using the `.pipeline-config.yaml` file, see [Custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize) and [Pipeline parameters](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm#cd-ci-parameters) lists.


## Collecting evidences in the pipeline stages
{: #devsecops-app-preview-pr-evidences}

| Task or Stage       | Evidence Type                                                                                                 |
|---------------------|---------------------------------------------------------------------------------------------------------------|
| `start`             | NA                                                                                                            |
| `setup`             | NA                                                                                                            |
| `detect-secrets`    | `com.ibm.detect_secrets`                                                                                      |
| `test`              | `com.ibm.unit_tests`                                                                                          |
| `static-scan`       | `com.ibm.static_scan`                                                                                         |
| `compliance-checks` | `com.ibm.code_bom_check`, `com.ibm.code_cis_check`, `com.ibm.code_vulnerability_scan`, `com.ibm.branch_protection` |
| `build-artifact`      | NA                                                                                                            |
| `build-scan-artifact`     | `com.ibm.cloud.image_vulnerability_scan`                                                                                 |
| `deploy-app-preview`            | NA                                                                                                            |
| `deploy-acceptance-tests`      | `com.ibm.acceptance_tests`                                                                                        |
| `app-preview-pr-finish`   | `com.ibm.pipeline_logs`,`com.ibm.pipeline_run_data`
{: caption="Table 2. App-Preview Pipeline stages and associated evidences" caption-side="top"}

For more information about how to collect evidences within the customizable user stages by using the `collect-evidence` script, see [collect-evidence script](/docs/devsecops?topic=devsecops-devsecops-collect-evidence).

 
## Prerequisites 
{: #devsecops-app-preview-pr-pipeline-prerequisites}

Ensure that:

- A CI toolchain is created using the “Create Toolchain” option.

- The CI pipeline has a working Git PR trigger.

- Required environment variables and permissions are correctly configured for image registry and deployment.

## Setting up App-Preview PR Pipeline

### 1. Setting up trigger

To configure the pipeline trigger:

- You can modify the existing PR pipeline trigger.
- Go to the Triggers section of your CI pipeline.

- Update the Git PR Trigger:

- Set `EventListener` to `app-preview-pr-listener`

- Save the changes

Alternatively, create a new trigger:
1. Go to the PR pipeline, click Add and click on Git repository.

2. Select `EventListener` as `app-preview-pr-listener`.

3. Complete the remaining fields based on your existing Git PR trigger as a reference.

4.  Disable the old Git PR trigger and enable the new `app-preview-trigger`.

Now, when a new pull request is created, the `app-preview` trigger will be activated.

### 2. Required Environment Properties

---

In addition to the required environment properties for the PR pipeline, the following properties have been correctly configured in your pipeline setup to ensure the App Preview PR Pipeline runs successfully.

| Name                 | Type | Description                                                  |
|----------------------|------|--------------------------------------------------------------|
| `app-url`            | text | The URL of your preview application.                         |
| `registry-namespace` | text | The Container Registry namespace for the image.              |
| `registry-region`    | text | The IBM Cloud region for the image registry.                 |

After adding these variables , trigger the PR pipeline.

## Detect Secrets Scan
{: #devsecops-app-preview-pr-pipeline-detect-secrets}

The [IBM Detect Secrets](https://github.com/IBM/detect-secrets) tool identifies where secrets are visible in app code. More information on setting up your repo for the scan is available [here](https://cloud.ibm.com/docs/devsecops?topic=devsecops-cd-devsecops-detect-secrets-scans)


## Static Code Scan
{: #devsecops-app-preview-pr-pipeline-static-codescan}

The static code scan stage runs a number of static code analyzer tools on the specified app repositories.  
The repos provided by the [pipelinectl save_repo](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#save_repo) command and the default app repo are scanned.

You can use any of the following methods to add static code to your pipeline:

* Provide an already running SonarQube instance name, URL, and credentials by adding the SonarQube tool to your toolchain. The static-scan task runs a scan on the specified repos.

* If you don't have your own SonarQube instance, the pipeline creates a SonarQube instance during the pipeline run. You can access this instance after the static-scan stage successfully runs.

* By using the `opt-in-gosec` parameter to run the gosec scan for golang security checks.

* Add your own static scan code to the static-scan custom stage in your `.pipeline-config.yaml` file for a custom implementation.



## Adding SonarQube Scan to Your Pipelines
{: #devsecops-app-preview-pr-pipeline-sonarqube-info}

For more information about integrating SonarQube with the continuous integration pipeline, see [Configuring SonarQube](/docs/devsecops?topic=devsecops-sonarqube).



## Adding GoSec Scan Integration to Your Pipelines

{: #devsecops-app-preview-pr-pipeline-gosec-add}

Use [gosec](https://github.com/securego/gosec){: external} to inspect Golang source code in your scanned repositories.

To enable the GoSec scan, provide the following parameter:

| Name          | Type  | Description              | Required or Optional |
|:--------------|:------|:--------------------------|:---------------------|
| `opt-in-gosec` | text  | Option to enable gosec scan | Optional              |
{: caption="Table 3. GoSec scan parameters" caption-side="top"}

For more information, see [Configuring GoSec](/docs/devsecops?topic=devsecops-devsecops-gosec).

### Using Other Static Scanners
{: #devsecops-app-preview-pr-pipeline-other-static-scans}

If you want to use your own static scan implementation, you can modify your `.pipeline-config.yaml` file and add your [custom script](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize) to the `static-scan` stage.



## Scans and Checks in Compliance Checks
{: #devsecops-app-preview-pr-pipeline-compliancechecks}

| Scan or Check                      | Description |
|:-----------------------------------|:------------|
| Code Risk Analyzer vulnerability scan | Finds vulnerabilities for app package dependencies, container base images, and OS packages using Code Risk Analyzer. |
| Code Risk Analyzer CIS check   | Runs [configuration checks](/docs/cli?topic=cli-cra-cli-plugin#deployment-command) on Kubernetes deployment manifests using Code Risk Analyzer. |
| Code Risk Analyzer BOM check   | The BOM for a specified repo that captures the pedigree of all of the dependencies. This BOM is collected at different granularities. For example, the BOM captures the list of base images that are used in the build, the list of packages from the base images, and the list of app packages that are installed over the base image. The BOM acts as a ground truth for the analytic results and can potentially be used to enforce policy gates. Uses the Code Risk Analyzer tool. |
| Repository compliance checking | Checks that branch protection settings are correct. For example, the master/main branch should always restrict the force push. For more information, see [Configuring your Git Repos and Issue Tracking repository.](/docs/devsecops?topic=devsecops-cd-devsecops-config-github) |
| Mend Unified Agent vulnerability scan | The [Mend Unified Agent scanning tool](https://docs.mend.io/bundle/unified_agent/page/overview_of_the_unified_agent.html){:external} scans app repos' open source components for vulnerable libraries and source files. For more information, see [Configuring Mend Unified Agent scans.](/docs/devsecops?topic=devsecops-cd-devsecops-mend-scans) |
{: caption="Table 4. Compliance scans and checks" caption-side="top"}

These scans are run on all application repositories that the pipeline is aware of.  
To add repositories to these scans, use the [`pipelinectl`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl) interface during the setup stage.

For more information about the expected output from user script stages, see [Custom Scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize).



## Build Stage
{: #devsecops-app-preview-pr-pipeline-build}

In the Build stage, you can build your own artifacts.  
While the pipeline provides default features for Docker image artifacts, you can build any type of artifact.

Use environment variables configured in the Pipeline UI to provide credentials, secrets, and parameters for your build.

These variables are accessible in this and all custom stages.  
For more details on how to access parameters and secrets in custom scripts, see [Custom Scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize).

## Artifact Scan
{: #devsecops-app-preview-pr-pipeline-artifactscan}

In the App-Preview PR pipeline:

- Only the artifact scan happens.
- The scan checks for vulnerabilities in the built Docker images using the Container Registry Vulnerability Advisor.

You can customize the scan behavior by modifying the `.pipeline-config.yaml` file if needed.

## Deploy
{: #devsecops-app-preview-pr-pipeline-deploy}

The Deploy stage deploys built artifacts into a environment.

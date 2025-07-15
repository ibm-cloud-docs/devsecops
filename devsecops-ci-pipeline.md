---

copyright:
  years: 2021, 2025
lastupdated: "2025-07-15"

keywords: DevSecOps, scan, inventory, compliance, dynamic scan, zap,

subcollection: devsecops, ci pipeline

---

{{site.data.keyword.attribute-definition-list}}

# Continuous integration pipeline
{: #cd-devsecops-ci-pipeline}

The continuous integration pipeline builds the deployable artifacts from the applications' repositories.
{: shortdesc}

Before it builds an artifact, the pipeline checks that the code is scanned and tested, in the same way that pull requests are processed. Built artifacts are also scanned for vulnerabilities and signed in the pipeline before they are marked ready for release and deployment in the [inventory](/docs/devsecops?topic=devsecops-cd-devsecops-inventory). Unlike the pull request pipeline, the Continuous Integration pipeline collects evidence and result artifacts on each stage of the build, such as testing, scanning, and signing. This data correlates to the built artifacts and can be tracked through the deployment process and change management.

## Stages and tasks
{: #devsecops-ci-pipeline-stages}


The table below lists the tasks run in a CI Pipeline. In addition the table also provides an overview of each of these stages:

- **Task or Stage**: This refers to the name of the stage as defined within the `.pipeline-config.yaml` configuration file.

- **Short description**: This provides a concise explanation of the actions performed during the execution of the stage.

- **Customisation permissible**: This indicates whether users have the flexibility to modify or replace the default behavior of the stage by inserting a custom script in the `.pipeline-config.yaml` file.

- **Default Reference Implementation**: This indicates whether the DevSecOps pipelines come with a pre-defined or default implementation for the stage. Notably, for certain stages like `unit-tests` or `setup`, the DevSecOps pipeline doesn't offer any out-of-the-box implementation. Instead, users are required to provide custom scripts or code tailored to their application's requirements.

- **Evidence Collection**: This indicates whether the stage performs the collection of standard evidence. When DevSecOps **Pipeline** provide a reference implementation for a stage, evidence collection is performed out-of-the-box. However, if **User** choose to modify or replace these predefined stages, they must ensure that their custom implementations include appropriate evidence collection. The same responsibility falls on users for stages where the DevSecOps pipeline doesn't provide an out-of-the-box implementation, necessitating them to perform evidence collection. The column indicates the entity (**User/Pipeline**) responsible for carrying out the evidence collection.

- **Skip permissible (applicable to version >= v10)**: This indicates whether users can opt out of running this stage by setting the skip property to true in the `.pipeline-config.yaml`. However, caution is advised when using this feature, especially for stages designed to collect evidence. Skipping such stages might lead to missing essential evidences for the build.

|Task or stage |Short description	|Customisation permissible in `.pipeline-config.yaml` | Default Reference Implementation |Evidence Collection |Skip permissible |
|:----------|:------------------------------|:------------------|:------------------|:------------------|:------------------|
|`start` 		|Set up the pipeline environment. 		|No		| Yes | Pipeline | No |
|`setup`		|Set up your build and test environment.		|Yes			| No | NA | No |
|`detect-secrets`		|Run detect secrets scan on the application code.		|Yes		| Yes | Pipeline | No|
|`test`		|Run unit tests and application tests on application code. 		|Yes		| No | **User** | Yes |
|`static-scan`		|Run static scan code on the application code.		|Yes		|  Yes | Pipeline | Yes |
|`compliance-checks` 		|Run Code Risk Analyzer scans and other compliance checks on app repos.   	|Yes			|  Yes | Pipeline | Yes |
|`containerize` 		|Build the artifacts. 		|Yes			| No | NA | No |
|`sign-artifact` 		|Sign the built artifacts.   	|Yes			|  Yes | Pipeline | No |
|`deploy`		|Deploy the built artifacts to the dev environment. 		|Yes		|  No | NA | No |
|`dynamic-scan` 		|Run dynamic scan on the application. 		|Yes			|  Yes | Pipeline | Yes |
|`acceptance-test`		|Run acceptance and integration tests on the deployed built artifacts on the dev environment.		|Yes		|  No | **User** | Yes |
|`scan-artifact` 		|Scan the built artifacts.   	|Yes			|  Yes | Pipeline | Yes |
|`release` 		|Add the built artifacts to the inventory. 		|Yes			|  No |  NA | Yes |
|`finish` 		|Collect, create, and upload the logs files, artifacts, and evidence to the evidence locker.   	|Yes			|  Yes | NA | Yes |
{: caption="Continuous integration stages and tasks" caption-side="top"}

For more information about how to customize stages by using the `.pipeline-config.yaml` file, see [Custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize) and [Pipeline parameters](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm#cd-ci-parameters) lists.

## Stages and evidences
{: #devsecops-ci-pipeline-evidences}

The table below provides a relationship between various types of evidence and the specific stages within the pipeline where their collection takes place.

|Task or stage |Evidence Type |
|:----------|:------------------------------|
|`start` 		| NA |
|`setup`		| NA |
|`detect-secrets`		| `com.ibm.detect_secrets` |
|`test`		|  `com.ibm.unit_tests`|
|`static-scan`		|`com.ibm.static_scan` |
|`compliance-checks` 		| `com.ibm.code_bom_check`, `com.ibm.code_cis_check`, `com.ibm.code_vulnerability_scan`, `com.ibm.branch_protection` |
|`containerize` 		| NA |
|`sign-artifact` 		| `com.ibm.cloud.image_signing`|
|`deploy`		| NA |
|`dynamic-scan` 		| `com.ibm.dynamic_scan` |
|`acceptance-test`		| `com.ibm.acceptance_tests` |
|`scan-artifact` 		| `com.ibm.cloud.image_vulnerability_scan`|
|`release` 		| NA |
|`finish` 		| `com.ibm.pipeline_logs`, `com.ibm.pipeline_run_data` |
{: caption="Continuous integration stages and associated evidences" caption-side="top"}

For more information about how to collect evidences within the customizable user stages by using the `collect-evidence` script, see [collect-evidence script](/docs/devsecops?topic=devsecops-devsecops-collect-evidence).

## Detect Secrets scan
{: #devsecops-ci-pipeline-detect-secrets}

The [IBM Detect Secrets](https://github.com/IBM/detect-secrets) tool identifies where secrets are visible in app code. More information on setting up your repo for the scan is available [here](https://cloud.ibm.com/docs/devsecops?topic=devsecops-cd-devsecops-detect-secrets-scans)

## Static code scan
{: #devsecops-ci-pipeline-static-codescan}

The static code scan stage runs a number of static code analyzer tools on the specified app repositories. The repos that are provided by the [`pipelinectl save_repo`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#save_repo) command and the default app repo are scanned.

You can use any of the following methods to add static code to your pipeline:

* Provide an already running SonarQube instance name, URL, and credentials by adding the SonarQube tool to your toolchain. The static-scan task runs a scan on the specified repos.

* If you don't have your own SonarQube instance, the pipeline creates a SonarQube instance during the pipeline run. You can access this instance after the static-scan stage successfully runs.

* By using the `opt-in-gosec` parameter to run the gosec scan for golang security checks.

* Add your own static scan code to the static-scan custom stage in your `.pipeline-config.yaml` file for a custom implementation.

### Adding SonarQube scan to your pipelines
{: #devsecops-ci-pipeline-sonarqube-info}

For more information about integrating SonarQube with the continuous integration pipeline, see [Configuring SonarQube](/docs/devsecops?topic=devsecops-sonarqube).

### Adding gosec scan integration to your pipelines
{: #devsecops-ci-pipeline-gosec-add}

Use [gosec](https://github.com/securego/gosec){: external} to inspect golang source code in your scanned repositories.

To enable gosec scan, provide the following parameter, and set the value to `1`.

| Name | Type | Description | Required or optional |
|--|--|--|--|
| `opt-in-gosec`     | text   | option to enable gosec scan | optional |
{: caption="gosec scan parameters" caption-side="top"}

More information about setting up the gosec scan in the continous integration pipeline, see [Configuring GoSec](/docs/devsecops?topic=devsecops-devsecops-gosec)

### Using other static scanners
{: #devsecops-ci-pipeline-other-static-scans}

If you want to use your own static scan implementation instead, you can modify your `.pipeline-config.yaml` file and add your own [Custom Script](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize) to the `static-scan` stage.

## Scans and checks in compliance checks
{: #devsecops-ci-pipeline-compliancechecks}

| Scan or check |  Description |
|---------|------------|
| Code Risk Analyzer vulnerability scan | Finds vulnerabilities for all of the app package dependencies, container base images, and operating system packages. Uses the Code Risk Analyzer tool. |
| Code Risk Analyzer CIS check |  Runs [configuration checks](/docs/cli?topic=cli-cra-cli-plugin#deployment-command) on Kubernetes deployment manifests. Uses the Code Risk Analyzer tool. |
| Code Risk Analyzer Bill of Material (BOM) check | The BOM for a specified repo that captures the pedigree of all of the dependencies. This BOM is collected at different granularities. For example, the BOM captures the list of base images that are used in the build, the list of packages from the base images, and the list of app packages that are installed over the base image. The BOM acts as a ground truth for the analytic results and can potentially be used to enforce policy gates. Uses the Code Risk Analyzer tool. |
| Repository compliance checking | Checks that branch protection settings are correct. For example, the master/main branch should always restrict the force push. For more information, see [Configuring your {{site.data.keyword.gitrepos}} repository](/docs/devsecops?topic=devsecops-cd-devsecops-config-github).|

{: caption="Compliance scans and checks" caption-side="top"}

These scripts are run on all of the app repos that the pipeline is aware of. To add repos to these scans, use the [`pipelinectl`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl) interface that is provided in your setup stage.

For more information about the expected output from user script stages, see [Custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize).

**Note**: CI pipelines are not triggered on tags because they do not support features such as `branch protection checks` or `peer review check`. If you choose to run a CI pipeline on tags, ensure to disable the `peer-review-compliance` and `branch-protection-check` properties by setting it to `0`. However, be aware that in this case, evidence collection will not occur for these. 

## Build
{: #devsecops-ci-pipeline-build}

In the Build stage, you can build your own artifacts. Although the pipeline provides some default features for Docker image type artifacts, you can build any type of artifact in this stage.

Use the environment variables from the Pipeline UI to provide credentials, secrets, and parameters for your build. You can access these environment variables in this stage, and in all custom stages. For more information about how to access parameters and secrets in custom script stages, see [Custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize).

## Artifact scan and sign
{: #devsecops-ci-pipeline-artifactscan}

The artifact scan and sign stages provide a default behavior for Docker images, with customizable steps:

* Image signing by using GPG Key.
* Container Registry Vulnerability Advisor scanning.



To get started with these stages, provide your artifacts for the pipeline to use the `pipelinectl` interface. You are not required to update the build scripts and the `.pipeline-config.yaml` configuration.



To use a different scan or sign process, or to process artifacts other than Docker images in `icr.io`, you can customize these stages by using the `.pipeline-config.yaml` configuration in your project.

## Deploy to dev
{: #devsecops-ci-pipeline-devdeploy}

The Deploy stage deploys built artifacts into a dev environment.

## Dynamic scan
{: #devsecops-ci-pipeline-dynamic-codescan}

Dynamic code scan is a form of black box vulnerability scanning that allows software teams to scan running applications and identify vulnerabilities.

The Dynamic Scan stage runs immediately after the `Deploy to dev` stage after a successful deployment to the dev environment.

By default, the pipeline provides support to run Zed Attack Proxy (ZAP) Scan, which is a free and open source penetration testing tool that is maintained under the umbrella of OWASP. It performs both API and UI dynamic scans, both of which can be run on the sample hello-compliance-app.

To run the dynamic scan, set the pipeline parameter `opt-in-dynamic-scan` to a nonempty value. To disable the stage from running dynamic scans, set the pipeline parameter `opt-in-dynamic-scan` to empty. For more information about setting pipeline parameters, see [Pipeline parameters](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm).

The CI pipeline creates issues in the Issues Repository based on the severity. The label that is attached to the issue indicates the severity of the vulnerability.

### ZAP API scan
{: #devsecops-zap-api-scan}

The ZAP API scans the application endpoints for possible data leakages, content-type errors, external redirects, code injection, SQL injection, remote OS command injection, and other vulnerabilities that are exposed by the application. The ZAP API scans help developers secure the application by detecting these vulnerabilities in a stage or test environment and fixing them before the application is deployed on a production environment.

ZAP API scans require the following inputs to scan your application:

* Swagger Definition File - Describing the HTTP APIs and their associated parameters as exposed by the app.
* API Key - Authentication token that is required to authenticate with the API endpoints.
* API Endpoint - Endpoints from the swagger definitions that you want ZAP to scan.
* Excluded URLs - The URLs to be ignored by ZAP scanner.

The ZAP API Scanner uses the inputs that are mentioned to run scan and produce a report.

Set `opt-in-dynamic-api-scan` to a nonempty value for ZAP API scans to run. To opt out, set this parameter to empty.

### ZAP UI Scan
{: #devsecops-zap-ui-scan}

ZAP UI scans the application endpoints for vulnerabilities on the web pages themselves, like unsecure cookies that are being set, improper caching header settings, cross domain file inclusions, improper CORS settings, and other vulnerabilities that are exposed by the application.

UI scans work similarly to API scans, but they use a UI test script instead of a Swagger file. The UI test script starts a headless browser that is configured to proxy through the ZAP scanner's proxy, and it runs a UI test against the UI endpoint. The process to run ZAP UI tests is as follows:

* Copy the tests scripts to the ZAP Scanner containers.
* Run the ZAP Proxy.
* Run the zap test scripts.

The ZAP proxy records the traffic and discovers the endpoints to scan. After the scans are finished, the proxy produces a report and generates issues the same way as in the ZAP API scan.

Set `opt-in-dynamic-ui-scan` to a nonempty value for ZAP API scans to run. To opt out, set this parameter to empty.

* Add your own dynamic scan code to the dynamic-scan custom stage in your `.pipeline-config.yaml` file for a custom implementation.

For more information about the ZAP API scan and ZAP UI scans, see [Configuring ZAP scans](/docs/devsecops?topic=devsecops-zap-scans).

## Release to inventory
{: #devsecops-ci-pipeline-inventoryrel}

Use the release to inventory user script stage to add artifacts to the inventory by using the `cocoa inventory add` CLI command. For more information about the command, see the topic for [cocoa inventory add](/docs/devsecops?topic=devsecops-cd-devsecops-cli#inventory-add).

If you want to skip the inventory update if there are problems in the pipeline, use the following environment variables to check the status of the pipeline. Check their status before updating the inventory:

* `skip-inventory-update-on-failure`	Opt-in environment variable from pipeline to specify whether inventory update should be done.
* `one-pipeline-status`					Is set to `1` if there is any stage failure in the pipeline run.

You can use the `pipelinectl` interface to access your repos and artifacts by using the `list_repos`, `load_repo`, `list_artifacts`, and `load_artifact` commands. For more information about the commands, see the [pipelinectl](/docs/devsecops?topic=devsecops-devsecops-pipelinectl) documentation.


## Collecting compliance data on build
{: #devsecops-ci-pipeline-collectdata}

When the pipeline runs successfully, you can collect information about the build.

Evidence is collected on all checks, scans, tests, and artifact signing into the evidence locker. The pipeline log files are also saved to the locker, along with the pipeline data itself, containing the Tekton definitions. Compliance data on peer reviews is also collected in this step. The pipeline uses `pipelinectl` to search for repos with pull requests that were merged since the last build. It also checks their review status, saves it as an artifact, and creates evidence based on the result.

The final script is an evaluator, which marks the pipeline status green or red, based on the evidence statuses. If they contain any failures, the continuous integration run is marked as red.

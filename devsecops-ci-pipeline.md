---

copyright:
  years: 2021, 2022
lastupdated: "2022-03-01"

keywords: DevSecOps

subcollection: devsecops

---

{:shortdesc: .shortdesc}
{:table: .aria-labeledby="caption"}
{:external: target="_blank" .external}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:download: .download}
{:help: data-hd-content-type='help'}
{:support: data-reuse='support'}

# Continuous integration pipeline
{: #cd-devsecops-ci-pipeline}

The continuous integration pipeline builds the deployable artifacts from the application repositories.
{: shortdesc}

Before it builds artifacts, the pipeline checks that the code is scanned and tested, in the same way that pull requests are processed. Built artifacts are also scanned for vulnerabilities and signed in the pipeline before they are marked ready for release and deployment in the [inventory](/docs/devsecops?topic=devsecops-cd-devsecops-inventory). Unlike the pull request pipeline, the continuous integration pipeline collects evidence and result artifacts on each stage of the build, such as testing, scanning, and signing. This data correlates to the built artifacts and can be tracked through the deployment process and change management.

## Stages and tasks
{: #devsecops-ci-pipeline-stages}

|Task or stage |Short description	|Customizable in `.pipeline-config.yaml` |
|:----------|:------------------------------|:------------------|
|`start` 		|Set up the pipeline environment. 		|No		|
|`setup`		|Set up your build and test environment.		|Yes			|
|`test`		|Run unit tests and application tests on application code. 		|Yes		|
|`static-scan`		|Run static scan code on the application code.		|Yes		|
|`compliance-checks` 		|Run Code Risk Analyzer scans and other compliance checks on app repos.   	|No			|
|`containerize` 		|Build the artifacts. 		|Yes			|
|`sign-artifact` 		|Sign the built artifacts.   	|Yes			|
|`deploy`		|Deploy the built artifacts to the dev environment. 		|Yes		|
|`dynamic-scan` 		|Run dynamic scan on the application. 		|Yes			|
|`acceptance-test`		|Run acceptance and integration tests on the deployed built artifacts on the dev environment.		|Yes		|
|`scan-artifact` 		|Scan the built artifacts.   	|Yes			|
|`release` 		|Add the built artifacts to the inventory. 		|Yes			|
|`finish` 		|Collect, create, and upload the logs files, artifacts, and evidence to the evidence locker.   	|No			|
{: caption="Table 1. Continuous integration stages and tasks" caption-side="top"}

For more information about how to customize stages by using the `.pipeline-config.yaml` file, see [Custom scripts](/docs/devsecops?topic=devsecops-custom-scripts) and [Pipeline parameters](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm#cd-ci-parameters) lists.

## Static code scan
{: #devsecops-ci-pipeline-static-codescan}

The static code scan stage runs a number of static code analyzer tools on the specified app repositories. The repos that are provided by the [`pipelinectl save_repo`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#save_repo) command and the default app repo are scanned.

You can use any of the following methods to add static code to your pipeline:

* Provide an already running SonarQube instance name, URL, and credentials by adding the SonarQube tool to your toolchain. The static-scan task runs a scan on the specified repos.

* Add your own static scan code to the static-scan custom stage in your `.pipeline-config.yaml` file for a custom implementation.

## Scans and checks in compliance checks
{: #devsecops-ci-pipeline-compliancechecks}

| Scan or check |  Description |
|---------|------------|
| Detect secrets | The [IBM Detect Secrets](https://github.com/IBM/detect-secrets){: external} tool identifies where secrets are visible in app code. |
| Code Risk Analyzer vulnerability scan | Finds vulnerabilities for all of the app package dependencies, container base images, and operating system packages. Uses the Code Risk Analyzer tool. |
| Code Risk Analyzer CIS check |  Runs configuration checks on Kubernetes deployment manifests. Uses the Code Risk Analyzer tool. |
| Code Risk Analyzer Bill of Material (BOM) check | The BOM for a specified repo that captures the pedigree of all of the dependencies. This BOM is collected at different granularities. For example, the BOM captures the list of base images that are used in the build, the list of packages from the base images, and the list of app packages that are installed over the base image. The BOM acts as a ground truth for the analytic results and can potentially be used to enforce policy gates. Uses the Code Risk Analyzer tool. |
| Repository compliance checking | Checks that branch protection settings are correct. |
{: caption="Table 5. Compliance scans and checks" caption-side="top"}
    
These scripts are run on all of the app repos that the pipeline is aware of. To add repos to these scans, use the [`pipelinectl`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl) interface that is provided in your setup stage.

For more information about the expected output from user script stages, see [Custom scripts](/docs/devsecops?topic=devsecops-custom-scripts).

## Build
{: #devsecops-ci-pipeline-build}

In the Build stage, you can build your own artifacts. Although the pipeline provides some default features for Docker image type artifacts, you can build any type of artifact in this stage.

Use the environment variables from the Pipeline UI to provide credentials, secrets, and parameters for your build. You can access these environment variables in this stage, and in all custom stages. For more information about how to access parameters and secrets in custom script stages, see [Custom scripts](/docs/devsecops?topic=devsecops-custom-scripts#cd-devsecops-scripts-secrets).

## Artifact scan and sign
{: #devsecops-ci-pipeline-artifactscan}

The artifact scan and sign stages provide a default behavior for Docker images, with customizable steps:

* Image signing by using GPG Key.
* Container Registry Vulnerability Advisor scanning.

To get started with these stages, provide your artifacts for the pipeline to use the `pipelinectl` interface. You are not required to update the build scripts and the `.pipeline-config.yaml` configuration.

To use a different scan or sign process, or to process artifacts other than Docker images in `icr.io`, you can customize these stages by using the `.pipeline-config.yaml` configuration in your project.

## Deploy to dev
{: #devsecops-ci-pipeline-devdeploy}

The Deploy stage deploys built artifacts into a dev environment. You can provide your variables and credentials for this stage from [variables in the pipeline UI](/docs/devsecops?topic=devsecops-custom-scripts#cd-devsecops-scripts-secrets) and the [pipeline trigger webhook payload](/docs/devsecops?topic=devsecops-cd-devsecops-webhook-payloads).

## Dynamic scan
{: #devsecops-ci-pipeline-dynamic-codescan}

The Dynamic scan stage runs a dynamic application security testing tool for finding vulnerabilities in the deployed application.

By default, the pipeline provides support to run OWASP ZAP Scan. ZAP is an open source web application security scanner that provides several features to scan applications for common vulnerabilities. It performs both API and UI dynamic scans, both of which are required for the sample hello-compliance-app.

### ZAP API Scan
{: #devsecops-zap-api-scan}

API scans work by providing a Swagger file that describes the API exposed by the app along with a few other inputs like the API key to use to authenticate with API. The scanner will systematically scan all the endpoints and produce a report.

### ZAP UI Scan
{: #devsecops-zap-ui-scan}

UI scans work similarly to API scans, but instead of providing a Swagger file, you provide a UI test script. This test script needs to be copied inside the ZAP scanner container and be run after the ZAP proxy is running before running the scan. The test script should start a headless browser that is configured to proxy through the ZAP scanner's proxy, and run a UI test that hits the UI endpoints. The ZAP proxy records the traffic and discovers the endpoints to scan. Then, the scan runs, produces a report, and generates issues in the same way as the API scan.

* Add your own dynamic scan code to the dynamic-scan custom stage in your `.pipeline-config.yaml` file for a custom implementation.

## Release to inventory
{: #devsecops-ci-pipeline-inventoryrel}

Use the release to inventory user script stage to add artifacts to the inventory by using the `cocoa inventory add` CLI command. For more information about the command, see the topic for [cocoa inventory add](/docs/devsecops?topic=devsecops-cd-devsecops-cli#inventory-add).

You can use the `pipelinectl` interface to access your repos and artifacts by using the `list_repos`, `load_repo`, `list_artifacts`, and `load_artifact` commands. For more information about the commands, see the [pipelinectl](/docs/devsecops?topic=devsecops-devsecops-pipelinectl) documentation.

## Collecting compliance data on build
{: #devsecops-ci-pipeline-collectdata}

When the pipeline runs successfully, you can collect information about the build.

Evidence is collected on all checks, scans, tests, and artifact signing into the evidence locker. The pipeline log files are also saved to the locker, along with the pipeline data itself, containing the Tekton definitions. Compliance data on peer reviews is also collected in this step. The pipeline uses `pipelinectl` to search for repos with pull requests that were merged since the last build. It also checks their review status, saves it as an artifact, and creates evidence based on the result.

The final script is an evaluator, which marks the pipeline status green or red, based on the evidence statuses. If they contain any failures, the continuous integration run is marked as red.

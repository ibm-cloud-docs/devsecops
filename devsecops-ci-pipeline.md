---

copyright:
  years: 2021, 2022
lastupdated: "2022-03-24"

keywords: DevSecOps, scan, inventory, compliance

subcollection: devsecops, ci pipeline

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

* If you don't have your own SonarQube instance, the pipeline creates a SonarQube instance during the pipeline run. You can access this instance after the static-scan stage successfully runs.

* Add your own static scan code to the static-scan custom stage in your `.pipeline-config.yaml` file for a custom implementation.

### Adding SonarQube scan to your pipelines
{: #devsecops-ci-pipeline-sonarqube-info}

For more information about integrating SonarQube with the continuous integration pipeline, see the [Configuring SonarQube](/docs/devsecops?topic=ContinuousDelivery-sonarqube) section.

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

Dynamic code scan is a form of black-box vulnerability scanning that allows software teams to scan running applications and identify vulnerabilities.

The Dynamic Scan stage runs immediately after the `Deploy to dev` stage, after a successful deployment to the dev environment.

By default, the pipeline provides support to run Zed Attack Proxy (ZAP) Scan, which is a free and open-source penetration testing tool that is maintained under the umbrella of OWASP. It performs both API and UI dynamic scans, both of which can be run on the sample hello-compliance-app.

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

The ZAP API Scanner uses the inputs mentioned to run scan and produce a report.

Set `opt-in-dynamic-api-scan` to a nonempty value for ZAP API scans to run. To opt out, set this parameter to empty.

### ZAP UI Scan
{: #devsecops-zap-ui-scan}

ZAP UI scans the application endpoints for vulnerabilities on the web pages themselves, like unsecure cookies being set, improper caching header settings, cross domain file inclusions, improper CORS settings, and other vulnerabilities that are exposed by the application.

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

You can use the `pipelinectl` interface to access your repos and artifacts by using the `list_repos`, `load_repo`, `list_artifacts`, and `load_artifact` commands. For more information about the commands, see the [pipelinectl](/docs/devsecops?topic=devsecops-devsecops-pipelinectl) documentation.

## Collecting compliance data on build
{: #devsecops-ci-pipeline-collectdata}

When the pipeline runs successfully, you can collect information about the build.

Evidence is collected on all checks, scans, tests, and artifact signing into the evidence locker. The pipeline log files are also saved to the locker, along with the pipeline data itself, containing the Tekton definitions. Compliance data on peer reviews is also collected in this step. The pipeline uses `pipelinectl` to search for repos with pull requests that were merged since the last build. It also checks their review status, saves it as an artifact, and creates evidence based on the result.

The final script is an evaluator, which marks the pipeline status green or red, based on the evidence statuses. If they contain any failures, the continuous integration run is marked as red.

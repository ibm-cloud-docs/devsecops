---

copyright:
  years: 2023, 2024
lastupdated: "2024-02-01"

keywords: DevSecOps, custom scripts, scripts, pipeline stages

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# DevSecOps Pipelines stages and custom scripts explained
{: #cd-devsecops-pipelines-stages}

## Pipelines stages explained
{: #cd-devsecops-scripts-stages}

This section lists the stages in Pull Request (PR), Continuous Integration (CI), Continuous Deployment (CD) and Continuous Compliance (CC) pipelines.

### Pull Request (PR) pipeline stages
{: #cd-devsecops-pipeline-stages}

The PR pipeline runs compliance checks on a pull request for a given application repository and acts as a safeguard for merging into the main branch. The PR pipeline is triggered by opening or updating a pull request against a given branch.

For more information, see [Pull Request (PR) pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-pr-pipeline) documentation.

|PR pipeline stage |Description |
|:--- |:--- |
|code-pr-start|Clone Github repository association with the Pull Request|
|code-setup|Set up your build and test environment to prepare the rest of the pipeline stages|
|code-detect-secrets|Runs detect secrets scan on application code|
|code-unit-tests|Executes unit test for code associated with the PR|
|code-compliance-checks|Code Risk Analyzer (CRA) or Mend Unified Agent scans for vulnerability detection on open-source dependencies, as well as any other compliance checks on application repositories|
|code-pr-finish|Consolidate pipeline status. If any of the compliance checks previously executed in the pipeline fail, this stage fails. This is the "fail safe" behavior to ensure that compliance issues are remediated before a PR can be merged and/or a build can be deployed.|
{: caption="Table 1. Pull Request (PR) pipeline stages" caption-side="bottom"}
{: #pr-pipeline}

### Continuous Integration (CI) pipeline stages
{: #cd-devsecops-ci-pipeline-stages}

The CI pipeline builds deployable artifacts from application repositories. It scans, tests, and signs built artifacts while also collecting evidence on each stage to be released in the inventory and tracked through the deployment and change management.

For more information, see [Continuous Integration (CI) pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-ci-pipeline) documentation.

| CI pipeline stage |Description |
|:---|:---|
|code-pr-start|Set up the pipeline environment, including cloning the configuration and application repositories to the pipeline local file system|
|code-setup|Set up your build and test environment to prepare the rest of the pipeline stages|
|code-unit-tests|Executes unit tests and application tests on application code|
|code-peer-review|Enforces the [Peer Review](/docs/devsecops?topic=devsecops-cd-devsecops-peer-review) of code changes before they are merged and promoted to production|
|code-static-scan|Runs static scan on application code (default is SonarQube but other tools can be used)|
|code-compliance-checks|Runs [Code Risk Analyzer (CRA)](/docs/devsecops?topic=devsecops-cd-devsecops-cra-scans) or [Mend Unified Agent](/docs/devsecops?topic=devsecops-cd-devsecops-mend-scans) scan for vulnerability detection on open-source dependencies, as well as any other compliance checks on application repositories|
|build-artifact|Build the artifacts and images from the source code repository|
|build-sign-artifact|Signs built artifacts and images|
|build-scan-artifact|Scan the container image with [Vulnerability Advisor](/docs/devsecops?topic=devsecops-cd-devsecops-va-scans)|
|deploy-dev|Deploys the built artifacts to the dev environment|
|code-dynamic-scan|Runs the [OWASP-Zap API and UI dynamic scans](/docs/devsecops?topic=devsecops-cd-devsecops-zap-scans) on the application that is deployed to a dev environment|
|deploy-acceptance-tests|Runs acceptance and integration tests on the deployed built artifacts on the dev environment|
|deploy-release|Add the built artifacts to the inventory which will be used by the Continuous Delivery (CD) pipeline|
|code-ci-finish|Collect, create, and upload the logs files, artifacts, and evidence to the evidence locker. If any of the compliance checks previously executed in the pipeline fail, this stage fails. This is the "fail safe" behavior to ensure that compliance issues are remediated before a PR can be merged and/or a build can be deployed.|
{: caption="Table 2. Continuous Integration (CI) pipeline" caption-side="bottom"}
{: #ci-pipeline}

### Continuous Deployment (CD) pipeline stages
{: #cd-devsecops-cd-pipeline-stages}

The CD pipeline compiles all the content for evidence and the change request summary. It deploys the build to an environment, such as staging or production, while uploading all evidence to the evidence locker.

For more information, see [Continuous Deployment (CD) pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-cd-pipeline) documentation.

| CD pipeline stage |Description |
|:---|:---|
|prod-start|Calculates the [deployment delta](/docs/devsecops?topic=devsecops-cd-devsecops-promote-branches) (services that will be updated by the CD deployment)|
|prod-setup|Set up the target environment to prepare the rest of the CD pipeline stages|
|prod-verify-artifact|[Verifies signatures](/docs/devsecops?topic=devsecops-devsecops-image-verify) of images and artifacts that were built in the CI pipeline|
|prod-change-request|Creation, approval of the [Change Request](/docs/devsecops?topic=devsecops-cd-devsecops-automate-changemgmt) associated to this deployment, and switch to implement state|
|prod-deployment|Deploys the inventory delta to the target environment|
|prod-acceptance-tests|Runs acceptance and integration tests on the deployed built artifacts on the target environment|
|prod-finish|Collects all evidence to the evidence locker, and close change request|
{: caption="Table 3. Continuous Deployment (CD) pipeline" caption-side="bottom"}
{: #cd-pipeline}

### Continuous Compliance (CC) pipeline stages
{: #cd-devsecops-cc-pipeline-stages}

The CC pipeline performs periodic scans on deployed artifacts and their source repositories. It scans the latest deployed state in the inventory and creates, updates or auto-closes incident issues in the incident repository.

For more information, see [Continuous Compliance (CC) pipeline](/docs/devsecops?topic=devsecops-devsecops-cc-pipeline) documentation.

| CC pipeline stage |Description |
|:---|:---|
|cc-start|Clone repository, process inventory based on tag, and register repos and artifacts|
|cc-setup|Set up the CC pipeline environment for the scans in later stages. This may include installing build dependencies or performing Maven builds|
|cc-detect-secrets|Runs detect secrets scan on application code|
|cc-static-scan|Runs static scan on application code (default is SonarQube but other tools can be used)|
|cc-dynamic-scan|Runs the [OWASP-Zap API and UI dynamic scans](/docs/devsecops?topic=devsecops-cd-devsecops-zap-scans) on the application that is deployed to the target environment|
|cc-compliance-checks|Runs [Code Risk Analyzer (CRA)](/docs/devsecops?topic=devsecops-cd-devsecops-cra-scans) or [Mend Unified Agent](/docs/devsecops?topic=devsecops-cd-devsecops-mend-scans) scan for vulnerability detection on open-source dependencies, as well as any other compliance checks on application repositories|
|cc-scan-artifact|Scan the container image with [Vulnerability Advisor](/docs/devsecops?topic=devsecops-cd-devsecops-va-scans)|
|cc-finish|Collect, create, and upload the logs files, artifacts, and evidence to the evidence locker. If any of the compliance checks previously executed in the CC pipeline fails, this stage fails.|
{: caption="Table 4. Continuous Compliance (CC) pipeline" caption-side="bottom"}
{: #cc-pipeline}

### Pipeline compliance checks
{: #cd-devsecops-pipeline-compliance-checks}

The following scans and checks are performed during the code-compliance-checks task within the PR, CI, and CC pipelines.
| Tool |Scan or Check |
|:---|:---|
|Detect secrets|Identifies visible secrets|
|Code Risk Analyzer (CRA)|Finds vulnerabilities for package dependencies, container base images, and OS packages|
|Code Risk Analyzer CIS|Checks configuration on Kubernetes deployment manifests|
|Code Risk Analyzer Bill of Materials (BOM)|Lists all dependencies included in packages and images|
|Mend Unified Agent (UA)|Scans open source components for vulnerabilities|
|Repository compliance|Checks settings of branch protection rules|
{: caption="Table 5. Pipeline compliance checks" caption-side="bottom"}
{: #cc-pipeline}


### Stage descriptions
{: #cd-devsecops-stage-desc}

* `setup`: Run scripts to complete setup tasks that are done outside the pipeline run context. These tasks persist for the entire pipeline run. For example, setting up an external environment, setting up branch protection rules, performing health checks, checking environments, or initializing external services.
* `test`: Run unit tests, include all of your tests that run on your codebase, and pre-deploy time.
* `static-scan`: Run a static code analyzer tool.
* `peer-review`: Run scripts to check whether the pull request is reviewed and has the minimum number of required approvals.
* `dynamic-scan`: Run dynamic scan on the application.
* `containerize`: Create the artifacts that are required by your app, such as Docker images, RPMs, and Java Archive (JARS) files.
* `scan-artifact`: Scan the artifacts, for example, by using Container Registry Vulnerability Advisor for Docker images.
* `sign-artifact`: Sign the artifacts.
* `release`: Add artifacts to the inventory by marking them available for deployment.
* `deploy`: Deploy artifacts to an environment such as test and dev or staging and prod.
* `acceptance-test`: Run tests on deployed artifacts. You can also include your post-deployment tests in this stage.
* `verify-artifact`: Run scripts to check whether the artifacts that are signed in the CI pipeline have valid signatures.
* `finish`: This stage is customizable stage and is always executed, irrespective of pipeline status.

## Customizing pipelines using custom scripts
{: #cd-devsecops-pipelines-custom-customize}

Custom scripts are extension points in the pipeline where adopters, teams, and users can provide scripts to run custom tasks that are required by their continuous integration and continuous deployment strategies.
{: shortdesc}

Custom scripts control the pipeline stages. You can use a configuration file (`pipeline-config.yaml`) to configure the behavior of stages, script content, and the base image that runs the scripts. The scripts and configuration for pipeline stages are loaded from a Git repository (repo) that can either be the application (app) repo (similar to `.travis.yml` or `Jenkinsfile`) or a custom repo.

When any of the custom scripts are started, the complete URL of the custom script file, including the file name and the commit hash, is printed at the beginning of the pipeline logs as follows: `The custom script can be viewed using the following link: 'https://<source repo url>/<organization name>/<repository name>/blob/<commit hash>/.pipeline-config.yaml'`. This positioning improves traceability.

Best practices and guidance can be found [here](/docs/devsecops?topic=devsecops-cd-devsecops-basics-pipelines-customization).

### Configuration in `pipeline-config.yaml`
{: #cd-devsecops-scripts-config}

You can use a `.pipeline-config.yaml` configuration file to extend pipeline behavior.

### File location
{: #cd-devsecops-scripts-location}

For pull request and continuous integration pipelines, store the `.pipeline-config.yaml` configuration file in an app repo in the same manner as the `.travis.yml`, or `Jenkinsfile` files. For continuous deployment pipelines, store this file in a dedicated repo.

For all pipelines, you can customize the location and source of the `.pipeline-config.yaml` file with the following pipeline UI parameters:

* `pipeline-config` to set the path of the configuration file. The default value is `.pipeline-config.yaml`.
* `pipeline-config-repo` to set the repo to pull the configuration and scripts from. The default value is the continuous integration app repo.
* `pipeline-config-branch` to use as the branch for the configuration in the config repo. The default value is the continuous integration app repo branch, and the `master` branch in continuous deployment.

### Configuration parameters
{: #cd-devsecops-scripts-configparm}

The configuration in the `.pipeline-config.yaml` file supports the following parameters:

These settings are not pipeline parameters, they must be part of your `.pipeline-config.yaml` file.
{: important}

* `image`: You can use any Docker image that is accessible by the pipeline worker as a base image for the task.

* `script`: The script to run in the stage. Because this field is used as a script file, ensure that the contents act like a script file in the base image that you provided. You can include other scripts next to this configuration file, and refer to them from this entry point. For example:

```bash
test:
  image: ibmcom/pipeline-base-image:2.7
  script: |
    #!/bin/sh
    scripts/lint.sh
    scripts/unit-test.sh
```
{: codeblock}

* `dind`: Specify whether to enable `docker-in-docker` for the script context. The default setting is `false`.

* `abort_on_failure`: By default, the pipeline stops when the script fails. Setting this to `false` marks the stage in a warning (amber state) (it passed with warning), allows the pipeline to continue, and lets the failed job be referenced in the evidence later.

* `image_pull_policy`: Set the pod `ImagePullPolicy` setting that is provided in the image, for the base image. Possible values are the same as valid values for Kubernetes: `Always` or `IfNotPresent`. The default value is `IfNotPresent`.

* `configmap`: Pull key-value pairs from a provided configmap that is accessible by `PipelineRun`. The following values are supported:

    * `config-name`: The direct configmap name syntax. The stage runner tries to access and mount the configmap named `config-name`.
    * `$prop-name`: The indirect configmap name syntax. The configmap name is looked up from the environment-properties configmap, which contains every environment value that is set on the pipeline UI. For example, if there is a prop: `my-config` entry in the environment properties, then `my-config` is used for `$prop`.

* `secret`: Pull key-value pairs from a provided configmap that is accessible by `PipelineRun`. The following values are supported:

    * `secret-name`: The direct Secret name syntax. The stage runner tries to access and mount the Secret named `secret-name`.

    * `$prop-name`: The indirect Secret name syntax. The Secret name is located in the environment-properties configmap that contains every environment value that is set on the pipeline UI. For example, if the environment-properties configmap contains the `my-secret` entry property, `my-secret` is used for `$prop`.

* `runAfter`: Run the current stage after the stage that specified in this property completes. Set the value with the stage name that you used in `.pipeline-config.yaml`. Use this property sparingly and confirm that the stage that is specified by this property exists in the pipeline. If the stage does not exist, the pipeline can go into a deadlock.

* `skip`: If set to `true`, bypass the current stage, if possible, in v10 pipelines. Not all stages can be bypassed. The following table lists the stages that can be skipped during the pipeline run.

| Pipeline | Stages |
|-|-|
PR pipeline|`code-unit-tests`, `code-compliance-tests` and `code-pr-finish`|
|CI pipeline|`code-unit-tests`, `code-static-scan`, `code-compliance-checks`, `build-scan-artifact`, `code-dynamic-scan`, `deploy-acceptance-tests`, `deploy-release` and `code-ci-finish`|
|CD pipeline|`prod-verify-artifact`, `prod-acceptance-tests` and `prod-finish`|
|CC pipeline|`cc-static-scan`, `cc-dynamic-scan`, `cc-compliance-checks`, `cc-scan-artifact` and `cc-finish`|
|App-preview PR pipeline|`code-unit-tests`, `code-static-scan`, `code-compliance-checks`, `build-scan-artifact`, `deploy-acceptance-tests` and `app-preview-pr-finish` |
|Dev-mode CI pipeline|`code-unit-tests`, `code-static-scan`, `deploy-release` and `code-ci-finish`|
|Dev-mode CD pipeline|`prod-acceptance-tests` and `prod-finish`|
{: caption="Table 1. Stages that can be skipped in pipeline runs" caption-side="top"}

#### Example configuration
{: #cd-devsecops-scripts-sample-config}

```bash
version: '1' # fixed, this value is used to track schema changes

# `setup` runs right after the app repo is cloned
setup:

  # the pipeline will break in case setup fails (default is true)
  abort_on_failure: true

  # any docker image can be used which is accessible by the private worker
  image: ibmcom/pipeline-base-image:2.7

  # you can reference configmaps, each key in the configmap is going to be available as `/config/{key}`
  configmap: my-config

    # `$prop` is the indirect config map syntax, the concrete configmap is looked
    # up from the `environment-properties` configmap
    #
    # Eg. if there's a `prop: my-config` entry in the environment properties,
    # then `my-config` is going to be used for `$prop`
  configmap: $prop

  # the mechanism described works for secrets as well!
  secret: $my-secrets

  # the script is executed inside the checked out app repo
  script: |
    #!/bin/sh
    ...

# `test` runs after `setup`, but before building the docker image
test:
  image: ibmcom/pipeline-base-image:2.7
  script: |
    #!/bin/sh
    ...

# `static-scan` runs after `test`, but before building the docker image
static-scan:
  image: ibmcom/pipeline-base-image:2.12
  script: |
    #!/bin/sh
    ...


# `deploy` runs after building the docker image
deploy:
  image: ibmcom/pipeline-base-image:2.7

  # the script has access to the built docker image, which is available at `/config/image`
  script: |
    #!/bin/sh

    cat /config/image

# `dynamic-scan` runs after `deploy`, but before the acceptance test run
dynamic-scan:
  image: ibmcom/pipeline-base-image:2.12
  script: |
    #!/bin/sh
    ...

# `acceptance-test` runs after `deploy`
acceptance-test:
  image: ibmcom/pipeline-base-image:2.7
  script: |
    #!/bin/sh
    ...
```

To view the `.pipeline-config.yaml` configuration file that is used by default for the example app in the reference pipeline template, see [configuration for pull requests and continuous integration](https://us-south.git.cloud.ibm.com/open-toolchain/hello-compliance-app/-/blob/master/.pipeline-config.yaml){: external} and [configuration for continuous deployment](https://us-south.git.cloud.ibm.com/open-toolchain/hello-compliance-deployment/-/blob/master/.pipeline-config.yaml){: external}.

## Secrets, parameters, and properties
{: #cd-devsecops-scripts-secrets}

Use [pipelinectl commands](/docs/devsecops?topic=devsecops-devsecops-pipelinectl) to read or write data from the pipeline at run time. You can read and write secrets, variables, repo data, artifact data, and evidence result artifacts (such as test result files) by way of the pipelinectl interface.

You do not need to install the pipelinectl tool for your scripts or base images, the reference pipeline provides the binary files for the context of your script.
{: tip}

### Accessing pipeline environment secrets and properties
{: #cd-devsecops-scripts-accesssecrets}

Environment properties and secrets (defined on the pipeline UI) are accessible through `pipelinectl`:

```bash
# if you set my_config_value on the UI, it's accessible via `get_env`
my_config_value=$(get_env my_config_value)
```

You can set other variables by using `set_env`:

```bash
set_env my_config_value "some value"

# later on, even in another stage
my_config_value=$(get_env my_config_value)
```

These values are not persisted, they're scoped for a single pipeline run.
{: tip}

#### Example
{: #cd-devsecops-scripts-sample-artifactory}

You can create an Artifactory auth credential for your own build by adding the required parameters to the pipeline UI:

    my-artifactory-email
    my-artifactory-user
    my-artifactory-password
    my-artifactory-url

When these parameters are available in the pipeline, you can access them in the user scripts and create a Docker auth JSON file. For example:

```bash
kubectl create secret docker-registry regcred \
  --o json \
  --dry-run=client \
  --docker-server="$(get_env my-artifactory-url)" \
  --docker-username="$(get_env my-artifactory-user)" \
  --docker-email="$(get_env my-artifactory-email)" \
  --docker-password="$(get_env my-artifactory-password)" \
   | jq -r '.data[".dockerconfigjson"]'

{"auths":{"us.icr.io":{"username":"user@ibm.com","password":"secret-password","email":"user@ibm.com","auth":"dXNlckBpYm0uY29tOnNlY3JldC1wYXNzd29yZA=="}}}
```

### Parameters for custom scripts
{: #cd-devsecops-scripts-parm}

In addition to the pipeline environment variables and secrets, these parameter values are provided for user script stages.

You can access repos, artifacts, files, and environment variables by using the `pipelinectl` command in the user scripts. Use the following methods: `list_repos`, `load_repo`, `load_artifact`, `list_artifacts`, and `get_env`.

#### `pipelinectl`
{: #cd-devsecops-scripts-pipelinectl}

`app-repo` is the default app repo that is cloned internally and added to the pipeline by using the `save_repo pipelinectl` interface with the `app-repo` reference name. If you set up the pipeline from the toolchain template, the default repo is either provided by the repo pipeline UI parameter or selected by its toolchain binding name. Use the `load_repo` command to access properties from the saved `app-repo`.

#### Values in `/config/`
{: #cd-devsecops-scripts-configvalue}

To support user script compatibility with an earlier version, these values are copied to `/config/`. Because the availability of these files depends on the pipeline context, make sure that they are available before you use their value.

| Path | Description |
|-------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| `/config/api-key` | The value of `ibmcloud-api-key` copied, for script compatibility with an earlier version.|
| `/workspace/git-token` | The Git token that is obtained in the beginning of the pipeline for script compatibility with an earlier version. |
| `/config/environment` | The deployment target environment for [inventory](/docs/devsecops?topic=devsecops-cd-devsecops-inventory). |
| `/config/deployment-delta-path` | The path to the JSON file that contains the deployment delta. |
| `/config/inventory-path` | The path to the inventory repo content. |
| `/config/git-branch` | The current, checked out Git branch of the app repo. |
| `/config/git-commit` | The latest Git commit of the checked out app repo. |
| `/config/repository-url` | The app repo URL. |
| `/config/inventory-url` | The inventory repo URL. |
| `/config/image` | The built Docker image artifact that contains the registry, namespace, name, and digest of the image. |
| `/config/artifact` | The built Docker image artifact that contains the registry, namespace, name, and digest of the image. |
| `/config/signature` | The image artifact signature. |
{: caption="Table 2. 'config' Values" caption-side="top"}

These values are phased out and replaced by the stage I/O interface by way of pipelinectl.
{: deprecated}

#### Environment variables
{: #cd-devsecops-scripts-envvar}

The following table includes the default ENV variables for the context of custom script stages that are provided by the pipeline:

| Path | Description |
|-------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| `BUILD_NUMBER` | The Tekton pipeline build number.|
| `PIPELINE_CONFIG_PATH` | The path to the root of the pipeline script library. |
| `PIPELINE_CONFIG_REPO_PATH` | The directory where the pipeline `config-repo` is cloned. |
| `PIPELINE_RUN_NAME` | The Tekton pipeline run name. |
| `PIPELINE_RUN_ID` | The Tekton pipeline run ID. |
| `PIPELINE_RUN_URL` | The Tekton pipeline run URL. |
| `PIPELINE_ID` | The Tekton pipeline ID. |
| `STAGE` | The user script stage. |
| `TOOLCHAIN_DATA_PATH` | The toolchain data path `/toolchain/toolchain.json` file. |
| `TRIGGER_TYPE` | The pipeline trigger type. |
| `TRIGGER_NAME` | The pipeline trigger name. |
| `TRIGGER_PAYLOAD` (DEPRECATED) | The payload that is received by way of the pipeline trigger event, if there was one. For more information about working with payloads, see [Accessing arguments from webhook payloads](/docs/devsecops?topic=devsecops-cd-devsecops-webhook-payloads). |
| `TRIGGERED_BY` | Information about who triggered the pipeline. This value can be an email address or a time-triggered pipeline run.  |
| `WORKSPACE` | The path to the shared workspace. |
| `PIPELINE_STATUS` | Status of the pipeline run, can be "Succeeded", "Failed", or blank. `PIPELINE_STATUS` is available in finish stages only |
| `INVENTORY_URL` | The inventory repository url. |
| `EVIDENCE_LOCKER_URL` | The evidence repository url. |
| `INCIDENT_ISSUES_URL` | The issues repository url. |
{: caption="Table 3. Environment variables" caption-side="top"}

You can access these environment variables in any script, for example, `${PIPELINE_ID}`.

### Accessing toolchain data from custom scripts
{: #devsecops-scripts-toolchaindata}

You can read toolchain data from the `toolchain.json file` that is provided by the pipeline at the path that is stored in the `TOOLCHAIN_DATA_PATH` environment variable. You can also use the toolchain integration and `jsonpath`, and access them in `/config` in the same way as any other variable. Or, you can use `get_env`, which has the same effect.

#### Example usage
{: #devsecops-scripts-toolchaindata-example}

```bash
TOOLCHAIN_CONFIG_JSON="$(get_env TOOLCHAIN_CONFIG_JSON)"
toolchain_id=$(jq -r '.toolchain_guid' "$TOOLCHAIN_CONFIG_JSON")
```
{: codeblock}

### Shared workspaces
{: #devsecops-scripts-workspaces-shared}

Because these custom scripts operate in the same workspace, you can pass data between them by writing it into a file and then reading it in the other task. This workspace is mounted on a path that is provided in the `$WORKSPACE` variable.

#### Example usage
{: #devsecops-scripts-workspaces-shared-example}

Writing:

```bash
echo -n "$DIGEST" > "${WORKSPACE}"/image-digest
```
{: codeblock}

Reading:

```bash
image_digest=$(cat "${WORKSPACE}"/image-digest)
```
{: codeblock}

### Custom finish stages
{: #devsecops-scripts-custom-finish}

The finish stage has three steps:

| Step | Description |
|:--|:--|
| `evaluate` | Non customizable step that executes tasks that are related to collection and upload of log files, artifacts, and evidence to the evidence locker. |
| `prepare` | Non customizable step that sets up the required tools to run the custom finish stage. |
| `finish` | Executes the custom script that is provided in `.one-pipeline-config.yaml`. |
{: caption="Table 4. Steps in finish stage" caption-side="top"}

This pipeline status can be determined in two ways:

1. You can get the overall status of the pipeline run from the environment variable `PIPELINE_STATUS`. The value can be "Succeeded" , "Failed"  or "" (case sensitive). `PIPELINE_STATUS` comes from the underlying Tekton framework.
2. Because the `evaluate` step evaluates a pipeline run from the perspective of evidence, this evaluation might differ from the value set in `PIPELINE_STATUS`. To see the pipeline status based on evidence evaluation, use the environment variable `finish_evaluate_exit_code`.

## Stage output
{: #cd-devsecops-scripts-stageoutput}

Some data is expected to be available by other stages on the workspace, so be sure to create them by using the [`pipelinectl` commands](/docs/devsecops?topic=devsecops-devsecops-pipelinectl). To make the required outputs available to the other stages and scripts in the pipeline, use pipelinectl's save methods, like save_repo, save_artifact, save_result, and set_env.

|Source stage |Description	|`Pipelinectl` method |Required |
|:----------|:------------------------------|:------------------|:----------|
|`setup` 		|You can add more repos to the pipeline compliance checks and static code scan		|`save_repo`		|No		|
|`test`		|To [attach test results to the compliance evidence as evidence artifacts](/docs/devsecops?topic=devsecops-cd-devsecops-add-pipeline-steps), use the `save_result` command in this stage.		|`save_result`			|No		|
|`static-scan`		|To [attach test results to the compliance evidence as evidence artifacts](/docs/devsecops?topic=devsecops-cd-devsecops-add-pipeline-steps), use the `save_result` command in this stage. 		|`save_result`		|No		|
|`dynamic-scan`		|To [attach test results to the compliance evidence as evidence artifacts](/docs/devsecops?topic=devsecops-cd-devsecops-add-pipeline-steps), use the `save_result` command in this stage. 		|`save_result`		|No		|
|`containerize`		|[Add artifact names and digests to the pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-config-github).		|`save_artifact`		|Yes		|
|`sign-artifact` 		|Add the image signature from the GPG signing output.   	|`save_artifact`			|Yes		|
|`acceptance-test` 		|To [attach test results to the compliance evidence as evidence artifacts](/docs/devsecops?topic=devsecops-cd-devsecops-add-pipeline-steps), use the `save_result` command in this stage. 		|`save_result`			|No		|
{: caption="Table 5. Stage output" caption-side="top"}

### Stage results API
{: #cd-devsecops-scripts-resultsapi}

The result states of these custom stages are saved with `pipelinectl`. The result state of a stage is either success, failure, or skipped. These results can be used later to evaluate whether the custom stage ran successfully.

The following tasks and stages are available:

| Pipeline | Task or stages |
|:-----------------|:------------------------------------|
|**Pull request pipeline stages**| setup, test, detect-secrets, and branch-protection. The detect-secrets and branch-protection stages are not custom stages. They are provided by the pipelines by default.|
|**Continuous integration pipeline stages**| `setup`, `test`, `static-scan`, `containerize`, `sign-artifact`, `deploy`, `acceptance-test`, `scan-artifact`, `release`, `detect-secrets`, `branch-protection`, `bom-check`, `cis-check`, and `vulnerability-scan`. The `detect-secrets`, `branch-protection`, `bom-check`, `cis-check`, and `vulnerability-scan` stages are not custom stages. They are provided by the pipelines by default. |
|**continuous deployment pipeline stages**| `setup`, `deploy`, `acceptance-test`, `create-change-request`, `change-request-check-approval`, `change-request-change-state-to-implement`, and `close-change-request`. The `create-change-request`, `change-request-check-approval`, `change-request-change-state-to-implement`, and `close-change-request` stages are not custom stages. They are provided by the pipelines by default.|
|**Continuous compliance pipeline stages**| `setup`, `test`, `static-scan`, `scan-artifact`, `acceptance-test`, `detect-secrets`, `branch-protection`, `bom-check`, `cis-check`, and `vulnerability-scan`. The `detect-secrets`, `branch-protection`, `bom-check`, `cis-check`, and `vulnerability-scan` stages are not custom stages. They are provided by the pipelines by default.|
{: caption="Table 6. Tasks and stages" caption-side="bottom"}

#### Example usage
{: #devsecops-scripts-resultsapi-example}

```bash
# List saved stage results
$ get_data result
detect-secrets
branch-protection

# Get stage result
$ get_data result detect-secrets
success
```
{: codeblock}

For more information about the Stage Results API, see [Using the Stage Results API in custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-stage-results).

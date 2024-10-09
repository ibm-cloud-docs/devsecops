---

copyright:
  years: 2024
lastupdated: "2024-05-24"

keywords: DevSecOps, inventory model, inventory, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Customizing Secrets, parameters, and properties
{: #cd-devsecops-scripts-secrets}

Use [pipelinectl commands](/docs/devsecops?topic=devsecops-devsecops-pipelinectl) to read or write data from the pipeline at run time. You can read and write secrets, variables, repo data, artifact data, and evidence result artifacts (such as test result files) by way of the pipelinectl interface.

You do not need to install the pipelinectl tool for your scripts or base images, the reference pipeline provides the binary files for the context of your script.
{: tip}

## Accessing pipeline environment secrets and properties
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

### Example

{: #cd-devsecops-scripts-sample-artifactory}

You can create an Artifactory auth credential for your own build by adding the required parameters to the pipeline UI:

```bash
     my-artifactory-email
    my-artifactory-user
    my-artifactory-password
    my-artifactory-url
```

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

| Path                            | Description |
|---------------------------------|-------------|
| `/config/api-key`               | The value of `ibmcloud-api-key` copied, for script compatibility with an earlier version.|
| `/workspace/git-token`          | The Git token that is obtained in the beginning of the pipeline for script compatibility with an earlier version. |
| `/config/environment`           | The deployment target environment for [inventory](/docs/devsecops?topic=devsecops-cd-devsecops-inventory). |
| `/config/deployment-delta-path` | The path to the JSON file that contains the deployment delta. |
| `/config/inventory-path`        | The path to the inventory repo content. |
| `/config/git-branch`            | The current checked out Git branch of the app repo. |
| `/config/git-commit`            | The latest Git commit of the checked out app repo. |
| `/config/repository-url`        | The app repo URL. |
| `/config/inventory-url`         | The inventory repo URL. |
| `/config/image`                 | The built Docker image artifact that contains the registry, namespace, name, and digest of the image. |
| `/config/artifact`              | The built Docker image artifact that contains the registry, namespace, name, and digest of the image. |
| `/config/signature`             | The image artifact signature. |
{: caption="'config' Values" caption-side="top"}

These values are phased out and replaced by the stage I/O interface by way of pipelinectl.
{: deprecated}

#### Environment variables
{: #cd-devsecops-scripts-envvar}

The following table includes the default ENV variables for the context of custom script stages that are provided by the pipeline:

| Path                           | Description |
|--------------------------------|-------------|
| `BUILD_NUMBER`                 | The Tekton pipeline build number.|
| `PIPELINE_CONFIG_PATH`         | The path to the root of the pipeline script library. |
| `PIPELINE_CONFIG_REPO_PATH`    | The directory where the pipeline `config-repo` is cloned. |
| `PIPELINE_RUN_NAME`            | The Tekton pipeline run name. |
| `PIPELINE_RUN_ID`              | The Tekton pipeline run ID. |
| `PIPELINE_RUN_URL`             | The Tekton pipeline run URL. |
| `PIPELINE_ID`                  | The Tekton pipeline ID. |
| `STAGE`                        | The user script stage. |
| `TOOLCHAIN_DATA_PATH`          | The toolchain data path `/toolchain/toolchain.json` file. |
| `TRIGGER_TYPE`                 | The pipeline trigger type. |
| `TRIGGER_NAME`                 | The pipeline trigger name. |
| `TRIGGERED_BY`                 | Information about who triggered the pipeline. This value can be an email address or a time-triggered pipeline run.  |
| `WORKSPACE`                    | The path to the shared workspace. |
| `PIPELINE_STATUS`              | The status of the pipeline run. Can be "Succeeded", "Failed", or blank. `PIPELINE_STATUS` is available in finish stages only |
| `INVENTORY_URL`                | The inventory repository url. |
| `EVIDENCE_LOCKER_URL`          | The evidence repository url. |
| `INCIDENT_ISSUES_URL`          | The issue's repository url. |
{: caption="Environment variables" caption-side="top"}

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

| Step       | Description |
|------------|-------------|
| `evaluate` | Non-customizable step that executes tasks that are related to collection and upload of log files, artifacts, and evidence to the evidence locker. |
| `prepare`  | Non-customizable step that sets up the required tools to run the custom finish stage. |
| `finish`   | Executes the custom script that is provided in `.one-pipeline-config.yaml`. |
{: caption="Steps in finish stage" caption-side="top"}

This pipeline status can be determined in two ways:

1. You can get the overall status of the pipeline run from the environment variable `PIPELINE_STATUS`. The value can be "Succeeded", "Failed", or "" (case-sensitive). `PIPELINE_STATUS` comes from the underlying Tekton framework.
2. Because the `evaluate` step evaluates a pipeline run from the perspective of evidence, this evaluation might differ from the value set in `PIPELINE_STATUS`. To see the pipeline status based on evidence evaluation, use the environment variable `finish_evaluate_exit_code`.

---

copyright:
  years: 2022
lastupdated: "2022-06-03"

keywords: DevSecOps, async sub pipelines, ibm cloud

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

# Triggering a stage by using async sub pipelines
{: #devsecops-async-sub-pipelines}

You can trigger any stage that is added to the  `.pipeline-config.yaml` configuration file by using async sub pipelines. You don't need to add those stages inline to the pipeline, and you don't need to modify the pipeline.
{: shortdesc}

See the following example code:

```yaml
setup:
  image: icr.io/continuous-delivery/pipeline/pipeline-base-image:2.12@sha256:ff4053b0bca784d6d105fee1d008cfb20db206011453071e86b69ca3fde706a4
  script: |
    #!/usr/bin/env bash

    source "${ONE_PIPELINE_PATH}"/tools/trigger-task
    set_env "variable-for-my-custom-task" "foo_bar"
    export_env "variable-for-my-custom-task"
    trigger-task "my-custom-task"

my-custom-task:
  image: icr.io/continuous-delivery/pipeline/pipeline-base-image:2.12@sha256:ff4053b0bca784d6d105fee1d008cfb20db206011453071e86b69ca3fde706a4
  script: |
    #!/usr/bin/env bash
    if [[ "$PIPELINE_DEBUG" == 1 ]]; then
      trap env EXIT
      env
      set -x
    fi
    printf "Test custom stage to trigger async"
    list_repos
    list_artifacts
    get_env "variable-for-my-custom-task"
```
{: codeblock}

For triggering the stage, a webhook trigger is created with a webhook secret. The webhook secret is found in the [Pipeline parameters](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm) and in the `Subpipeline Webhook Trigger`'s properties as `subpipeline-webhook-token`. For more information about updating the `subpipeline-webhook-token`'s value, see [Updating the async stage webhooks](/docs/devsecops?topic=devsecops-update-async-webhook).

The newly triggered pipeline name is the stage name (e.g. `my-custom-task`), so make sure that the stage name is a valid pipeline run name. The stage name is used to create the name or ID on a Kubernetes resource. Object names and IDs in Kubernetes must follow [RFC 1123](https://datatracker.ietf.org/doc/html/rfc1123){: external} or [RFC 1035](https://datatracker.ietf.org/doc/html/rfc1035){: external}:

* Contain a maximum of 63 characters.
* Contain only lowercase alphanumeric characters or the hyphen character '-'.
* Start with an alphanumeric character.
* End with an alphanumeric character.

## Passing data to async pipeline
{: #devsecops-pass-data}

You can pass variables that are needed to run the stage, and you can use [`pipelinectl`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl) for this.

You can pass data between two pipelines by following these steps:
1. Use `set_env` to save a variable before you trigger the async pipeline. Note that this can happen in the pipeline before the triggering stage. You don't need to use `set_env` twice.
1. Mark the variable with `export_env` to export it for the async pipeline run.
1. Any variable that is marked for export is available by using `get_env` in the async pipeline.

```bash
set_env <variable-name> <variable-value>
export_env <variable-name>
get_env <variable-name>
```
{: codeblock}

Only the marked variables are available in the async pipeline. Not everything can be exported because the data might be too large or contain sensitive data.
{: important}

### Trigger async pipeline command
{: #devsecops-trigger-async-pipeline}

Use the following command to trigger a stage:

```bash
trigger-task <stage-name>
```
{: codeblock}

### Example code
{: #devsecops-trigger-command-example}

```yaml
setup:
  image: icr.io/continuous-delivery/pipeline/pipeline-base-image:2.12@sha256:ff4053b0bca784d6d105fee1d008cfb20db206011453071e86b69ca3fde706a4
  script: |
    #!/usr/bin/env bash

    source "${ONE_PIPELINE_PATH}"/tools/trigger-task
    set_env "variable-for-my-custom-task" "foo_bar"
    export_env "variable-for-my-custom-task"
    trigger-task "my-custom-task"
```
{: codeblock}

This command triggers the task from the pipeline configuration yaml. For example, `trigger-task owasp-zap`.

###  What is accessible in the triggered pipeline
{: #devsecops-accessible-triggered-pipeline}

Every repository and artifact that are saved and exported (by using `set_env` from [`pipelinectl`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl) and the [`export_env` tool](/docs/devsecops?topic=devsecops-devsecops-async-stages)) in the previous pipeline are available in the new triggered async pipeline as well. Repositories are cloned with the commit in the previous pipeline into the same workspace-relative folders. The commit of repositories must be saved in `pipelinectl` for the pipeline to can be able to clone the same state of repository.

To get to repositories and artifacts use [`list_repos`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#list_repos) and [`list_artifacts`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#list_artifacts). You can get all artifacts that were built and all repositories that were cloned only you must save them in [`pipelinectl`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl).

**Not Available**
It is not available any changes on the workspace that is made in previous tasks in the triggering pipeline run (for example, npm install) because we cannot pass the all workspace into the new triggered pipeline run.

## How to query status of triggered pipeline
{: #devsecops-query-pipeline-status}

### API curl call
{: #devsecops-api-call}

```sh
 while [ "$STATE" = "running" -o "$STATE" = "waiting" -o "$STATE" = "queued" -o "$STATE" = "pending" ]; do
            PIPELINE_STATUS=$(curl -s -k -X GET \
            --header "Authorization: Bearer $IAM_ACCESS_TOKEN" \
            --header "Accept: application/json" \
            --header "Content-Type: application/json" \
            $CD_PIPELINE_RUN_URL)
            STATE=$(echo $PIPELINE_STATUS | jq -r '.status .state')
            echo $STATE
            sleep 10
 done
```
{: codeblock}

### ibmcloud cli call
{: #devsecops-query-cli-call}

```bash
ibmcloud dev tekton-pipelinerun [pipelineID] --run-id [pipelinerunID] [--output JSON]
ibmcloud dev tekton-pipelinerun ls [pipelineID]
```
{: codeblock}

For more information, see the [{{site.data.keyword.cloud_notm}} CLI (`tekton-pipelinerun`) command](/docs/cli?topic=cli-idt-cli#tekton-pipelinerun).

For more information about triggering pipelines using the CLI, see [Using triggers](/docs/ContinuousDelivery?topic=ContinuousDelivery-tekton-pipelines&interface=api#using-triggers)

## Related information
{: #devsecops-async-sub-related}

For more information, see the following topics:
- [Updating the async stage webhooks](/docs/devsecops?topic=devsecops-update-async-webhook)
- [Async, parallel stage runs](/docs/devsecops?topic=devsecops-devsecops-async-stages)

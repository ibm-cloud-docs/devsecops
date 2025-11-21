---

copyright: 
  years: 2025, 2025
lastupdated: "2025-11-21"

keywords: DevSecOps, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Simple Automation Pipeline
{: #cd-devsecops-simple-pipeline}

This new listener is designed specifically for simple automation scenarios such as running smoke tests, regression suites, or one-off scripts without triggering unnecessary devsecops tasks like evidence, inventory, issue processing. It supports a minimal configuration with a single run task and step, allowing teams to define their automation workflows directly in the pipeline configuration file.

By adopting this new listener, teams can now integrate their automation tasks more cleanly and efficiently while reducing the load on shared CI/CD infrastructure.

{: shortdesc}

Please note that Simple Automation Pipeline support is available only in versions 11.16.0 and above, 10.48.0 and above, and 9.65.0 and above.
{: important}

## Configuring the Simple Automation Pipeline
{: #cd-devsecops-configuring-simple-pipeline}

At a minimum, the Simple Automation Pipeline requires the following three Toolchain Integrations:

1. Delivery Pipeline Integration
This integration enables users to execute Tekton-based pipelines by providing the necessary runtime interface. Support for the Simple Automation Pipeline is provided through a new Tekton listener. Users have the flexibility to either add this new listener to their existing Delivery Pipeline Integration within their toolchains or create a separate Delivery Pipeline Integration specifically for it. 

2. Git Integration for Pipeline Configuration Repository
This refers to the Git repository that contains the `pipeline-config.yaml` file used to configure the pipeline execution.

3. Git Integration for Pipeline Definition Repository
This repository holds the Tekton pipeline definitions. It is typically maintained by the DevSecOps team and includes the latest released versions of the tekton based pipeline definitions.

### Configuring the Simple Automation Pipeline as Manual Trigger

Use this mode when you need to run the pipeline as an ad-hoc job or task that involves manual initiation or intervention. To execute the Simple Automation Pipeline as a manual pipeline, follow these steps:

1. Add a **Manual** trigger and provide a name for the trigger.

2. Choose the EventListener named **simple-listener**.

When triggered manually, the pipeline makes the following variables available to the stages:

| Variable     | Description                          |
|--------------|--------------------------------------|
| `branch`     | The branch that was selected for the run. |
| `commit-sha` | The commit ID associated with the branch. |

### Configuring the Simple Automation Pipeline as Git Trigger
{: #cd-devsecops-git-trigger}

Git triggers are used to run the Simple Automation Pipeline in response to events from a source code management (SCM) system such as GitHub. This mode is useful for automating workflow whenever pull requests or commits are pushed.

1. Add a **Git Repository** trigger and provide a name for the trigger.

2. Choose the EventListener named **simple-listener**.

3. Choose the Git events based on your requirement.

Depending on the Git event selected, the following variables are passed to the stages:

- [ ] When a pull request is opened or updated
- [ ] When a pull request is closed

| Variable      | Description                                   |
|---------------|-----------------------------------------------|
| `head-branch` | Branch name of the PR source (head).          |
| `head-sha`    | Commit SHA of the PR source branch.           |
| `head-repo`   | URL of the repository where the source branch resides. |
| `base-branch` | Branch name of the PR target (base).          |
| `base-repo`   | URL of the repository where the base branch resides. |
| `base-sha`    | Commit SHA of the PR base branch.             |
| `pr-url`      | API URL of the pull request.                  |

- [ ] When a commit is pushed:

| Variable     | Description                          |
|--------------|--------------------------------------|
| `branch`     | The branch where the commit occurred. |
| `commit-sha` | Commit ID of the change.             |


Only selected variables from the PR or commit payload are injected into the pipeline stages.
If you need to access additional payload content, you can use the pr-url value to fetch the complete PR payload with curl, and then parse out the required fields. For example:
```
PR_BODY=$(curl -s -u "$GHE_USER:$GHE_TOKEN" "$PR_URL" | jq .body)
```
You can then extract any other values from PR_BODY as needed.


### Configuring the Simple Automation Pipeline as Timed Trigger

Timed triggers are ideal for scheduling pipelines to run automatically at specified intervals without manual intervention. This mode is useful for recurring automation tasks such as nightly test runs, periodic scans, or regular maintenance jobs. To execute the Simple Automation Pipeline as a scheduled pipeline, follow these steps:

1. Add a **Timed** trigger and assign it a suitable name.

2. Select the EventListener named **simple-listener**.

3. Enter a valid **CRON Expression** to define the schedule. For example, to run the job every weekday at 10 AM, use 0 10 * * 1-5.

4. Choose the appropriate **Timezone** for the schedule.


### Configuring the Simple Automation Pipeline as Generic Webhook Trigger

Webhook triggers are used to automatically run the Simple Automation Pipeline in response to events from an external system. This mode is ideal for integrating the pipeline into your automation workflows, ensuring automation tasks run immediately when specific conditions are met. To execute the Simple Automation Pipeline from another pipeline or process, follow these steps;

1. Add a **Generic Webhook** trigger and give it a meaningful name.

2. Choose the **EventListener** named **simple-listener**.

3. Select an appropriate **Securing** mode to validate the identity of incoming web requests.

4. Enter a value for the **Secret**, which is required to be included in the HTTP request sent to the trigger.

5. Choose the correct **Token Source** that specifies where to extract the token from in the incoming request (e.g., header, query, or payload).

6. Define a **Key Name** that corresponds to the field holding the token in the selected token source.

7. Copy the generated **Webhook URL**. You can use this URL with any HTTP client (e.g., curl) to trigger the pipeline by including the appropriate token in the request.

Example curl command to trigger the Simple Automation Pipeline configured as Generic Webhook:

```
curl -X POST \
  https://devops-api.<region>.devops.cloud.ibm.com/v1/tekton-webhook/<toolchain-id>/run/<pipeline-id> \
  -H 'Content-Type: application/json' \
  -H 'token: <token-value>' \
  -d '{
    "somekey": "somevalue"
  }'
```

## Environment Properties
{: #cd-devsecops-environment-properties-simple-pipeline}

The following environment properties must be set in the Delivery Pipeline to ensure successful execution:

| **Name**               | **Type**         | **Description**                                                                            |**Required or Optional**|
|------------------------|------------------|--------------------------------------------------------------------------------------------|------------------------|
| `pipeline-config-repo` | Text value       | Git repository URL where the pipeline configuration YAML and pipeline scripts are located. |Required                |
| `pipeline-config`      | Text value       | Name of the pipeline configuration YAML file.                                              |Required                |
| `git-token`            | Secure value     | GIT Personal Access Token for pipeline to access Pipeline Configuration Repository.        |Required                |
| `ibmcloud-api-key`     | Secure value     | IBM Cloud API key used for authentication and secure access to IBM Cloud services.         |Required                |
| `pipeline-debug`       | Enumeration      | Controls debug logging. Set to `1` to enable debug mode or `0` to disable it.              |Optional                |

## Configuring pipeline configuration for the Simple Automation Pipeline 

The Pipeline Configuration YAML file allows users to customize how the pipeline is executed. This file, referenced by the **pipeline-config** environment property, must reside in the Git repository specified by the **pipeline-config-repo** environment property.

Users can trigger custom scripts by referencing them directly within the pipeline configuration file. For the Simple Automation Pipeline, it is mandatory to define the execute task within this YAML configuration. Users can implement the pipeline configuration for both v10 and v11 versions of the tekton compliance pipeline definitions. 


### Example Pipeline Configuration for v10

Below is a sample `pipeline-config.yaml` for a Simple Automation Pipeline using **v10**:

- The **execute** task **must** be defined.
- Use the **image** field to specify the container image for running the script.
- The **script** field lists the shell commands to execute inside the container.
    - The script sources **dummy_script.sh** from the configured pipeline configuration repository.

```yaml
version: '1'

execute:
  image: icr.io/continuous-delivery/pipeline/pipeline-base-ubi:3.61
  script: |
    #!/usr/bin/env bash
    echo "Hello World!"
    
    cat $TRIGGER_PAYLOAD_PATH
    source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/dummy_script.sh
```

### Example Pipeline Configuration for v11

Below is a sample `pipeline-config.yaml` for a Simple Automation Pipeline using **v11**:

- The **simple-execute** task **must** be defined.
- The **execute** step is **required** within the task.
- Use the **image** field to specify the container image for running the script.
- The **script** field lists the shell commands to execute inside the container.
    - The script sources **dummy_script.sh** from the configured pipeline configuration repository.

```yaml
version: '2'

tasks:
  simple-execute:
    steps:
      - name: execute
        image: icr.io/continuous-delivery/base-images/base:v1.19.0
        script: |
          echo "Hello World!"
          
          cat $TRIGGER_PAYLOAD_PATH
          source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/dummy_script.sh
```

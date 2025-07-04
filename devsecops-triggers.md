---

copyright: 
  years: 2021, 2025
lastupdated: "2025-07-04"

keywords: DevSecOps, triggers

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Triggers
{: #cd-devsecops-triggers}

DevSecOps continuous integration and continuous deployment pipelines provide a set of default triggers.
{: shortdesc}

|Pipeline |Name	|Description |State |
|:----------|:------------------------------|:------------------|:----------|
|Pull request		|Git PR Trigger		|Runs the pull request pipeline on pull requests within the Application Repository.			|Enabled			|
|Pull request 		|Manual Trigger 		|Runs the pull request pipeline on a specified commit or branch, on demand.			|Enabled			|
|Promotion 		|Git Promotion Validation Trigger 		|Runs the promotion validation pipeline when a pull request is opened/updated on the `target-environment` branch of the Inventory.	|Disabled			|
|Continuous integration		|Git CI Timed Trigger		|Runs the continuous integration pipeline on the `master` branch of the Application Repository every day at 4 AM. |Disabled			|
|Continuous integration		|Git CI Trigger		|Runs the continuous integration pipeline when a commit is pushed to the master branch of the Application Repository.	|Enabled			|
|Continuous integration		|Manual Trigger 		|Runs the continuous integration pipeline on a specified commit or branch, on demand.		|Enabled			|
|continuous deployment		|Git CD Timed Trigger		|Runs the continuous deployment pipeline every day at 4 AM.	|Disabled			|
|continuous deployment		|Git CD Trigger		|Runs the continuous deployment pipeline when a commit is pushed to the `target-environment` branch of the Inventory.	|Disabled			|
|continuous deployment		|Manual CD Trigger		|Runs the continuous deployment pipeline on the `target-environment` branch of the Inventory, on demand.	|Enabled			|
|continuous deployment		|Manual Promotion Trigger		|Runs the promotion pipeline to promote inventory changes from the `source-environment` branch to the `target-environment` branch.	|Enabled			|
|Continuous compliance		|Git CC Timed Trigger		|Runs the continuous compliance pipeline every day at 4 AM.	|Enabled			|
|Continuous compliance		|Manual CC Trigger		|Runs the continuous compliance pipeline on the `environment-tag` tag of the Inventory, on demand.	|Enabled			|
|Continuous integration, continuous deployment, continuous compliance		|Evidence Pruner Timed Trigger		|Runs the pipeline every day at 2AM to clean up all the old evidence generated before `evidence-retention-days` days.	|Disabled by default.			|
|Continuous integration, continuous deployment, continuous compliance		|Evidence Pruner Manual Trigger 	|Runs the pipeline to clean up all the old evidence generated before `evidence-retention-days` days. Runs on demand.	|Enabled			|
{: caption="Triggers" caption-side="top"}

You can [add triggers](/docs/devsecops?topic=devsecops-cd-devsecops-add-pipeline-triggers) to the set of default triggers.
{: tip}

## Using Triggers
{: #cd-devsecops-using-triggers}

You can trigger pipelines using either the web UI or a command-line/API call. This is commonly used for pipelines such as [CI](/docs/devsecops?topic=devsecops-cd-devsecops-ci-pipeline), [CD](/docs/devsecops?topic=devsecops-cd-devsecops-cd-pipeline), or [CC](/docs/devsecops?topic=devsecops-devsecops-cc-pipeline) pipelines.

### 1. Triggering the Pipeline from the UI
{: #trigger-pipeline-UI}


For example, to trigger the Continuous Compliance (CC) pipeline manually:

1. Go to Your Toolchain  
   Navigate to your toolchain and select your existing toolchain from the list.

2. Create a Toolchain (if needed)  
   If you don't have a toolchain yet, create one using [this link](https://cloud.ibm.com/automation/toolchains).

3. Trigger the Pipeline  
   Inside the toolchain, click on the pipeline component and select Run to manually start the pipeline.


4. Click 'Run' Again to Confirm  
   A dialog or confirmation screen may appear—check the required environment properties, make any necessary changes, and then click Run again to start the pipeline.


5. Monitor Pipeline Execution  
   After triggering, you can see the CC pipeline being triggered and its stages executing on the UI. The logs and status of each stage will be visible as the pipeline progresses.

### 2. Triggering the Pipeline via Command Line or API
{: #trigger-pipeline-Command}

You can trigger a pipeline from the command line using either the IBM Cloud CLI or by making a direct API call using `curl`.

#### 2.1. Using IBM Cloud CLI
{: #trigger-pipeline-CLI}

You can trigger the pipeline using the IBM Cloud CLI. For more information about CLI commands, refer to the [IBM Cloud CLI docs](https://cloud.ibm.com/docs/cli).

1. Log in to IBM Cloud
   ```bash
   ibmcloud login --sso
   ```
2. Trigger the pipeline manually
   ```bash
   ibmcloud dev tekton-trigger run <pipeline_id> --trigger-name "<trigger_name>"
   ```

After replacing the `pipeline_id` and `trigger_name` in the command, run it to start the pipeline. If successful, the output will indicate that the pipeline has started and display details similar to the following:

Pipeline started successfully.

- Run ID : ruekhg-eifjkvr-kjf-rkvj
- Trigger : CC Manual Trigger
- Status : pending

You can then verify that your pipeline has been triggered by opening your respective toolchain in the UI.

#### 2.2. Using `curl` to Trigger the Pipeline via API
{: #trigger-pipeline-API}

You can trigger the pipeline directly via API using `curl`. For more information on how to trigger Tekton pipelines via API, see the [IBM Cloud API Docs/
CD Tekton Pipeline](https://cloud.ibm.com/apidocs/tekton-pipeline#create-tekton-pipeline-run).


1. Log in to IBM Cloud
   ```bash
   ibmcloud login --sso
2. Authentication Process
- If you don't already have an API Key, go to [IBM Cloud API Keys](https://cloud.ibm.com/iam/apikeys) to generate one.

- Once you have your API key, generate an IAM token using the following `curl` command:

  ```bash
    curl -X POST 'https://iam.cloud.ibm.com/identity/token' \
      -H 'Content-Type:application/x-www-form-urlencoded' \
      -H 'Accept:application/json' \
      -d 'grant_type=urn:ibm:params:oauth:grant-type:apikey&apikey=<API_KEY>'
- Replace <API_KEY> with your generated API Key.
- Once you have the IAM token, use it to authenticate the following request to access your pipeline:
    ```bash
    curl -L --request GET 'https://api.us-south.devops.cloud.ibm.com/pipeline/v2/tekton_pipelines/<PIPELINE_ID>' \
  --header 'Authorization: Bearer <IAM_TOKEN_VALUE>'
    ```

- After authenticating, trigger the pipeline using the following `curl` command:

    ```bash
    curl -i -X POST \
        -H "Authorization: Bearer <IAM_token>" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        --data '{
            "trigger": {
                "name": "CC Manual Trigger",
                "properties": {
                    "pipeline-debug": "false"
                }
            }
        }' \
        "https://api.us-south.devops.cloud.ibm.com/pipeline/v2/tekton_pipelines/<pipeline_id>/pipeline_runs"
    ```

- The `-i` option ensures that HTTP response headers are included in the output, which helps you see the HTTP status code in case of errors.
- You can add any environment properties required for the pipeline inside the `--data` payload, To learn more about how to add properties refer to this [IBM Cloud API Docs/
CD Tekton Pipeline](https://cloud.ibm.com/apidocs/tekton-pipeline#create-tekton-pipeline-run).

- The output looks similar to the following when the pipeline is triggered:

   - The HTTP response will show a `201 Created` status, confirming that the pipeline trigger request was accepted.
   - A `location` header will point to the URL of the triggered pipeline run.
   - You'll see metadata such as:
      - `pipeline_id`
      - `status` (e.g., `"pending"`)
      - `trigger` name (e.g., `"Manual CD Trigger"`)
      - `run_id` (unique ID for the triggered run)
      - A full URL to view the run in the UI.

   - This confirms that the pipeline was successfully triggered. You can now navigate to your toolchain's pipeline section in the IBM Cloud UI to view its status and logs.
   
- When the command runs successfully, the pipeline is triggered. Once triggered, the response includes details of the pipeline run.

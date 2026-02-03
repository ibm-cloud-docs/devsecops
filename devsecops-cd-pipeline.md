---

copyright:
  years: 2021, 2026
lastupdated: "2026-02-03"

keywords: DevSecOps, IBM Cloud, deployment delta

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Continuous deployment pipeline
{: #cd-devsecops-cd-pipeline-overview}

The continuous deployment pipeline generates all of the evidence and change request summary content. The pipeline deploys the build artifacts to an environment, such as staging or production, and then collects, creates, and uploads all existing log files, evidence, and artifacts to the evidence locker.
{: shortdesc}

## Stages and tasks
{: #cd-devsecops-pipeline-stages}

The following table lists the tasks run in a CD Pipeline. In addition the table also provides an overview of each of these stages:

- **Task or Stage**: This refers to the name of the stage as defined within the `.pipeline-config.yaml` configuration file.

- **Short description**: This provides a concise explanation of the actions performed during the execution of the stage.

- **Customisation permissible**: This indicates whether users have the flexibility to modify or replace the default behavior of the stage by inserting a custom script in the `.pipeline-config.yaml` file.

- **Default Reference Implementation**: This indicates whether the DevSecOps pipelines come with a pre-defined or default implementation for the stage. Notably, for certain stages like `unit-tests` or `setup`, the DevSecOps pipeline doesn't offer any out-of-the-box implementation. Instead, users are required to provide custom scripts or code tailored to their application's requirements.

- **Evidence Collection**: This indicates whether the stage performs the collection of standard evidence. When DevSecOps **Pipeline** provide a reference implementation for a stage, evidence collection is performed out-of-the-box. However, if **User** choose to modify or replace these predefined stages, they must ensure that their custom implementations include appropriate evidence collection. The same responsibility falls on users for stages where the DevSecOps pipeline doesn't provide an out-of-the-box implementation, necessitating them to perform evidence collection. The column indicates the entity (**User/Pipeline**) responsible for carrying out the evidence collection.

- **Skip permissible (applicable to version >= v10)**: This indicates whether users can opt out of running this stage by setting the skip property to true in the `.pipeline-config.yaml`. However, caution is advised when using this feature, especially for stages designed to collect evidence. Skipping such stages might lead to missing essential evidences for the build.

|Task or stage |Short description	|Customisation permissible in `.pipeline-config.yaml` | Default Reference Implementation |Evidence Collection |Skip permissible |
|:----------|:------------------------------|:------------------|:------------------|:------------------|:------------------|
|`start` 		|Set up the pipeline environment. 		|No		| Yes | NA | No |
|`setup`		|Set up your build and test environment.		|Yes			| No | NA | No |
|`verify-peer-review`		|Ensure that the pull requests intended for the current deployment have been approved. This stage will generate a list of pull requests linked to the ongoing deployment. If any pull requests remain unapproved, the deployment will be halted.	|Yes			| Yes | Pipeline | Yes |
|`verify-artifact`		|Validate the correct signing of the image scheduled for deployment. If the image lacks proper signature, the deployment will be obstructed, and the corresponding evidence collection process will be initiated.		|Yes			| Yes | Pipeline | Yes |
|`change-request`		|Generate the change request and create the evidence summary. 		|No		| Yes | Pipeline | No |
|`deployment`		|Deploy the build artifacts to the environment, such as staging or production.		|Yes		| No | NA | No |
|`acceptance-test` 		|Run acceptance and integration tests on the deployment.   	|Yes			| No | **User** | Yes |
|`finish` 		|Collect and upload log files, artifact, and evidence to the evidence locker. 		|Yes			| Yes | Pipeline | Yes |
|`rollback` |This is a step inside `prod-finish`, which gets executed whenever a rollback scenario is encountered | Yes | Yes | NA | No |
{: caption="Pipeline stages and tasks" caption-side="top"}

For more information about how to customize stages by using the `.pipeline-config.yaml` file, see [Custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize) and [Pipeline parameters](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm) lists.

## Deployment delta
{: #cd-devsecops-pipeline-delta}

When the inventory promotion is ready, the continuous deployment pipeline can start. The deployment delta is the difference between the contents of the last concluded deployment and the current deployment. The deployment delta lists the inventory items that are being deployed.

To access the deployment delta in your deployment scripts, you can use the following commands:

```bash
# return a JSON file path with an array of inventory entries that were changed
get_env DEPLOYMENT_DELTA_PATH

# return a JSON file path with an array of inventory entries that were deleted
get_env DEPLOYMENT_DELTA_DELETIONS_PATH

# returns a JSON file path with an array of all inventory entries
get_env INVENTORY_ENTRIES_PATH

```

## Calculate deployment BOM
{: #cd-devsecops-pipeline-bom}

The deployment Bill of Material (BOM) represents all of the artifacts that are deployed as part of a single change request. After the deployment delta is calculated, the pipeline creates the deployment BOM based on these items.

## Collect evidence summary
{: #cd-devsecops-pipeline-collect}

An evidence summary is created from all of the evidence that was created during the relevant build that led to a deployment. Evidence that is created during the deployment itself is also added to the summary. The evidence summary is added to the change request's description.
By default, the evidence summary comprises build-time evidence gathered in the CI pipeline along with evidences gathered during the current CD pipeline execution.

The following instructions apply exclusively to CD Pipelines targeting production environments. Use the target-environment-purpose flag to determine whether the environment is designated as either `pre_prod` or `production`.
{: important}


**Prerequisite**: In the CD pipeline targeting the pre_prod environment, set the pre-prod-evidence-collection flag to 1. This enables the collection and storage of build-time evidence in the evidence locker for all the assets deployed by the CD Pipeline, performing deployment on the pre-prod environment. By default, for environments designated as pre_prod, the pre-prod-evidence-collection flag is set to 1.

In the CD pipeline targeting the production environment, set the pre-`prod-evidence-collection` flag to `1`. This enables the CD Pipeline targeting the production environment to retrieve evidence gathered during the deployment to the pre-prod environment. By default, for environments designated as production, the `pre-prod-evidence-collection` flag is set to `0`.

Once the `pre-prod-evidence-collection` flag is set to `1`, Change Requests generated by the CD Pipeline targeted at production environment will include references to the Change Request IDs from pre-production environments within the **Validation Record** field.

For more information, see [Evidence summary](/docs/devsecops?topic=devsecops-devsecops-evidence-summary#evidence-v2-summary).

## Prepare and create change request
{: #cd-devsecops-pipeline-prepcr}

Everything that changes the [baseline](/docs/devsecops?topic=devsecops-cd-devsecops-inventory) must be traced by way of a change request. These changes include updates to the existing code level, changes to the configuration, and updates of the worker nodes. Collection of [peer review compliance](/docs/devsecops?topic=devsecops-cd-devsecops-peer-review) data is based on the data that is accessible in the inventory, the evidence locker, and the incident issue repo.

Use`get_env CHANGE_REQUEST_ID` to leverage the value of Change Request ID in the subsequent stages.

 Do not change the value of the variable that uses `set_env` as this is intended for internal implementation only.
 {: important}

This step creates the change request by attaching the available compliance data based on the [promotion pull request](/docs/devsecops?topic=devsecops-cd-devsecops-promotion-pipeline#cd-devsecops-promotion-pipelinepr) fields. [Deployment readiness](/docs/devsecops?topic=devsecops-cd-devsecops-automate-changemgmt#cd-devsecops-cr-approve) is calculated based on the available evidence in the collected compliance status.

If the pre-prod evidences are captured in the production deployment, pre-prod change requests are linked to the production change request. For more information, see [Data included in change requests](/docs/devsecops?topic=devsecops-cd-devsecops-cr-data).

Multiple change requests can pre-created using a dedicated Change-Request Listener for different [deployment configurations](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm#pipeline-parm-deployment-config) including deployments to multiple regions, targets and clusters. These pre-created change requests, after approval, can be passed down to deployment pipeline just-in-time which leverages precomputed information to speed up the actual deployment.

## Check change request approval
{: #cd-devsecops-pipeline-checkcr}

If every compliance check is successful, such as unit tests, Code Risk Analyzer tasks, branch protection, and detect secrets, the change request is approved automatically and the task runs successfully. For more information, see [Automating change management](/docs/devsecops?topic=devsecops-cd-devsecops-automate-changemgmt).

If a compliance check fails, the change request state is not approved. You can [approve the change request manually](/docs/devsecops?topic=devsecops-cd-devsecops-approve-cr) and add the `change-request-id` to the environment properties to use the previously created change request in the next run. You can also approve the change request manually and add an emergency label.

## Deployment
{: #cd-devsecops-pipeline-deployment}

In the deployment stage, the pipeline deploys the built artifacts into an environment, such as staging or prod. The variables and credentials for these stages can be found in the following sources:

* Variables from the pipeline UI (`get_env`)

 ![Edit property](images/target-environment.png){: caption="Edit property" caption-side="bottom"}

* [Pipeline trigger webhook](/docs/devsecops?topic=devsecops-cd-devsecops-webhook-payloads)

For more information about how to access these variables, see [Custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize).

## Acceptance test
{: #cd-devsecops-pipeline-acctest}

You can run a set of automated tests to validate that the deployment was successful and is working as expected. For traceability purposes, make sure that the test log contains a reference to the code level or image that is being tested.

## Change request close
{: #cd-devsecops-pipeline-closecr}

The details for the deployment are uploaded to the closing summary change task and then the task closes the change request. The `close_category` is added to the close change request task, with the following values:

* Successful (if was deployed ready, and CD deployment succeeded)
* Successful with issues (if the summary has issues, it was not deploy ready and CD deployment happened with emergency)

## Inventory conclude
{: #cd-devsecops-pipeline-inventory}

For more information about inventory conclude, see [Inventory](/docs/devsecops?topic=devsecops-cd-devsecops-inventory).

## Redeploy an application

{: #cd-devsecops-manual-redeployment}

### Overview

If an application crashes or behaves unexpectedly after infrastructure changes, you can force a redeployment to resolve the issue.

Use `force-redeploy=true` when triggering a **Manual CD** pipeline run to override the default change-detection behavior. This setting instructs the pipeline to redeploy the full inventory, even when no new changes are detected.

#### Default behavior (without force redeploy)

When `force-redeploy` is not set or is set to `false`, the pipeline deploys only when new changes are detected in the inventory repository for the target environment.

1. The CD pipeline starts and tags the current commit with the pipeline run ID.
2. The pipeline reads the content of the corresponding environment branch from that tag.
3. The pipeline calculates the deployment delta between the current commit and the commit associated with the `<target-environment>_latest` tag.
4. The pipeline evaluates the delta:

   * If the delta is empty, the pipeline stops and no deployment occurs.
   * If the delta contains changes, the pipeline continues.
5. The pipeline deploys the changes identified in the delta.
6. After a successful deployment, the pipeline attaches the `<target-environment>_latest` tag to the new commit.

#### Forced behavior (with force redeploy)

When `force-redeploy` is set to `true`, the pipeline bypasses delta validation and redeploys the complete inventory, regardless of detected changes. This approach ensures that the full application state is reapplied to the deployment target.

1. The CD pipeline starts and tags the current commit with the pipeline run ID.
2. The pipeline reads the content of the corresponding environment branch from that tag.
3. The pipeline calculates the deployment delta, which is typically empty in this scenario.
4. The `force-redeploy=true` setting causes the pipeline to skip the delta check and proceed.
5. The pipeline redeploys the entire application state from the branch.
6. After a successful redeployment, the pipeline attaches the `<target-environment>_latest` tag to the commit it just deployed.

### Procedure

1. Start a new **Manual CD** pipeline run.

2. In the pipeline's configuration, set the `force-redeploy` environment variable to `true`.

3. Run the pipeline.

## Rollback a deployment
{: #rollback-a-deployment}

### Overview

A rollback reverses a previous deployment and restores a known application state when a deployment causes instability, failures, or compliance issues. Rollbacks are typically performed to recover service reliability, enforce configuration consistency, or maintain regulatory alignment.

Three rollback approaches are available, each suited to different recovery scenarios:

* **Full Rollback**: Restores a last known good configuration by referencing a ServiceNow Change Request ID. Recommended for controlled and auditable recovery.
* **Full Rollback using GitOps**: Restores a previous state by manually reverting commits in the inventory repository. Not recommended due to limited compliance handling.
* **Inline Rollback**: Reverts a failed deployment within the same pipeline run when execution errors occur.

### Full Rollback

{: #cd-devsecops-full-rollback}

#### Overview of the Rollback Pipeline
This pipeline allows you to roll back to a previous, known-good configuration by targeting a specific ServiceNow Change Request ID (for example, `CHG-12345`).

This Change Request ID serves as a unique bookmark for a successful deployment and is a required input for the rollback pipeline.

You can find the Change Request ID for a previous deployment in two ways:

* **ServiceNow**: Locate the change request for the deployment you want to restore.
* **Inventory Repository**: Because the inventory's state is managed in source control, each deployment is uniquely identified by a commit. You can find the commit you want to revert to and identify the `CHG-***` tag associated with that commit.

The rollback pipeline contains the following stages:

* `prod-rollback-start`
* `prod-setup`
* `prod-rollback-change-request`
* `prod-deployment`
* `prod-acceptance-tests`
* `prod-rollback-finish`

The pipeline run uses information from the `rollback-change-request-id` environment property to create a new change request.

To maintain compliance, the pipeline reopens issues linked to the previous deployment. These issues are attached to the new change request, and their original due dates remain unchanged. A successful rollback moves the `_latest` tag in the inventory to the previous commit.

#### Create a Rollback Pipeline

Use the `cd-rollback-listener` to trigger a rollback to the last known good version.

1. Go to your CD pipeline.
2. Add a manual trigger or duplicate the **Manual CD Trigger**.
3. Edit the trigger and set the listener to `cd-rollback-listener`.
4. Select **Save**.
5. Create a separate trigger for each required region and target environment combination.

#### Trigger a Rollback Pipeline

A rollback pipeline run uses the following environment properties:

| Environment property                                                         | Description                                                                                            |
| :--------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------- |
| `rollback-change-request-id`                                                 | (Required) The change request ID of the concluded deployment that you want to roll back.               |
| `rollback-limit`                                                             | The maximum number of deployments you can roll back. The default is 1 (the last completed deployment). |
| `region`                                                                     | The region for the rollback.                                                                           |
| `target-environment`                                                         | The target environment for the rollback (for example, stage or prod).                                  |
| {: caption="Table 1. Rollback environment properties" caption-side="bottom"} |                                                                                                        |

The pipeline terminates unless the following criteria are met:

* `rollback-change-request-id` must be the ID of a concluded deployment for the same region and target-environment.
* The deployment associated with `rollback-change-request-id` must not be older than the number of deployments specified by rollback-limit.
  {: important}

The `PIPELINE_NAME` Tekton environment property determines if a run is a deployment or a rollback.

* `cd-rollback-pipeline`: Default value for a rollback.
* `cd-pipeline`: Default value for a deployment.

You can use this property to customize branching logic.


### Full Rollback using GitOps

{: #cd-devsecops-gitops-raw}

#### Overview of Rollback using GitOps

Use the continuous deployment pipeline to deploy a previous version of the inventory to the target environment by using Git operations that revert changes to a specific `commit-id`.
{: shortdesc}

Because you are reverting commits in the inventory repository directly instead of triggering the rollback pipeline with a ServiceNow Change Request ID, this process does not automatically reopen previous compliance issues or manage their due dates.
{: note}

When triggered, the CD pipeline completes the following steps for the redeployment:

* The pipeline starts and tags the current commit (the revert commit) with the pipeline run ID.
* The pipeline reads the content of the corresponding environment branch from that tag.
* The pipeline calculates the deployment delta between the current commit and the commit associated with the `<target-environment>_latest` tag.
* After a successful deployment, the `<target-environment>_latest` tag is moved to the new (reverted) commit.

#### Create a Rollback Promotion Pull Request

1. Identify the `commit-id` of the deployment version in the inventory that you want to roll back to.
2. Revert the repository state to that commit.
3. Create a pull request to promote the reverted state.

The following example shows this scenario by using `git` commands.

1. List commits and tags to find the commit ID of the last known good state (for example, `refs/tags/8`)

   ```bash
   # /c/usr/devsecops/compliance-inventory (master)
   $ git show-ref --tags
   ...
   83f7a87ee59185eaeac554bd3abeebfd2c1b4ad8 refs/tags/8
   ...
   1914a125e76aa97c497f4bd2c2f455b58cf079b8 refs/tags/prod_latest
   ```

   {: codeblock}

2. List all commits between the current state (`refs/tags/prod_latest`) and the target state (`refs/tags/8`).

   ```bash
   # /c/usr/devsecops/compliance-inventory (master)
   $ git rev-list --no-merges HEAD...83f7a87ee59185eaeac554bd3abeebfd2c1b4ad8
   67cc8babdff3e09c1f0e632f897798c1b5424f38
   6fab5ce3d60590cd858206424ecfd7d3a8c9ceb4
   ...
   ```

   {: codeblock}

3. Revert the inventory state to the target commit (`refs/tags/8`).

   ```bash
   # /c/usr/devsecops/compliance-inventory (master)
   $ git revert -n $(git rev-list --no-merges HEAD...83f7a87ee59185eaeac554bd3abeebfd2c1b4ad8)
   ```

   {: codeblock}

4. Commit the new state.

   ```bash
   # /c/usr/devsecops/compliance-inventory (master|REVERTING)
   $ git commit -m "revert master to 83f7a87ee59185eaeac554bd3abeebfd2c1b4ad8"
   [master af82538] revert master to 83f7a87ee59185eaeac554bd3abeebfd2c1b4ad8
   ```

   {: codeblock}

5. Push the update to the master branch.

   ```bash
   # /c/usr/devsecops/compliance-inventory (master)
   $ git push --set-upstream origin master
   ...
   To [https://us-south.git.cloud.ibm.com/jaunin.b/compliance-inventory.git](https://us-south.git.cloud.ibm.com/jaunin.b/compliance-inventory.git)
     67cc8ba..af82538  master -> master
   ...
   ```

   {: codeblock}


#### Trigger the Continuous Deployment Pipeline

1. Create a pull request for the rollback promotion changes.

2. Review and merge the pull request.

3. Trigger the **Manual CD** pipeline run in your CD Toolchain.


### Inline Rollback

{: #cd-devsecops-inline-rollback}

#### Overview of Inline Rollback

This mode rolls back the current deployment within the context of the same pipeline run. It is required when an error or failure occurs during the Deployment or the Acceptance Test stage, causing the deployment attempt to be marked as a failure.

An inline rollback automatically runs if the `rollback-enabled` environment property is set to `1` and a failure occurs in the Deployment or Acceptance test stage.

If a rollback is triggered, the CD pipeline runs the segment defined for `rollback` in your `.pipeline-config.yaml` file. If a `rollback` segment is not defined in the file, a default implementation runs and prompts you to provide a rollback script.

Example rollback script in `.pipeline-config.yaml`:

```bash
rollback:
  image: icr.io/continuous-delivery/pipeline/pipeline-base-image:2.74
  script: |
    #!/usr/bin/env bash
    if [[ "$PIPELINE_DEBUG" == 1 ]]; then
      trap env EXIT
      env
      set -x
    fi
    source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/deploy_setup.sh
    source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/rollback.sh
```

{: codeblock}

#### Properties set during Inline Rollback

{: #cd-devsecops-inline-rollback-properties}

* `rollback-status`: Indicates the status of the rollback step. Possible values: `[notRun, success, failure]`
* `rollback-exit-code`: The exit code of the rollback step. This value is empty if the rollback did not run.
* `default-rollback-executed`: Set to `true` if the default implementation (which prompts for a script) was run. This value is empty by default.
* `pipeline-execution-status`: Indicates the status of the overall pipeline run. Possible values: `[successful_deployment, failed_deployment_failed_rollback, failed_deployment_successful_rollback]`



## Enabling deployment traceability
{: #enable-traceability}

When a PR or merge request is merged, the system enhances transparency and traceability by providing clear visibility into what happens next. It automatically updates the PR with deployment status, allowing stakeholders to easily track the progress of a fix or feature without requiring access to pipeline details. This streamlined approach not only improves transparency but also facilitates traceability, reducing the effort that is needed to gather and verify information. After a successful deployment, a label in the format `deploy:{region}:{env}` is applied to the pull requests included in the deployment.

An opt-in flag `deployment-traceability`, is available to enable this feature in the CD pipeline. Users can activate deployment traceability by setting the flag to `1` when needed.

To further support this functionality, if the user wants to provide a specific token for adding labels to PRs in the app repository, they can use the following environment properties to specify the Git token. The order in which the Git token is looked up is as follows:

- An existing environment property for a repository-specific token: `git-token-$repo_name-$repo_org`.
- A new environment property for an organization-specific token: `git-token-$repo_org`.

# 

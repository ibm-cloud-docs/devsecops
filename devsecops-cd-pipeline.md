---

copyright: 
  years: 2021, 2024
lastupdated: "2024-01-12"

keywords: DevSecOps, IBM Cloud, deployment delta

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Continuous deployment pipeline
{: #cd-devsecops-cd-pipeline}

The continuous deployment pipeline generates all of the evidence and change request summary content. The pipeline deploys the build artifacts to an environment, such as staging or production, and then collects, creates, and uploads all existing log files, evidence, and artifacts to the evidence locker.
{: shortdesc}

## Stages and tasks
{: #cd-devsecops-pipeline-stages}

The table below lists the tasks run in a CD Pipeline. In addition the table also provides an overview of each of these stages:

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
|`verify-peer-review`		|Set up your build and test environment.		|Yes			| No | Pipeline | Yes |
|`verify-artifact`		|Set up your build and test environment.		|Yes			| No | Pipeline | Yes |
|`change-request`		|Generate the change request and create the evidence summary. 		|No		| Yes | Pipeline | No |
|`deployment`		|Deploy the build artifacts to the environment, such as staging or production.		|Yes		| No | NA | No |
|`acceptance-test` 		|Run acceptance and integration tests on the deployment.   	|Yes			| No | **User** | Yes | 
|`finish` 		|Collect and upload log files, artifact, and evidence to the evidence locker. 		|Yes			| Yes | Pipeline | Yes |
{: caption="Table 1. Pipeline stages and tasks" caption-side="top"}

For more information about how to customize stages by using the `.pipeline-config.yaml` file, see [Custom scripts](/docs/devsecops?topic=devsecops-custom-scripts) and [Pipeline parameters](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm) lists.

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

You can identify the type of environment as `pre-prod` or `production` based on whether the flag `target-environment-purpose` is set to `pre_prod` or `production`. Evidences are captured in the evidence summary of the production deployment when you set the `pre-prod-evidence-collection` to `1` in `production`, and if the collection was gathered during the pre-prod deployment.

For more information, see [Evidence summary](/docs/devsecops?topic=devsecops-devsecops-evidence-summary#evidence-v2-summary).

## Prepare and create change request
{: #cd-devsecops-pipeline-prepcr}

Everything that changes the [baseline](/docs/devsecops?topic=devsecops-cd-devsecops-inventory) must be traced by way of a change request. These changes include updates to the existing code level, changes to the configuration, and updates of the worker nodes. Collection of [peer review compliance](/docs/devsecops?topic=devsecops-cd-devsecops-peer-review) data is based on the data that is accessible in the inventory, the evidence locker, and the incident issue repo.

This step creates the change request by attaching the available compliance data based on the [promotion pull request](/docs/devsecops?topic=devsecops-cd-devsecops-promotion-pipeline#cd-devsecops-promotion-pipelinepr) fields. [Deployment readiness](/docs/devsecops?topic=devsecops-cd-devsecops-automate-changemgmt#cd-devsecops-cr-approve) is calculated based on the available evidence in the collected compliance status.

If the pre-prod evidences are captured in the production deployement, pre-prod change requests are linked to the production change request. For more information, see [Data included in change requests](/docs/devsecops?topic=devsecops-cd-devsecops-cr-data).

## Check change request approval
{: #cd-devsecops-pipeline-checkcr}

If every compliance check is successful, such as unit tests, Code Risk Analyzer tasks, branch protection, and detect secrets, the change request is approved automatically and the task runs successfully. For more information, see [Automating change management](/docs/devsecops?topic=devsecops-cd-devsecops-automate-changemgmt).

If a compliance check fails, the change request state is not approved. You can [approve the change request manually](/docs/devsecops?topic=devsecops-cd-devsecops-approve-cr) and add the `change-request-id` to the environment properties to use the previously created change request in the next run. You can also approve the change request manually and add an emergency label.

## Deployment
{: #cd-devsecops-pipeline-deployment}

In the deploy stage, the pipeline deploys the built artifacts into an environment, such as staging or prod. The variables and credentials for these stages can be found in the following sources:

* Variables from the pipeline UI (`get_env`)

 ![Edit property](images/target-environment.png){: caption="Figure 1. Edit property" caption-side="bottom"}

* [Pipeline trigger webhook](/docs/devsecops?topic=devsecops-cd-devsecops-webhook-payloads)

For more information about how to access these variables, see [Custom scripts](/docs/devsecops?topic=devsecops-custom-scripts).

## Acceptance test
{: #cd-devsecops-pipeline-acctest}

You can run a set of automated tests to validate that the deployment was successful and is working as expected. For traceability purposes, make sure that the test log contains a reference to the code level or image that is being tested.

## Change request close
{: #cd-devsecops-pipeline-closecr}

The details for the deployment are uploaded to the closing summary change task and then the task closes the change request. The `close_category` is added to the close change request task, with the following values:

* Successful
* Successful with issues (if the summary has issues)

## Inventory conclude
{: #cd-devsecops-pipeline-inventory}

For more information about inventory conclude, see [Inventory](/docs/devsecops?topic=devsecops-cd-devsecops-inventory).

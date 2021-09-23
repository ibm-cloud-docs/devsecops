---

copyright:
  years: 2019, 2021
lastupdated: "2021-03-31"

keywords: event, security, IBM, activity tracker, Continuous Delivery

subcollection: devsecops

---

{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:deprecated: .deprecated}
{:download: .download}
{:preview: .preview}

# Auditing events for {{site.data.keyword.contdelivery_short}}
{: #cd-at-events}

As a security officer, auditor, or manager, you can use the Activity Tracker service to track how users and applications interact with the {{site.data.keyword.contdelivery_full}} service and toolchain instances in {{site.data.keyword.cloud}}.
{: shortdesc}

{{site.data.keyword.at_full_notm}} records user-initiated activities that change the state of a service in {{site.data.keyword.cloud_notm}}. You can use this service to investigate abnormal activity and critical actions and to comply with regulatory audit requirements. In addition, you can be alerted about actions as they happen. The events that are collected comply with the Cloud Auditing Data Federation (CADF) standard. For more information, see the [getting started tutorial for {{site.data.keyword.at_full_notm}}](/docs/Activity-Tracker-with-LogDNA?topic=Activity-Tracker-with-LogDNA-getting-started).

{{site.data.keyword.contdelivery_short}} automatically generates events for specific actions and forwards these event logs to {{site.data.keyword.at_full_notm}}. To access these logs, you must [provision an instance of {{site.data.keyword.at_full_notm}}](/docs/Activity-Tracker-with-LogDNA?topic=Activity-Tracker-with-LogDNA-getting-started).


## Events for {{site.data.keyword.contdelivery_short}}
{: #cd-events}

The following table lists the actions that generate {{site.data.keyword.contdelivery_short}} data events:

| Action | Description | 
|:-----------------|:-----------------|
| `continuous-delivery.auth-user.create` | Manually or automatically add authorized users. For more information about automatically adding authorized users, see [How are users counted for instances of {{site.data.keyword.contdelivery_short}} in resource groups?](/docs/ContinuousDelivery?topic=ContinuousDelivery-limitations_usage#how-are-users-counted-for-instances-of-continuous-delivery-in-resource-groups-) |
| `continuous-delivery.auth-user.read` | View the list of authorized users from the Manage tab of a {{site.data.keyword.contdelivery_short}} service instance |
| `continuous-delivery.auth-user.delete` | Delete authorized users from the {{site.data.keyword.contdelivery_short}} service instance |
{: caption="Table 1. Actions that generate {{site.data.keyword.contdelivery_short}} data events" caption-side="top"}

## Events for toolchains
{: #toolchain-events}

The following table lists the actions that generate toolchain management events:

| Action | Description | 
|:-----------------|:-----------------|
| `toolchain.instance.create` | Create a toolchain | 
| `toolchain.instance.update` | Rename a toolchain. This event is not sent when tool integrations are added to (`toolchain.tool-instance.deploy`) or removed from (`toolchain.tool-instance.undeploy`) a toolchain. |
| `toolchain.instance.delete` | Delete a toolchain |
| `toolchain.tool-instance.deploy` | Add a tool integration to a toolchain |
| `toolchain.tool-instance.undeploy` | Remove a tool integration from a toolchain |
{: caption="Table 2. Actions that generate toolchain management events" caption-side="top"}

The following table lists the actions that generate toolchain data events:

| Action | Description | 
|:-----------------|:-----------------|
| `toolchain.tool-instance.create` | Add a tool integration to a toolchain. This event is always followed by the toolchain.tool-instance.deploy event. | 
| `toolchain.tool-instance.read` | View the configuration of a tool integration | 
| `toolchain.tool-instance.update` | Save configuration changes to a tool integration | 
| `toolchain.tool-instance.delete` | Remove a tool integration from a toolchain. This event is always preceded by the toolchain.tool-instance.undeploy event. |
{: caption="Table 3. Actions that generate toolchain data events" caption-side="top"}

## Events for {{site.data.keyword.DRA_short}}
{: #insights-events}

The following table lists the actions that generate {{site.data.keyword.DRA_short}} management events:

| Action | Description | 
|:-----------------|:-----------------|
| `toolchain.insights-tag.create` | Create a tag |
| `toolchain.insights-tag.read` | View a tag |
| `toolchain.insights-tag.update` | Update a tag |
| `toolchain.insights-tag.delete` | Delete a tag |
| `toolchain.insights-decision.evaluate` |Make a gate decision on a build  |
{: caption="Table 4. Actions that generate {{site.data.keyword.DRA_short}} management events" caption-side="top"}

The following table lists the actions that generate {{site.data.keyword.DRA_short}} data events:

| Action | Description | 
|:-----------------|:-----------------|
| `toolchain.insights.read` | View any build, deploy, or test record |
| `toolchain.insights.update` | Publish a new build, deploy, or test record. The event's metadata includes `toolchainId` (unique identifier for toolchain) and might also contain the `build_artifact` (name of application), `build_id` (id of build), `branch` (Git branch of build), `environment_name` (name of environment where the test ran), and `operationId` (indicates how the update was completed, such as `postBuild`, `postResults`, `postDeployment`, `postResultsById`, `postLifeCycleStage`, `postBuildArtifactMetaData`, `putLifeCycleStage`, `putLifeCycleStagesOrder`, and `resultsMultipart`) values. The values that are included in the event's metadata is determined by the type of record (build, deployment, or test) that is published.      |
| `toolchain.insights.policy.create` | Create a policy |
| `toolchain.insights.policy.read` | View a policy |
| `toolchain.insights.policy.update` | Update a policy. The event's metadata might include the `toolchainId` (unique identifier for toolchain) and `policyName` (name of policy that was updated) values. |
| `toolchain.insights.policy.delete` | Delete a policy |
| `toolchain.insights.data-toolchain.delete` | Delete data for a toolchain |
| `toolchain.insights.data-environment.delete` | Delete data for a specific environment |
| `toolchain.insights.data-application.delete` | Delete data for a specific application |
| `toolchain.insights.data-branch.delete` | Delete data for a specific application and branch |
{: caption="Table 5. Actions that generate {{site.data.keyword.DRA_short}} data events" caption-side="top"}

## Events for {{site.data.keyword.deliverypipeline}}
{: #pipeline-events}

The following table lists the actions that generate {{site.data.keyword.deliverypipeline}} management events:

| Action | Description | 
|:-----------------|:-----------------|
| `toolchain.pipeline.create` | Create a delivery pipeline or a Tekton pipeline for a toolchain |
| `toolchain.pipeline-run.create` | Trigger a delivery pipeline or a Tekton pipeline to run manually or by using Git or a timer |
{: caption="Table 6. Actions that generate {{site.data.keyword.deliverypipeline}} management events" caption-side="top"}

The following table lists the actions that generate {{site.data.keyword.deliverypipeline}} data events:

| Action | Description | 
|:-----------------|:-----------------|
| `toolchain.pipeline.read` | View the delivery pipeline or the Tekton pipeline from a  {{site.data.keyword.contdelivery_short}} service instance |
| `toolchain.pipeline.update` | Rename the delivery pipeline or a Tekton pipeline. Update pipeline properties. Add, edit, or delete stages. This event is triggered by a change to a tool integration configuration. Because the customer data that is included in a typical update might contain secret data, job scripts, and more, this event does not include the `InitialValue` and `newValue` values. |
| `toolchain.pipeline.delete` | Delete the delivery pipeline or Tekton pipeline from the {{site.data.keyword.contdelivery_short}} toolchain instance  |
| `toolchain.pipeline-run.read` | View the run logs for a delivery pipeline or a Tekton pipeline in the Tekton dashboard |
| `toolchain.pipeline-run.update` | Update the delivery pipeline when a job completes within a stage or a user cancels a stage. Because the customer data that is included in a typical update might contain secret data, log output, large data, and more, this event does not include the `InitialValue` and `newValue` values. |
| `toolchain.pipeline-run.delete` | Delete the delivery pipeline when a pipeline job run is deleted by the {{site.data.keyword.contdelivery_short}} service. The delivery pipeline retains a limited number of runs.  |
{: caption="Table 7. Actions that generate {{site.data.keyword.deliverypipeline}} data events" caption-side="top"}

## Events for Eclipse Orion {{site.data.keyword.webide}}
{: #webide-events}

The following table lists the actions that generate {{site.data.keyword.webide}} management events:

| Action | Description | 
|:-----------------|:-----------------|
| `toolchain.web-ide-workspace.create` | Create an Eclipse Orion {{site.data.keyword.webide}} workspace. The most common trigger for this event is the first time that a user opens the {{site.data.keyword.webide}} for a specific toolchain.|
{: caption="Table 8. Actions that generate {{site.data.keyword.webide}} management events" caption-side="top"}

The following table lists the actions that generate {{site.data.keyword.webide}} data events:

| Action | Description | 
|:-----------------|:-----------------|
| `toolchain.web-ide-workspace.read` | View a {{site.data.keyword.webide}} workspace. This event is sent the first time that a user views an existing {{site.data.keyword.webide}} workspace in a session. |
| `toolchain.web-ide-workspace.delete` | Delete a {{site.data.keyword.webide}} workspace |
| `toolchain.web-ide-file.create` | Create a file or folder in a {{site.data.keyword.webide}} workspace. If this event is triggered by copying a file or a folder, the **requestData** field contains the **trigger** field with a value of `File Copy` and the **source** field (the source path of the copied file).  |
| `toolchain.web-ide-file.read` | View the contents of a file in a {{site.data.keyword.webide}} workspace |
| `toolchain.web-ide-file.edit` | Change the contents of a file in a {{site.data.keyword.webide}} workspace. If this event is triggered by copying a file or folder, the **requestData** field contains the **trigger** field with a value of `File Copy` and the **source** field (the source path of the copied file). |
| `toolchain.web-ide-file.update` | Update the path of a file or folder in a {{site.data.keyword.webide}} workspace. The **requestData** field for this event contains the **updateType** field with a value of `Path Change`, the **initialValue** field (the original path of the file or folder), and the **newValue** field (the new path of the file or folder). |
| `toolchain.web-ide-file.delete` | Delete a file or folder from a {{site.data.keyword.webide}} workspace |
| `toolchain.web-ide-workspace.import` | Import a file into a {{site.data.keyword.webide}} workspace. The **requestData** field for this event contains the **extractArchive** field. The value of this field is a Boolean operator that indicates whether a server-side request was initiated to extract the content from the file's archived content.  |
{: caption="Table 9. Actions that generate {{site.data.keyword.webide}} data events" caption-side="top"}

## Viewing events
{: #cd_at_ui}

Events that are generated by an instance of the {{site.data.keyword.contdelivery_short}} service are automatically forwarded to the {{site.data.keyword.at_full_notm}} service instance that is available in the same location.

{{site.data.keyword.at_full_notm}} can have only one instance per location. To view events, you must access the web UI of the {{site.data.keyword.at_full_notm}} service in the same location where your service instance is available. For more information, see [Launching the web UI through the IBM Cloud UI](/docs/Activity-Tracker-with-LogDNA?topic=Activity-Tracker-with-LogDNA-launch).

## Analyzing events
{: #authorized-user-events}

When user emails are added to or removed from the authorized users list of a {{site.data.keyword.contdelivery_short}} service instance in a resource group, {{site.data.keyword.contdelivery_short}} sends events to {{site.data.keyword.at_full_notm}} when either of the following methods are used.

* An administrator manually adds or removes a user email from the **Manage** tab of the {{site.data.keyword.contdelivery_short}} service.
* The system automatically adds one or more user emails in response to user-initiated or service-initiated actions with {{site.data.keyword.contdelivery_short}} toolchains.

The following authorized user events are displayed in {{site.data.keyword.at_full_notm}}:

* `Continuous Delivery: create auth-user [service name] (auth-user(s): [EMAILS])`
* `Continuous Delivery: delete auth-user [service name] (auth-user(s): [EMAILS])`
* `Continuous Delivery: create auth-user [service name] (auth-user(s): [EMAILS]; root-action: [ACTION]; root-action-service-instance: [CRN])`
* `Continuous Delivery: delete auth-user [service name] (auth-user(s): [EMAILS]; root-action: [ACTION]; root-action-service-instance: [CRN])`

Where:

* `[EMAILS]` is a comma-separated list of the user email IDs that are added to or removed from the list of authorized users.
* `[ACTION]` identifies the action that prompted the automatic addition or removal of user emails from the list of authorized users. If a root-action is not specified, the addition or removal of the user emails was performed directly by a user from the **Manage** tab of the {{site.data.keyword.contdelivery_short}} service dashboard.
* `[CRN]` specifies the [Cloud Resource Name](/docs/account?topic=account-crn) of the resource to which the root-action was applied. This resource can either be a toolchain or the {{site.data.keyword.contdelivery_short}} service instance itself.

The following table lists and describes the root actions that prompt user emails to automatically be added to or removed from the authorized users list.

| Root action | Description | 
|:-----------------|:-----------------|
| `continuous-delivery.instance.create` | Add a user email when the {{site.data.keyword.contdelivery_short}} service is provisioned. This email belongs to the user who created the service instance. The `root-action-service-instance` CRN identifies the {{site.data.keyword.contdelivery_short}} service instance.   | 
| `continuous-delivery.instance.delete` | Remove user emails when the {{site.data.keyword.contdelivery_short}} service is de-provisioned. The `root-action-service-instance` CRN identifies the {{site.data.keyword.contdelivery_short}} service instance. |
| `toolchain.git-repos-and-issue-tracking-repo.evaluate` | Add user emails after system evaluation of a {{site.data.keyword.gitrepos}} repo. Multiple emails that correspond to users with Developer (or greater) access to the repo might be added. The `root-action-service-instance` CRN identifies the toolchain that contains the {{site.data.keyword.gitrepos}} tool integration. |
| `toolchain.pipeline-stage.start` | Add user emails when a delivery pipeline stage starts. If the pipeline stage is manually started, the email of the user who started the pipeline is added. If a pipeline stage is triggered by a change to a repository (repo) in the {{site.data.keyword.gitrepos}} tool integration, multiple emails that correspond to users with Developer (or greater) access to the repo might be added. The `root-action-service-instance` CRN identifies the toolchain that contains the pipeline. |
| `toolchain.pipeline.read` | Add a user email when the user views a delivery pipeline. The `root-action-service-instance` CRN identifies the toolchain that contains the pipeline.  |
| `toolchain.pipeline.update` | Add a user email when the user edits a delivery pipeline. The `root-action-service-instance` CRN identifies the toolchain that contains the pipeline.  |
| `toolchain.web-ide.read` | Add a user email when the user opens a workspace in the Eclipse Orion {{site.data.keyword.webide}}. The `root-action-service-instance` CRN identifies the toolchain that contains the {{site.data.keyword.webide}} workspace. |
{: caption="Table 10. Actions that add or remove user emails from the authorized users list" caption-side="top"}

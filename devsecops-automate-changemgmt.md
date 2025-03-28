---

copyright: 
  years: 2021, 2024
lastupdated: "2024-10-09"

keywords: DevSecOps, automate change management, change management, ibm cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Automating change management
{: #cd-devsecops-automate-changemgmt}

Change management automation is an important part of the DevSecOps pipeline reference implementation. Developers, approvers, and auditors can monitor the compliance aspects of deployments. Every deployment must follow an organization's change management policy.
{: shortdesc}

Change management automation can be visualized via the following flowchart. The following flowchart illustrates a standard change management automation, emergency change management, a manual change request flow and the change management automation flow when inline rollback is involved.



![Change management automation](images/chg-mgmt-devsecops.jpg){: caption="Change management automation" caption-side="bottom"}




## Before you begin
{: #cd-devsecops-change-prereq}

Be sure to become familiar with the process and terminology. For more information, see [Automated change management](/docs/devsecops?topic=devsecops-cd-devsecops-change-management).

## Creating change requests for deployments
{: #cd-devsecops-change-fields}
{: step}

Use the pull request template that is provided in the inventory for promotion pull requests to populate the change request fields. Because you cannot automatically populate these fields, you must manually populate them to promote changes. By manually populating these fields, you trigger the deployment and continue automatic data collection for the rest of the change request.

 ![Promotion pull request](images/promotion-pr.png){: caption="Promotion pull request" caption-side="bottom"}
 
The promotion pull request template contains the following fields:

* **Priority** Required. The priority of the change. Valid values are: `critical`, `high`, `moderate`, `low`, and `planning`.
* **Change Request assignee** Required. The email address of the person that the change request is assigned to.
* **Additional Description** Describes the change process. Extra content from the automation process is appended here.
* **Purpose/Goal** Describes the purpose of the change.
* **Explanation of Impact** Describes the possible impact of the change.
* **Customer Impact** Required. Describes the impact of the change for the customer. Valid values are: `critical`, `high`, `moderate`, `low`, `no_impact`
* **Deployment Impact** Required. Descrives the impact of the change upon deployment of the change. Valid values are: `small`, `large`
* **Backout Plan** Describes the rollback or backout plan.

Along with these fields, you must set two additional fields from the environment properties:

* `target-environment-purpose` (Required) Valid values are: `production`, `pre_prod`. Any non-production deployment would qualify as `pre_prod`.
* `target-environment-detail` (Required) A string which describes the `target-environment` where the change is deployed.

For more information on change request data, see [Data included in change requests](/docs/devsecops?topic=devsecops-cd-devsecops-cr-data).
 
### Types of change
{: #cd-devsecops-change-type}

Change Request Management supports two types of change: **emergency** or **regular**.

If the current change is an emergency change, add the `emergency` label to the promotion pull request.

## Creating change requests for changes that require planned downtime
{: #cd-devsecops-cr-approve}
{: step}

The continuous deployment pipeline automatically approves change requests based on the following conditions:

* Implementing the change does not cause any downtime (outage duration is zero).
* Deployment Readiness is `true`.

The Change Management automation in the DevSecOps reference implementation assumes that all changes are implemented without planned downtime. If your changes require planned downtime, you must create the change request manually and send it for approval. After it is approved, you can start deployment by providing the change request ID. The pipeline moves ahead to check its approval, and then runs the deployment. For more information, see [Manually approving change requests](/docs/devsecops?topic=devsecops-cd-devsecops-approve-cr).

Deployment Readiness is calculated from the evidence that is collected in the continuous integration and continuous deployment stages. If any of the collected evidence suggests a deviation, or an unsuccessful check, scan, or test that is related to the deployed set of artifacts, the Deployment Readiness is set to `false`, and the Change Request is not auto-approved. The deployment stops, and prevents further steps until the change request is approved.

You can read the created change request ID from the pipeline logs, wait for approval, and then start the deployment again by using the same change request ID. The pipeline moves ahead to check its approval, and then continues the deployment.

If the change is a type of `emergency`, the change request must be retroactively reviewed and approved.

## Rerunning failed deployments by using an existing change request ID
{: #cd-devsecops-cr-existing}
{: step}

If you do not want to use automated change management, you can provide a previously created and approved change request instead. Rerun failed deployments in the following scenarios:

* The latest automatically created change request is not ready for deployment and the change request was not auto-approved. You got approval for the change request and must start deployment again by using the change request again.
* The deployment requires downtime. You created the change request, it was approved, and you followed the required steps in your organization's Change Management Policy.
* No code or configuration changed. You created the change request, explained what changed (if the change was not in your app code), received approval, and started a deployment by using the approved change request.

You can start the DevSecOps reference continuous deployment pipeline by using a pre-approved change request and entering the change request ID for the **change-request-id** property.

 ![Pre-approved change request](images/pre-approved-cr.png){: caption="Pre-approved change request" caption-side="bottom"}

If the **change-request-id** property is set, the pipeline skips data collection for the change request and moves ahead to check the approval state of the change request. If the **change-request-id** is set to `notAvailable` by default, a change request is automatically created by the continuous deployment pipeline.

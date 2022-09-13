---

copyright:
  years: 2021, 2022
lastupdated: "2022-09-13"

keywords: DevSecOps

subcollection: devsecops


---

{{site.data.keyword.attribute-definition-list}}

# Automated change management
{: #cd-devsecops-change-mgmt}

Change management automation helps developers, approvers, and auditors to monitor the compliance aspects of deployments. Every deployment must follow an organization's change management policy.
{: shortdesc}

The pipelines collect evidence from each part of the build and deployment lifecycle. Because each piece of evidence correlates to a specific build and deployment of the artifacts, you can determine whether the build or test deployment for a deployed artifact contained any incidents. This correlation between evidence and build and deployment artifacts is implemented through the Inventory Model.

## Connection between evidence, inventory, and change management
{: #cd-devsecops-change-connect}

The following diagram shows the data flow and connection between evidence, inventory, and change management.

![Data flow](images/data-flow.svg){: caption="Data flow" caption-side="bottom"}
 
1. Continuous integration runs build artifacts and leaves behind evidence about what happened during the creation of those artifacts.
2. Continuous integration creates entries in the inventory about the artifacts that are created.
3. Built artifacts in the inventory are promoted to deployment environments such as staging or pre-prod.
4. Change management automation uses data from the inventory, the evidence locker, and the promotion pull request to create the change request.

Deployments also leave evidence behind such as acceptance tests. Successfully deployed and tested artifacts are further promoted to production environments, such as `prod`.

Every deployment to every environment and region must file a separate change request to the Change Management repo. Change management automation helps you to create these change requests based on all of the evidence and information that is collected from the pipelines.

For more information, see [Automating change management](/docs/devsecops?topic=devsecops-cd-devsecops-automate-changemgmt).

## Change management command order
{: #devsecops-change-mgmt-order}

### Create change request
{: #create-change-request}

Everything that changes the baseline must be tracked by using a change request. The changes include, for example, updates to the existing code level, changes to the configuration, and updates of the worker nodes. Collecting peer review compliance data is based on the data that is accessible in the inventory, the evidence locker, and the incident issue repository.

Finally, this step creates the change request that is based on the Promotion PR fields, and attaches available compliance data. Deployment readiness is calculated by the collected compliance status, based on the available evidence.

### Request for approval
{: #change-mgmt-request-approval}

If the created change request deployment state is not ready, this step requests it for approval.

### Check for approval
{: #change-mgmt-check-approval}

If every compliance check (for example, unit test, CRA tasks, branch protection, detect secrets) is successful, the change request is approved automatically, and the task runs successfully.

If a compliance check fails, the change request state is not approved.

You can approve the change request manually and add the `change-request-id` to the environment properties to use the already created change request in the next run.

Another solution is using the `emergency` label in the promotion pull requests. For more information, see [Add emergency label](/docs/devsecops?topic=devsecops-cd-devsecops-approve-cr).

### Set to implement
{: #change-mgmt-set-implement}

This step sets the status of the change request to `implement` depending on the `success` or `failure` status of the change.

### Close change request
{: #change-mgmt-request-close}

Details about the deployment are uploaded to the closing summary change task, and the change request is closed. In the close change request task, the close_category is added with these values:

* successful
* successful with issues (if the summary has issues)

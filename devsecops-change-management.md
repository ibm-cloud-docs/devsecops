---

copyright: 
  years: 2022, 2024
lastupdated: "2024-06-18"

keywords: DevSecOps, change request, change management, ibm cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Automated change management
{: #cd-devsecops-change-management}

Change management automation is an important part of the DevSecOps pipeline reference implementation. Developers, approvers, and auditors can monitor the compliance aspects of deployments. Every deployment must follow an organization's change management policy.
{: shortdesc}

The pipelines collect [evidence](/docs/devsecops?topic=devsecops-devsecops-evidence) from every part of the build and deployment lifecycle. Every piece of evidence correlates to a specific build and deployment of the artifacts. So, for each deployed artifact, you can tell if its build or test deployment has incidents. This correlation is implemented through the [inventory model](/docs/devsecops?topic=devsecops-cd-devsecops-inventory).

## The connection between evidence, inventory, and change management
{: #connection-evidence-inventory-change}

Figure 1 shows the data flow and connection between evidence, inventory, and change management.

![Connection between evidence, inventory, and change management](images/change-management-data-flow.svg "Flow diagram that shows the relationship between evidence, inventory, and change management"){: caption="Figure 1. Connection between evidence, inventory, and change management" caption-side="bottom"}

1. CI runs build artifacts and leaves evidence of what happened during the creation of those artifacts.
2. CI runs create entries about the created artifacts in the inventory.
3. Built artifacts in the Inventory are [promoted](/docs/devsecops?topic=devsecops-cd-devsecops-inventory) to deployment environments, like staging or pre-production.
4. Change management automation uses data from the inventory, the evidence locker, and the promotion PR to create the change request deployments. Change management automaton also leaves evidence of acceptance tests, for example. Successfully deployed and tested artifacts are further promoted to production environments.

Every deployment to every environment and region must file a change request on the Change Management System. Change management automation helps you to create these change requests based on all the evidence and information that is collected from the pipelines.

For more information, see [Automating change management](/docs/devsecops?topic=devsecops-cd-devsecops-automate-changemgmt).

## Change management command order
{: #devsecops-change-mgmt-order}

The sequence of change request steps is as follows:

### Create change request
{: #create-change-request}

Everything that changes the baseline must be tracked by using a change request. The changes include, for example, updates to the existing code level, changes to the configuration, and updates of the worker nodes. Collecting peer review compliance data is based on the data that is accessible in the inventory, the evidence locker, and the incident issue repository.

Finally, this step creates the change request that is based on the Promotion PR fields, and attaches available compliance data. Deployment readiness is calculated by the collected compliance status, based on the available evidence.

### Request for approval
{: #change-mgmt-request-approval}

If the created change request deployment state is not ready, this step requests approval for the change.

### Check for approval
{: #change-mgmt-check-approval}

If every compliance check (for example, unit test, CRA tasks, branch protection, detect secrets) is successful, the change request is approved automatically, and the task runs successfully.

If a compliance check fails, the change request state is not approved.

You can approve the change request manually and add the **change-request-id** to the environment properties to use the already created change request in the next run.

Another solution is to use the **emergency** label in the promotion pull requests. For more information, see [Add emergency label](/docs/devsecops?topic=devsecops-cd-devsecops-approve-cr).

### Set to `implement`
{: #change-mgmt-set-implement}

This step sets the status of the change request to `implement` depending on the `success` or `failure` status of the change.

### Close change request
{: #change-mgmt-request-close}

Details about the deployment are uploaded to the closing summary change task, and the change request is closed. In the close change request task, the close_category is added with these values:

* `successful`
* `successful with issues` (if the summary has issues)


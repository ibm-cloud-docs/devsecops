---

copyright:
  years:  2021, 2022
lastupdated: "2022-08-05"

keywords: DevSecOps

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Manually approving change requests
{: #cd-devsecops-approve-cr}

You can either approve change requests manually or automatically.
{: shortdesc}

By default, standard change requests are created and if all of the required compliance checks pass, the deployment readiness is set to `true`. When a change request is of the standard type and the deployment readiness is set to `true`, it is automatically approved. 

If one or more compliance checks fail, deployment readiness is set to `false`, the continuous deployment pipeline breaks, and the deployment stops because of the unapproved change request.

You can deploy a manually approved change request by adding an emergency label to the promotion pull request or by providing a manually pre-approved (with label `status_approved`) change request. Take either of the following steps:

## Adding an emergency label to promotion PR
{: #cd-devsecops-emergency-label}
{: step}

If you need to deploy emergency changes immediately, you can use emergency mode. When you promote a change, add an `emergency` label to the promotion pull request to prompt the continuous deployment pipeline to create an emergency change request. Emergency change requests allow deployment to continue even if the approval is missing.

Use the emergency mode sparingly and with caution.
{:  important}

You can set the value of the emergency label (`default: EMERGENCY`) that the continuous deployment  pipeline looks for on the promotion pull request when you create the toolchain.

 ![Emergency label](images/emergency-label-at-creation.png){: caption="Emergency label" caption-side="bottom"}

You can also change the value of the emergency label from the pipeline's environment variables:

 ![Emergency label variable](images/emergency-label-env.png){: caption="Emergency label variable" caption-side="bottom"}

When the deployment completes, the emergency change request is set to the `Review` state, which indicates that it requires the assigned approvers to review and approve it.

## Providing a manually pre-approved CR
{: #cd-devsecops-manual-preapprove}
{: step}

If the deployment is not an emergency, you can wait for the change request to be manually approved. Use the existing, manually pre-approved change request number that is set in the continuous deployment pipeline environment variables. The default value is `notAvailable`, which means the continuous deployment pipeline creates a new change request.

![Manually pre-approved CR](images/pre-approved-cr-label.png){: caption="Manually pre-approved CR" caption-side="bottom"}

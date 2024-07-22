---

copyright:
  years: 2022, 2024
lastupdated: "2024-02-22"

keywords: DevSecOps, ibm cloud, best practices, secure application, continuous integration,

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Best practices to deploy a secure application
{: #practices-cd-sec-app}

To deploy a secure application, the DevSecOps continuous deployment toolchain contains only one pipeline with the following practices.
{: shortdesc}

- Change management automation to help developers, approvers, and auditors track deployments from the lens of compliance.
- Creates an evidence summary from the evidence that is collected during the associated continuous integration pipeline run for a give set of changes.
- Creates a change request in {{site.data.keyword.gitrepos}} based change management repository and adds deployment evidence.
- Uses the inventory repository to promote built artifacts to deployment environments such as  staging, and production.
- Validates the change request, and auto approves if all deployment criteria are met.
- If a change request is approved, or tagged as emergency, the pipeline deploys the image from the inventory to production.

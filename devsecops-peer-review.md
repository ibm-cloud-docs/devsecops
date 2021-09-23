---

copyright:
  years: 2021
lastupdated: "2021-07-07"

keywords: DevSecOps

subcollection: devsecops

---

{:shortdesc: .shortdesc}
{:table: .aria-labeledby="caption"}
{:external: target="_blank" .external}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:download: .download}
{:help: data-hd-content-type='help'}
{:support: data-reuse='support'}

# Peer review compliance
{: #cd-devsecops-peer-review}

Peer code reviews are a general best practice in software development and they are also a key component of delivering secure and compliant software.
{: shortdesc}

The DevSecOps reference implementation helps to enforce the review of new code changes before they are merged and promoted to production. The reference implementation discovers instances of code that was not peer reviewed, collects evidence, and creates incident issues to track these items.

Before you can merge code in the main branch, it must be reviewed by a person who did not upload the modified code.

The code repository (repo) must have at least two members, one member that has admin privileges and another member that has write privileges. If code is merged into a repo without a review, the action must be visible in the code repo audit trail. Periodically scan the audit trail to identify and analyze these exceptional situations.

The pipeline collects peer review compliance data during builds and deployments to create the audit trail from code pull request merges to change requests.

 ![Data collection](images/data-collection.svg)
 
## Data that is collected in continuous integration build runs 
{: #cd-devsecops-ci-data}

This data collection contains a list of all of the pull request that  were merged in app repos since the last build.

Pull request data is collected directly from the app repos. Data for each pull request that is related to commits between the repo commit that triggered the previous build and the currently available commit is collected. These commits are stored in the inventory entries.

Commits that do not contain a pull request create a compliance incident issue in the following releases of the pipeline. You cannot commit directly to the main branches.
{: important}

Collected data is saved as an evidence artifact, which is uploaded to the evidence locker, and then referred to in the evidence itself. The final evidence result is determined by the approved pull requests. Unapproved, but merged pull requests fail this type of evidence.

## Data that is collected in continuous delivery deployment runs 
{: #cd-devsecops-cd-data}

This data collection contains a list of all of the pull requests that were merged in app repos since the last deployment. 

Pull request data is collected from the inventory, the evidence locker, and the incident issue repo.

* The inventory gathers data from all builds on related artifacts since the last deployment.
* The evidence locker collects stored peer review data from the builds.
* The incident issue repo collects information about open pull request incidents.

The app repos are not accessed during this data collection. Because continuous delivery pipelines are assumed to be located in isolated environments, you cannot cross those boundaries.
{: important}

## Change request content 
{: #cd-devsecops-cr-content}

The following data is included in the automatically generated change request:

* List of pull requests in code repos that are related to this release. This data includes the pull request title and URL.
* List of pull request incidents that are not remediated. This data includes the pull request number and URL, and the incident URL.

Non-remediated pull request incidents impact the deployment readiness of the change request. If any pull request incidents are found, they are considered vulnerabilities and the change request must be reviewed and approved manually.
{: important}

## Pull request incident remediation
{: #cd-devsecops-pr-remediate}

Pull request incidents are considered vulnerabilities because they indicate that unchecked code is contained in the released artifacts. To remediate these incidents, complete the following steps:

1. Retroactively review the merged change.
2. Create an issue about how to fix any existing problems with the code.
3. Close the pull request incident issue.

The author of the pull request and the person who closes the pull request incident issue cannot be the same person.
{: tip}

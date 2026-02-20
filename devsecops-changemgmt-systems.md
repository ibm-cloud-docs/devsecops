---

copyright: 
  years: 2021, 2026
lastupdated: "2026-02-19"

keywords: DevSecOps, change management system, change management, ibm cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Change Request Management Systems
{: #cd-devsecops-changemgmt-systems}

Change management automation is an important part of the DevSecOps pipeline reference implementation. Developers, approvers, and auditors can monitor the compliance aspects of deployments. Every deployment must follow an organization's change management policy.
{: shortdesc}



## Git-based change management system
{: #cd-devsecops-changemgmt-git}

Git-based Change Request Management manages change requests by creating issues in a Git repository as tickets. These issues contain information that is required for the change request workflow.

Git Repo and Issue Tracking (GRIT) repository can be used as the Git repository to track change request.



### Required parameters for Git-based change management system
{: #cd-devsecops-changemgmt-git-params}

The following parameters are required for git-based change management:

| Parameter name | Description |
|-|-|
| `change-management-repo` | The Git repository URL to use for change management. |

The Git tool integration configuring the Git repository needs to be defined in the toolchain.
{: note}

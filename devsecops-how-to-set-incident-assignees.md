---

copyright:
  years: 2022
lastupdated: "2022-05-06"

keywords: DevSecOps, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Assigning incident issues to users
{: #assign-incident-issues}

Incident issues that are created by the continuous integration (CI) or continuous compliance (CC) pipelines can have default assignees.
{: shortdesc}

To achieve this, set the `incident-assignees` environment property to one or more usernames that are separated by a comma. For example, `First-User` or `First-User,Second-User`.



You can define this environment property per trigger so that each trigger can assign different users.
{: tip}

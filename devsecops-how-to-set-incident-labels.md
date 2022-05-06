---

copyright:
  years: 2022
lastupdated: "2022-05-06"

keywords: DevSecOps, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Labeling incident issues
{: #label-incident-issues}

Incident issues that are created by the continuous integration (CI) or continuous compliance (CC) pipelines can have default labels.
{: shortdesc}

To achieve this, set the `incident-labels` environment property to one or more labels that are separated by a comma. For example, `First-Label` or `First-Label,Second-Label`.

 

You can define this environment property per trigger so that each trigger can assign different users.
{: tip}

---

copyright:
  years: 2022
lastupdated: "2022-06-09"

keywords: DevSecOps, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Labeling incident issues
{: #label-incident-issues}

Incident issues that are created by the continuous integration (CI) or continuous compliance (CC) pipelines can have default labels.
{: shortdesc}

To set default labels for incident issues, set the `incident-labels` environment property to one or more labels that are separated by a comma. For example, `First-Label` or `First-Label,Second-Label`.

The pipeline attaches these labels to the incident issues that are created by the [`collect-evidence`](/docs/devsecops?topic=devsecops-devsecops-collect-evidence) script.

You can define this environment property per trigger so that each trigger can assign different users.
{: tip}

## Labels for incident issues discovered by Code Risk Analyzer
{: #label-incident-issues-cra}

Code Risk Analyzer (CRA) detects multiple types of vulnerabilities, like app dependency and image vulnerability.

The type of vulnerability can be of the following types: name `os`, `python`, `js`, `golang`, or `java`.

If the type of vulnerability is of type `os`, an  `os-vulnerability` label is attached to the issue. For any other type of vulnerability, an `app-vulnerability` label is attached to the issue.

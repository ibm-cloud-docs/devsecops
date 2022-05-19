---

copyright:
  years: 2022
lastupdated: "2022-05-19"

keywords: DevSecOps, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Labels for incident issues discovered by Code Risk Analyzer
{: #label-incident-issues-cra}

Code Risk Analyzer (CRA) detects multiple types of vulnerabilities, like app dependency and image vulnerability.
{: shortdesc}

The type of vulnerability can be of the following types: name `os`, `python`, `js`, `golang`, or `java`.

If the type of vulnerability is of type `os`, an  `os-vulnerability` label is attached to the issue. For any other type of vulnerability, an `app-vulnerability` label is attached to the issue.

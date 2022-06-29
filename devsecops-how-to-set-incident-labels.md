---

copyright:
  years: 2022
lastupdated: "2022-06-29"

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

## Labels for incident issues with available fixes
{: #label-incident-issues-fix-available}

For incident issues that are created by the continuous integration (CI) pipeline or continuous compliance (CC) pipeline, the scanner might have remediation information as a part of the scan result. If remediation information is available, a `fix-available` label is added to the incident issue with a link to the fix description inside the issue description.

However, the absence of the `fix-available` label does not mean that the issue is not actionable because not every scanner includes the fix information in the scan result. The scanner suggests the fix information, and that information comes from wherever the scanner sources this "fix" data. Some scanners might not have up-to-date "fix" dictionaries or do not contain information for a fix.
{: note}

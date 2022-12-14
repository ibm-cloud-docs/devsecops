---

copyright:
  years: 2022
lastupdated: "2022-12-14"

keywords: DevSecOps, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Managing incident issues
{: #incident-issues}

From a compliance perspective, creating, storing, and updating incident issues (vulnerability, CVE) as part of the [Continuous Integration](/docs/devsecops?topic=devsecops-cd-devsecops-ci-pipeline) and [Continuous Compliance](/docs/devsecops?topic=devsecops-devsecops-cc-pipeline) pipelines are essential for evidence collection.
{: shortdesc}

During the execution of the CI and CC pipelines, the [`collect-evidence` script](/docs/devsecops?topic=devsecops-devsecops-collect-evidence) creates incident issues, attaches them to the collected evidence, and stores them in the [incident issues repository](/docs/devsecops?topic=devsecops-tutorial-cd-devsecops#devsecops-ci-tool-integration-issues).

The `collect-evidence` script uses the functions of the [cocoa incident process](/docs/devsecops?topic=devsecops-cd-devsecops-cli#incident-process) command of the DevSecOps CLI tool, which processes the provided scan results and either creates new incident issues in the provided repository per vulnerability or updates the existing incident issues based on the subject-incident pairs. 
Therefore, the incident issues are bound to assets and created according to the [results of specific tools](/docs/devsecops?topic=devsecops-devsecops-issues-due-date#processing-results-issues).

## Incident issue processing
{: #incident-issue-processing-ci-cc}

Even though the CI and CC pipelines have many common steps, the issue processing of these pipelines has some [significant differences](/docs/devsecops?topic=devsecops-devsecops-issues-due-date#due-date-ci-cc):
* Incident issues that are created during the CI pipeline do not carry a due date, while incident issues that are created during the CC pipeline do.
* Incident issues that are created during the CI pipeline are found during the build, while incident issues that are created during the CC pipeline are found in the production environment.

The following diagram shows the possible use cases that are based on these differences:

![Vulnerability use cases flow](images/devsecops-vulnerability-usecases-flow.png "Vulnerability use cases flow"){: caption="Figure 1. Vulnerability use cases flow" caption-side="bottom"}

## Setting the due date for incident issues
{: #incident-issue-due-date-setting}

If the incident issues are found in production, the `Due Date` property might be added to the issue to specify the grace period in which it must be fixed. The [duration of the grace period](/docs/devsecops?topic=devsecops-devsecops-issues-due-date#grace-period-duration) is determined by the severity of the found vulnerability.

For more information about customizing the grace periods, see [Configuring custom grace periods on the CC pipeline](/docs/devsecops?topic=devsecops-devsecops-issues-due-date#configure-custom-grace-period).

## Labeling incident issues
{: #label-incident-issues}

Incident issues that are created by the continuous integration (CI) or continuous compliance (CC) pipelines can have default labels.

### Labels for incident issues discovered by Code Risk Analyzer
{: #label-incident-issues-cra}

Code Risk Analyzer (CRA) detects multiple types of vulnerabilities, like app dependency and image vulnerability.

The type of vulnerability can be of the following types: name `os`, `python`, `js`, `golang`, or `java`.

If the type of vulnerability is of type `os`, an `os-vulnerability` label is attached to the issue. For any other type of vulnerability, an `app-vulnerability` label is attached to the issue.

### Labels for incident issues with available fixes
{: #label-incident-issues-fix-available}

For incident issues that are created by the continuous integration (CI) pipeline or continuous compliance (CC) pipeline, the scanner might have remediation information as a part of the scan result. If remediation information is available, a `fix-available` label is added to the incident issue with a link to the fix description inside the issue description.

However, the absence of the `fix-available` label does not mean that the issue is not actionable because not every scanner includes the fix information in the scan result. The scanner suggests the fix information, and that information comes from wherever the scanner sources this "fix" data. Some scanners might not have up-to-date "fix" dictionaries or do not contain information for a fix.
{: note}

---

copyright:
  years: 2022
lastupdated: "2022-12-13"

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

## Managing incident issues
{: #incident-issue-managing}

### Setting the due date for incident issues
{: #incident-issue-due-date-setting}

If the incident issues are found in production, the `Due Date` property might be added to the issue to specify the grace period in which it must be fixed. The [duration of the grace period](/docs/devsecops?topic=devsecops-devsecops-issues-due-date#grace-period-duration) is determined by the severity of the found vulnerability.

For more information about customizing the grace periods, see [Configuring custom grace periods on the CC pipeline](/docs/devsecops?topic=devsecops-devsecops-issues-due-date#configure-custom-grace-period).

## Postponing the due date of an incident issue
{: #due-date-postpone}

If you want to postpone the due date of an incident issue, you can ask for a review from a security focal. Depending on the review, you can postpone the due date by modifying the `Due date` field in the {{site.data.keyword.gitrepos}} incident issues meta fields. 

![Setting and updating due date on {{site.data.keyword.gitrepos}}](images/devsecops-native-due-date.png){: caption="Setting and updating due date on {{site.data.keyword.gitrepos}}" caption-side="bottom"}

Be sure to reference the security-focal review in the issue, such as providing a link to it in a comment.
{: important}



### Postponing the due date of an issue that has a PCE
{: #due-date-change-pce}

If you have an issue that has a Public Cloud Security Exception (PCE), you can change its due date to be the same as the expiration date of the PCE so that evidence collection continues up to the PCE expiration date.

## Labeling incident issues
{: #label-incident-issues}

Incident issues that are created by the continuous integration (CI) or continuous compliance (CC) pipelines can have default labels.

### Labels for incident issues discovered by Code Risk Analyzer
{: #label-incident-issues-cra}

Code Risk Analyzer (CRA) detects multiple types of vulnerabilities, like app dependency and image vulnerability.

The type of vulnerability can be of the following types: name `os`, `python`, `js`, `golang`, or `java`.

If the type of vulnerability is of type `os`, an  `os-vulnerability` label is attached to the issue. For any other type of vulnerability, an `app-vulnerability` label is attached to the issue.

### Labels for incident issues with available fixes
{: #label-incident-issues-fix-available}

For incident issues that are created by the continuous integration (CI) pipeline or continuous compliance (CC) pipeline, the scanner might have remediation information as a part of the scan result. If remediation information is available, a `fix-available` label is added to the incident issue with a link to the fix description inside the issue description.

However, the absence of the `fix-available` label does not mean that the issue is not actionable because not every scanner includes the fix information in the scan result. The scanner suggests the fix information, and that information comes from wherever the scanner sources this "fix" data. Some scanners might not have up-to-date "fix" dictionaries or do not contain information for a fix.
{: note}

## Slack alerts for pending and overdue issues for CC pipeline
{: #devsecops-cc-pipeline-slack-alerts}

The Continuous Compliance (CC) pipeline can set due dates for the incident issues. The pipeline can also notify users about issues that have approaching due dates and overdue due dates by using Slack, if Slack integration is enabled.

For more information, see [Set up a Continuous Integration (CI) toolchain](/docs/devsecops?topic=devsecops-tutorial-ci-devsecops-template#devsecops-ci-tool-integration-optional-slack). For more information about due dates, see [Incident issues with Due Date](/docs/devsecops?topic=devsecops-issues-due-date).

The issues that are notified are classified as follows:
* Issues having pending due dates within a specific timeframe: issues that are open, have due dates, and are due within a timeframe.
* Issues having past due dates: issues that are open, have due dates, and the date is passed.

The timeframe durations are `overdue`, `due in 1 day`, `due in 2 days`, `due in 5 days`, and `due in 10 days`.

An example of this capability is as follows:

```text
Overdue issues:
- <issue url#1>
- <issue url#2>
- <issue url#3>

Issues due in 1 day:
- <issue url#4>

Issues due in 2 days:
- <issue url#5>

Issues due in 5 days:
- <issue url#6>
- <issue url#7>

Issues due in 10 days
- <issue url#8>
- <issue url#9>
- <issue url#10>
- <issue url#11>
- <issue url#12>
```

The aggregated list is ordered with the issues that are overdue first, and the issues with nearest due dates are listed before those with a later due date.

The `cc-finish` stage in the CC pipeline queries for the issues based on the criteria and triggers a Slack alert.

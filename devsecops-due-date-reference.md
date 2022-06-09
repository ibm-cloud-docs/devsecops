---

copyright:
  years: 2022
lastupdated: "2022-06-09"


keywords: DevSecOps, IBM Cloud, incident issues, grace period, due date

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Incident issues with Due Date
{: #devsecops-issues-due-date}

When you use the [collect-evidence](/docs/devsecops?topic=devsecops-devsecops-collect-evidence) script, incident issues are created and attached to the collected evidence. If issues are found in production, they can have a specified time period in which they must be fixed so that deployments are not blocked. The timeframe that is given to fix the problem in production is called the _**Grace Period**_. However, for better readability, _**Due Date**_ is now available in incident issues so that users know the date when the fix is due without calculating it from the grace period.
{: shortdesc}

If an issue such as vulnerability or CVE is found in production, and the same problem is also found in a build, the build does not make the situation worse. The feature can be deployed, and the team can focus on fixing the issue in production.

## Grace period duration
{: #grace-period-duration}

The duration of the grace period is determined by the severity of the found vulnerability. The pipeline calculates the grace period according to the following table:

| Severity      | Grace Period |
| :-----------: | :----------: |
| Informational | 90 days      |
| Low           | 45 days      |
| Medium        | 45 days      |
| High          | 15 days      |
| Critical      | 15 days      |
{: caption="Table 1. Default grace periods" caption-side="bottom"}

## Due Date calculation
{: #due-date-calculation}

The date of finding is when the CC pipeline runs and finds the issue in prod. If the issue exists, CC updates it with the Due Date that is calculated from that moment.

```text
<due date> = <date of finding the issue in prod> + <grace period in days (determined by severity)>
```

## Due Date format
{: #due-date-format}

Due date is in `ISO 8601` format, and shows as **YYYY-MM-DD**. Example: `Due Date: 2022-04-01`.

## Use cases
{: #due-date-usecases}

* A vulnerability is found in one of the base images in production by the CC pipeline. The team is notified of an Incident Issue with a Grace period set according to the severity of the vulnerability. The Grace Period is the number of days that the team must deploy a fix.

* The team builds a new release with a new feature. The build finds a CVE associated with the base image that is used for the application. The team runs the CC pipeline manually, which scans artifacts already in production. The manual CC run detects the same CVE in the same application in production and adds the Grace Period to the Incident Issue. The team now can build and deploy without being blocked.

## Difference between issue processing in CI and CC pipelines
{: #due-date-ci-cc}

Incident issues are created in both CI and CC pipelines:

* An issue that is created in **CI** means it was found during the build
* An issue that is created in **CC** means it was found in the production environment

A Due Date can be added automatically to issues only if they are related to problems found in the production environment. This means that only the CC pipeline is allowed to add the Due Date to an issue. If CI finds the issue and the Due Date is not available, the value of the Due Date is **n/a**.

## Processing results into issues
{: #processing-results-issues}

Issues for problems that are found are created according to the results of specific tools. The `collect-evidence` script attempts to process the result files in the attachments and create a list of issues.

Issues are bound to assets, which can be commits in a repository or a docker image with a digest.

An issue is created for every issue ID - an issue ID is composed of the following components:

- Bound asset
- The tool that found the vulnerability
- The vulnerability identifier (the CVE ID, for example)

For example, if `CVE-2022-001` is found by two separate tools, the process creates two issues today.

### Supported tools
{: #due-date-supported-tools}

Currently, the following tools are supported for issue processing:

* CRA
* VA
* OWASP-ZAP API scanner
* OWASP-ZAP UI scanner
* SonarQube

### Unsupported tools or result formats
{: #due-date-unsupported-tools-formats}

If the `collect-evidence` script receives an attachment from an unsupported tool, or the result file format is not recognized during processing, the script skips creating issues and uses the result file as a simple attachment to the evidence.

### Issue contents
{: #due-date-issue-contents}

- _“Issue”_ is the name of the problem or vulnerability
- _“Due Date”_ shows the date when the fix is due or **n/a** if no Due Date is available
- _“Subject”_ is the asset that the issue is bound to
- _“URL”_ is the link to the exact version of the asset
- _“Tool Type”_ is the tool that produced the contents for the issue

The issue description contains the timestamp when the issue was first discovered. For example: `First found on 2022-04-07.`. The date is in `YYYY-MM-DD` format. The locations where the problem occurs are listed in the comments of the issue.

## More issue options
{: #due-date-issue-options}

### Exempt
{: #due-date-issue-options-exempt}

If you want to mark an issue as permanently exempt, add the `exempt` label to the issue, so that the issue doesn't block deployments. However, the issue still appears in the change request for the deployment. As a best practice, link the source of the exemption (for example, the issue ticket) to the issue in a comment so that reviewers understand why the issue is exempted.

If an issue is marked with the `exempt` label, the CI pipeline needs to be rerun. Otherwise, it does not produce new evidence.
{: note}

## Related information
{: #due-date-related}

For more information, see [Configuring custom grace periods on the CC pipeline](/docs/devsecops?topic=devsecops-configure-custom-grace-period).

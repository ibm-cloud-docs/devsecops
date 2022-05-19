---

copyright:
  years: 2022
lastupdated: "2022-05-19"

keywords: DevSecOps, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Slack alerts for pending and overdue issues for CC pipeline
{: #devsecops-cc-pipeline-slack-alerts}

The Continuous Compliance (CC) pipeline can set due dates for the incident issues. The pipeline can also notify users about issues that have approaching due dates and overdue due dates by using Slack, if Slack integration is enabled.
{: shortdesc}

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

---

copyright:
  years: 2022
lastupdated: "2022-12-14"

keywords: DevSecOps, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Processing incident and nonincident issues
{: #issue-processing}


The following types of issues are supported:

* Incident issues, which can arise due to vulnerabilities or CVEs.
* Nonincident issues, these issues are not from vulnerabilities, but rather represent a deviation from the compliance posture. For example, unit test failures and branch protection check failures. 
{: shortdesc}

## Adding default assignees for issues
{: #issue-assignee}

You can define multiple default assignees for the issue by using the `incident-assignees` pipeline parameter. The `incident-assignees` parameter can be used only with GitHub accounts and GitLab Premium accounts. For more information about the usage of the `incident-assignees` parameter, see [Assigning issues to users](#assign-issues).

You can also set a default issue assignee for the pipeline with the `incident-assignee` pipeline parameter, but note that this parameter is deprecated and will be removed with the v1 evidence (legacy) collection.

## Filtering issues
{: #issue-filtering}

You can filter and search for issues by using default and custom labels. The following default labels are assigned to the issues upon creation or update:
- The [scan type](/docs/devsecops?topic=devsecops-devsecops-issues-due-date#due-date-supported-tools) that is used for the issue processing is added to the incident issue as a tool label (for example, `tool:cra`, `tool:va`, `tool:sonarqube`).
- A severity label is also assigned to the issues by default. The severity categories are defined based on the scan results and can be one of the following: `severity:critical`, `severity:high`, `severity:medium`, `severity:low`, `severity:informational`.
- The `has-exempt` is a VA tool-specific label that is assigned to the issue if it is exempted based on the scan result. If the `exempt` status is not included in the scan result, the issue can be exempted manually by assigning the `exempt` label and adding a link to the source of the exempt issue ticket in a comment. For more information, see [Exemptions](#due-date-issue-options-exempt). If an issue is marked with the `exempt` label, rerun the CI pipeline. Otherwise, the pipeline does not produce new evidence.

You can also add a default label for issues with the `incident-labels` pipeline parameter. For more information, see [Labeling issues](#label-issues).

## Searching on {{site.data.keyword.gitrepos}}
{: #devsecops-search-in-issues}

When you use {{site.data.keyword.gitrepos}}, you can search for issues in various ways in the UI. For example, you can filter by texts in the title or the body, even in comments, or you can filter by any label.

For more information, see the following documentation:
- How to [search on GitLab](https://docs.gitlab.com/ee/user/search/#search-issues-and-merge-requests){: external}.
- How to [search on GitHub](https://docs.github.com/en/search-github/searching-on-github/searching-issues-and-pull-requests){: external}.

## Labeling issues
{: #label-issues}

Issues that are created by the continuous integration (CI) or continuous compliance (CC) pipelines can have default labels.

Issues that are created by any scanning tool contains a default `severity` label. The severity levels are `informational`, `low`, `medium`, `high`, or `critical`. Example labels are as follows: `severity:informational`, `severity:low`, `severity:medium`, `severity:high`, or `severity:critical`. In the absence of severity information from the scanning tool, a default `severity:high` label is attached to the issue.

To set default labels for issues, set the `incident-labels` environment property to one or more labels that are separated by a comma. For example, `First-Label` or `First-Label,Second-Label`.

The pipeline attaches these labels to the issues that are created by the [`collect-evidence`](/docs/devsecops?topic=devsecops-devsecops-collect-evidence) script.

You can define this environment property per trigger so that each trigger can assign different users.
{: tip}

## Assigning issues to users
{: #assign-issues}

Issues that are created by the continuous integration (CI) or continuous compliance (CC) pipelines can have default assignees.

To assign default assignees, set the `incident-assignees` environment property to one or more usernames that are separated by a comma. For example, `First-User` or `First-User,Second-User`. For more information about the `incident-assignees` environment property, see [Pipeline parameters](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm).

The pipeline takes these usernames and assigns these users to the incident issues that are created by the [`collect-evidence`](/docs/devsecops?topic=devsecops-devsecops-collect-evidence) script. If a username is invalid, the logs provide a warning about an unsuccessful assignment, but the evidence collection is successful.

You can define this environment property per trigger so that each trigger can assign different users.
{: tip}

## Exemptions
{: #due-date-issue-options-exempt}

If you want to mark an issue as permanently exempt, add the `exempt` label to the issue so that the issue doesn't block deployments. However, the issue still appears in the change request for the deployment. As a best practice, link the source of the exemption (for example, the issue ticket) to the issue in a comment so that reviewers understand why the issue is exempted. Issues that have the exempt label are open permanently.

If you want to mark an issue permanently exempted with a custom label, add that label to the issue, and then add the `custom-exempt-label` optional parameter to the CI and CC pipelines with the same value as the custom label.

If an issue is marked as exempted, the CI pipeline needs to be rerun. Otherwise, it does not produce new evidence.
{: note}

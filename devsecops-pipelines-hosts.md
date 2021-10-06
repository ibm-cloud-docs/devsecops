---

copyright:
  years: 2021
lastupdated: "2021-06-30"

keywords: DevSecOps

subcollection: devsecops

---

{:shortdesc: .shortdesc}
{:table: .aria-labeledby="caption"}
{:external: target="_blank" .external}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:download: .download}
{:help: data-hd-content-type='help'}
{:support: data-reuse='support'}

# Hosts accessed from pipelines
{: #cd-devsecops-pipeline-hosts}

You can access hosts from the following types of pipelines: pull request, continuous integration, promotion, and continuous delivery.
{: shortdesc}

## Pull request pipelines
{: #pr-pipeline-hosts}

The following table lists and describes each of the hosts that are accessed from pull request pipelines.

| Host | Description |
|-------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| `https://hooks.slack.com/services` | When the Slack integration is configured, notifications are sent when pipelines start and finish. |
| `https://otc-github-consolidated-broker.<region>.devops.cloud.ibm.com/github/token?git_id=integrated` | The OTC broker URL to fetch a Git token if one was not provided.  |
| `https://<region>.git.cloud.ibm.com` | Clones repos, and sets and checks the pull request status. |
| `https://detect-secrets-client-version.s3.us-south` | The incremental stage ID that is shown in the pipeline UI.  |
| `https://otcbroker.devopsinsights.cloud.ibm.com` | The Git branch that the job uses as input. This property is only available in jobs that use a Git repository (repo) as input. |
| `https://iam.cloud.ibm.com/identity/token` | The Git commit that the job uses as input. This property is only available in jobs that use a Git repo as input. |
| `https://gitsecure.us-south.devopsinsights.cloud.ibm.com` | The Git commit value of the job's last successful run. This property is only available in jobs that use a Git repo as input. |
| `https://vcurator.us-south.devopsinsights.cloud.ibm.com` | The Git repo URL that the job uses as input. This property is only available in jobs that use a Git repo as input. |
{: caption="Table 1. Pull request pipeline hosts" caption-side="top"}

## Continuous integration pipelines
{: #ci-pipeline-hosts}

The following table lists and describes each of the hosts that are accessed from continuous integration pipelines.

| Host | Description |
|-------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| `https://hooks.slack.com/services` | When the Slack integration is configured, notifications are sent when pipelines start and finish. |
| `https://otc-github-consolidated-broker.<region>.devops.cloud.ibm.com/github/token?git_id=integrated` | The OTC broker URL to fetch a Git token if one was not provided.   |
| `https://<region>.git.cloud.ibm.com` | Clones repos, sets and checks the pull request status, and stores evidence. |
| Cloud Object Storage endpoint in `cos-endpoint` | The endpoint that stores the evidence in Cloud Object Storage.  |
| `https://detect-secrets-client-version.s3.us-south` | Detects secrets checks for newer versions. |
| `https://otcbroker.devopsinsights.cloud.ibm.com` | Checks the toolchain for Code Risk Analyzer support. |
| `https://iam.cloud.ibm.com/identity/token` | Gets the IAM token. |
| `https://gitsecure.us-south.devopsinsights.cloud.ibm.com` | The Code Risk Analyzer API. |
| `https://vcurator.us-south.devopsinsights.cloud.ibm.com` | Code Risk Analyzer. |
| `https://api.<region>.devops.cloud.ibm.com/v1/tekton-pipelines` | Gets the pipeline run data. |
{: caption="Table 2. Continuous integration pipeline hosts" caption-side="top"}

## Promotion pipelines
{: #promotion-pipeline-hosts}

The following table lists and describes each of the hosts that are accessed from promotion pipelines.

| Host | Description |
|-------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| `https://hooks.slack.com/services` | When the Slack integration is configured, notifications are sent when pipelines start and finish. |
| `https://otc-github-consolidated-broker.<region>.devops.cloud.ibm.com/github/token?git_id=integrated` | The OTC broker URL to fetch a Git token if one was not provided.  |
| `https://<region>.git.cloud.ibm.com` | The open promotion pull request. |
{: caption="Table 3. Promotion pipeline hosts" caption-side="top"}

## Continuous delivery pipelines
{: #cd-pipeline-hosts}

The following table lists and describes each of the hosts that are accessed from continuous delivery pipelines.

| Host | Description |
|-------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| `https://hooks.slack.com/services` | When the Slack integration is configured, notifications are sent when pipelines start and finish. |
| `https://otc-github-consolidated-broker.*region*.devops.cloud.ibm.com/github/token?git_id=integrated` | The OTC broker URL to fetch a Git token if one was not provided.  |
| `https://<region>.git.cloud.ibm.com` | Clones repos, and stores evidence, change requests, and inventory. |
| Cloud Object Storage endpoint in `cos-endpoint` | The endpoint that stores the evidence in Cloud Object Storage.  |
| `https://iam.cloud.ibm.com/identity/token` | The get IAM token. |
{: caption="Table 4. Continuous delivery pipeline hosts" caption-side="top"}

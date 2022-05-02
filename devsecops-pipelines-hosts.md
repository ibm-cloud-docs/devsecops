---

copyright:
  years: 2021, 2022
lastupdated: "2022-05-02"

keywords: DevSecOps

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Hosts accessed from pipelines
{: #cd-devsecops-pipeline-hosts}

You can access hosts from the following types of pipelines: pull request, continuous integration, promotion, continuous delivery, and continuous compliance.
{: shortdesc}

## Pull request pipelines
{: #pr-pipeline-hosts}

The following table lists and describes each of the hosts that are accessed from pull request pipelines.

| Host | Description |
|-------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| `https://hooks.slack.com/services` | When the Slack integration is configured, notifications are sent when pipelines start and finish. |
| `https://otc-github-consolidated-broker.<region>.devops.cloud.ibm.com/github/token?git_id=integrated` | The OTC broker URL to fetch a Git token if one was not provided.  |
| `https://<region>.git.cloud.ibm.com` | Clones repos, and sets and checks the pull request status. |
| `https://detect-secrets-client-version.s3.us-south` | Detects secrets checks for newer versions.  |
| `https://otcbroker.devopsinsights.cloud.ibm.com` | Checks the toolchain for Code Risk Analyzer support. |
| `https://iam.cloud.ibm.com/identity/token` | Gets the IAM token. |
| `https://gitsecure.us-south.devopsinsights.cloud.ibm.com` | The Code Risk Analyzer API. |
| `https://vcurator.us-south.devopsinsights.cloud.ibm.com` | Code Risk Analyzer. |
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
| `https://detect-secrets-client-version.s3.us-south` | Detects secrets checks for newer versions.  |
| `https://otcbroker.devopsinsights.cloud.ibm.com` | Checks the toolchain for Code Risk Analyzer support. |
| `https://gitsecure.us-south.devopsinsights.cloud.ibm.com` | The Code Risk Analyzer API. |
| `https://vcurator.us-south.devopsinsights.cloud.ibm.com` | Code Risk Analyzer. |
| Cloud Object Storage endpoint in `cos-endpoint` | The endpoint that stores the evidence in Cloud Object Storage.  |
| `https://iam.cloud.ibm.com/identity/token` | Gets the IAM token. |
{: caption="Table 4. Continuous delivery pipeline hosts" caption-side="top"}

## Continuous compliance pipelines
{: #cc-pipeline-hosts}

The following table lists and describes each of the hosts that are accessed from continuous compliance pipelines.

| Host | Description |
|-------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| `https://hooks.slack.com/services` | When the Slack integration is configured, notifications are sent when pipelines start and finish. |
| `https://otc-github-consolidated-broker.<region>.devops.cloud.ibm.com/github/token?git_id=integrated` | The OTC broker URL to fetch a Git token if one was not provided.  |
| `https://<region>.git.cloud.ibm.com` | Clones repos, and sets and checks the pull request status. |
| `https://iam.cloud.ibm.com/identity/token` | Gets the IAM token. |
| Cloud Object Storage endpoint in `cos-endpoint` | The endpoint that stores the evidence in Cloud Object Storage.  |
{: caption="Table 5. Continuous Compliance pipeline hosts" caption-side="top"}

---

copyright:
  years: 2021, 2022
lastupdated: "2022-03-01"

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

# Setting pull request status
{: #cd-devsecops-set-pr-status}

You can use the CLI `set-status` command to set the pull request status.
{: shortdesc}

The current implementation is tested on GitHub. For a list of available values, see [GitHub statuses](https://docs.github.com/en/rest/reference/repos#statuses){: external}.
{: tip}

1. Specify the following environment variables:

   ```bash
   GHE_TOKEN=    # Github token
   GHE_ORG=      # Github organization/username
   GHE_REPO=     # Github repository
   GHE_COMMIT=   # Github commit hash
   ```

1. Run the following command to set the pull request status:

   ```bash
   $ cocoa set-status \
    --state="pending" \
    --targetURL="https://cloud.ibm.com/devops/pipelines/tekton/some-toolchain/runs/some-pipelinerun/lint/lint?   env_id=ibm:yp:us-south" \
    --context="tekton/lint" \
    --description="Tekton linter is running."
   ```

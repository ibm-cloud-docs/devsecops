---

copyright:
  years: 2021, 2022
lastupdated: "2022-03-18"

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

You can use the [DevSecOps CLI](/docs/devsecops?topic=devsecops-cd-devsecops-cli) `set-status` command to set a GitHub or GitLab commit status. For more information about this command, see the [DevSecOps CLI documentation](/docs/devsecops?topic=devsecops-cd-devsecops-cli#set-status).
{: shortdesc}

- Example usage on GitLab:

   ```bash
   $ export GITLAB_TOKEN='my-git-token'
   $ export GHE_ORG='my-github-username'
   $ export GHE_REPO='my-github-repo-name'
   $ export GIT_COMMIT='1df4adf81a294315a3fb4e211df9ae6cad4895c1'
   $ cocoa set-status \
    --state="pending" \
    --targetURL="https://cloud.ibm.com/devops/pipelines/tekton/pipeline-id/runs/run-id/lint/lint?env_id=ibm:yp:us-south" \
    --context="tekton/lint" \
    --description="Tekton linter is running." \
    --git-provider="gitlab"
   ```

- Example usage on GitHub:

   ```bash
   $ export GHE_TOKEN='my-git-token'
   $ export GHE_ORG='my-github-username'
   $ export GHE_REPO='my-github-repo-name'
   $ export GIT_COMMIT='1df4adf81a294315a3fb4e211df9ae6cad4895c1'
   $ cocoa set-status \
    --state="pending" \
    --targetURL="https://cloud.ibm.com/devops/pipelines/tekton/pipeline-id/runs/run-id/lint/lint?env_id=ibm:yp:us-south" \
    --context="tekton/lint" \
    --description="Tekton linter is running." \
    --git-provider="github"
   ```

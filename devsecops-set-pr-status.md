---

copyright:
  years: 2021, 2022
lastupdated: "2022-08-17"

keywords: DevSecOps

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Configuring the pull request status
{: #cd-devsecops-set-pr-status}

Enable and configure status check of a pull request to know whether your commits meet the condition set in your repository at a glance.

The three *state of status* next to individual commits in your pull request are as follows:
- pending
- passing
- failing

You can use the [DevSecOps CLI](/docs/devsecops?topic=devsecops-cd-devsecops-cli) `set-status` command to set a GitHub or GitLab commit status.

For more information about this command, see the [DevSecOps CLI documentation](/docs/devsecops?topic=devsecops-cd-devsecops-cli#set-status).
{: shortdesc}

- Example for GitLab:

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

- Example for GitHub:

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

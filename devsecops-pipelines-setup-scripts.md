---

copyright:
  years: 2021
lastupdated: "2021-09-15"

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

# Setting up your pipeline scripts
{: #cd-devsecops-setup-pipeline-scripts}

You can use the DevSecOps CLI or the Git CLI to run operations on the inventory.
{: shortdesc}

## DevSecOps CLI
{: #cd-devsecops-setup-cli}

1. Create a promotion pull request from the master branch to the target staging branch.

```
cocoa inventory promote \
  --source="master" \
  --target="staging" \
  --priority="moderate" \
  --assigned-to="assignee@ibm.com" \
  --description="Change description" \
  --purpose="Change purpose" \
  --impact="Change impact" \
  --backout-plan="Details on backout and rollback")
  ```

1. Conclude a deployment by moving the `target_latest` tag to the same commit as the `pipeline-run-id` tag:

```
cocoa inventory label move \
  --to-label="${PIPELINE_RUN_ID}" \
  "target_latest"
```

## Git and GitHub CLI
{: #cd-devsecops-setup-gh}

1. Create a promotion pull request from the master branch to the target branch:

```
promote() {

  if [ -z $1 ] || [ -z $2 ]; then
    echo "Missing source and target"
    exit 1
  fi

  local source="$1"
  local target="$2"

  if ! git show-ref "refs/remotes/origin/$target"; then
    # Create a new target branch, from the beginning of master
    git checkout master
    git checkout -b "$target" $(git rev-list --max-parents=0 HEAD)
    git_push
  fi

  git checkout "$source"
  git pull --rebase

  # Create a promotion branch for the PR
  # this can be discarded after the Promotion PR merge
  git checkout -b "promote-$source-to-$target"
  git push --set-upstream origin "promote-$source-to-$target"

  # Create PR from promotion branch to target branch
  gh pr create \
    --base "$target" \
    --head "promote-$source-to-$target" \
    --title "Promote $source to $target" \
    --body "" \
    --repo "https://github.com/org/inventory-repository"

  # promotion branch can be deleted once the PR was merged
}

$ promote master staging
```

1. Conclude a deployment by moving the `target-latest` tag to the same commit as the `pipeline-run-id` tag:

```
conclude () {
  local target="$1"
  local tag="$2"

  latest="$1-latest"

  # remove the latest tag
  git push origin ":refs/tags/$latest"
  # find the commit hash of the target tag
  sha=$(git rev-list -n 1 $tag)
  
  # add the latest tag to the same commit of the target tag
  git tag -fa "$latest" -m "" $sha
  git push --tags --force
}

$ conclude staging pipeline-run-fe33b05c
```

1. Revert staging to an earlier state (using git and GitHub CLI):

```
revert () {
  local branch="$1"
  local commit="$2"
  
  # create a revert branch from the target branch
  git checkout "$branch"
  git pull --rebase
  git checkout -b "$branch-revert"

  # revert commits since the target commit, then commit and push
  git revert -n $(git rev-list --no-merges HEAD...$commit)
  git commit -m "revert $branch to $commit"
  git push --set-upstream origin "$branch-revert"

  # create PR from revert branch to the target branch
  gh pr create \
    --base "$branch" \
    --head "$branch-revert" \
    --title "Revert $branch to $commit" \
    --body "" \
    --repo "$REPO"

  # revert branch can be deleted once the PR was merged
}

$ revert staging ba3b8e5ed3320e6b4981077e1a1627f08de4f511
```

## Custom scripts

For information about using custom scripts in pipelines, see [Custom scripts](/docs/devsecops?topic=devsecops-custom-scripts).

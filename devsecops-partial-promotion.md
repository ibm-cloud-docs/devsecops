---

copyright: 
  years: 2024, 2024
lastupdated: "2024-04-08"

keywords: DevSecOps, IBM Cloud, inventory, partial promotion, manual promotion

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Promote selected Inventory entries manually
{: #cd-devsecops-inventory-partial-promotion}

The Inventory [Promotion pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-promotion-pipeline) provides a convenient and automated way to promote changes in the Inventory for deployment. This automation promotes all changed entries to a target environment.

There are use-cases where you need to promote only a selected subset of all the changes, and the provided automation cannot help you.

Currently, this is only possible using manual promotion. Inventory promotions are Pull Requests, so you need to create the promotion PR using git during a manual promotion.
{: shortdesc}

## Start with the target environment
{: #cd-devsecops-inventory-partial-promotion-target-environment-branch}

Create a new branch for the promotion PR **from the target environment**. In this example we name this branch to `manual-promote-1`

```bash
git checkout prod
git pull
git checkout -b manual-promote-1 prod
git push --set-upstream origin manual-promote-1
```

Now you have a branch, identical with the target - in our case `prod`.

## Cherry pick changes
{: #cd-devsecops-inventory-partial-promotion-cherry-pick}

By default, one-pipeline updates each inventory entry using individual commits. Therefore, each change should have a one-to-one connection to an inventory entry, which can help you during manual operations like the current one.

Take a look at your source environment branch, and find the commits you wish to select for promotion. If you have all the commits, cherry-pick them into the newly created promotion branch, and push them.

In our source environment branch (in our case `master`), we aim to promote the changes contained in `commit e9412b43a1ef8ccb39da642662a08da249496949`, which has already been merged into the source environment branch.

Depending on whether conflicts arise during the `cherry-pick` process, the following scenarios are possible:

**Scenario 1:** 
If there are no conflicts, the `cherry-pick` and `push` commands will function as follows.

```bash
git cherry-pick e9412b43a1ef8ccb39da642662a08da249496949
# [manual-promote-1 1d15d71] partial-promotion-to-prod-d4337c14
#  Author: <Name of Author> <EmailID of Author>
#  Date: Wed Apr 3 20:22:59 2024 +0530
#  1 file changed, 7 insertions(+), 7 deletions(-)

git push
```

**Scenario  2:** 
We have created the branch `manual-promote-1` from the target environment (in our case `prod`). Using `cherry-pick`, we are attempting to push changes from `commit e9412b43a1ef8ccb39da642662a08da249496949` to the target environment. This action might lead to conflicts. We must resolve these conflicts to ensure that the changes from `commit e9412b43a1ef8ccb39da642662a08da249496949` are successfully pushed to the target environment.

```bash
git cherry-pick e9412b43a1ef8ccb39da642662a08da249496949
# Auto-merging compliance-app-20240227083127123
# CONFLICT (content): Merge conflict in compliance-app-20240227083127123
# error: could not apply d4337c1... add build artifact compliance-app-20240227083127123 for 306eebe2694120b119bee6ef01218efd3efed8ab
# hint: After resolving the conflicts, mark them with
# hint: "git add/rm <pathspec>", then run
# hint: "git cherry-pick --continue".
# hint: You can instead skip this commit with "git cherry-pick --skip".
# hint: To abort and get back to the state before "git cherry-pick",
# hint: run "git cherry-pick --abort".

vi compliance-app-20240227083127123       #Resolve commit by retaining changes from commit e9412b43a1ef8ccb39da642662a08da249496949

git add compliance-app-20240227083127123

git cherry-pick --continue
# [manual-promote-1 1d15d71] partial-promotion-to-prod-d4337c14
#  Author: <Name of Author> <EmailID of Author>
#  Date: Wed Apr 3 20:22:59 2024 +0530
#  1 file changed, 7 insertions(+), 7 deletions(-)

git push
```

Now you can create the promotion PR with target environment (in our case `prod`) as base in the Inventory repo using the PR template provided. Fill out the [fields necessary for the Change Request](/docs/devsecops?topic=devsecops-cd-devsecops-automate-changemgmt#cd-devsecops-change-fields), then if you're ready, merge the PR, and trigger a deployment, just like during an automatic promotion.

![Partial Promotion PR Template](/images/partial-promotion-pr-template.png){: caption="Figure 1. Partial Promotion PR Template" caption-side="bottom"}


## Related information
{: #cd-devsecops-inventory-partial-promotion-related-information}

* [Documentation on how the Inventory Model works](/docs/devsecops?topic=devsecops-cd-devsecops-inventory)
* [Promotion pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-promotion-pipeline)

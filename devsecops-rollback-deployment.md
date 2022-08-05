---

copyright:
  years: 2022
lastupdated: "2022-08-05"

keywords: DevSecOps, inventory model, inventory, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Rolling back a deployment
{: #rollback-deployment}

Sometimes, a new deployment to an environment behaves abnormally and requires a rollback to a last known good version. In such cases, you can use the continuous deployment pipeline to deploy a previous version of the inventory to the target environment (for example, stage or prod).
{: shortdesc}

To roll back the deployment, complete these steps:

1. Select the version of the deployment (identified by commit-id) in the inventory to roll back to.
1. Revert the repository state to the identified commit.
1. Create a pull request to promote the reverted state.

The following commands show the scenario by using `git` commands:

1. List the commits and tags to identify the commit ID (version) to roll back to. For example, we identify `refs/tags/8` to be the most recent good deployment.

   ```bash
     # /c/usr/devsecops/compliance-inventory (master)
     $ git show-ref --tags
      09ce370c549a8313993ee143cbc9abc3127584c8 refs/tags/1
      09ce370c549a8313993ee143cbc9abc3127584c8 refs/tags/2
      09ce370c549a8313993ee143cbc9abc3127584c8 refs/tags/3
      0cc6ddd7dafffdc5a955a640e678633c2710b174 refs/tags/4
      0cc6ddd7dafffdc5a955a640e678633c2710b174 refs/tags/5
      0cc6ddd7dafffdc5a955a640e678633c2710b174 refs/tags/6
      83f7a87ee59185eaeac554bd3abeebfd2c1b4ad8 refs/tags/8
      0019d75d466bbdaba02ea979f3e094784a10b558 refs/tags/9
      1914a125e76aa97c497f4bd2c2f455b58cf079b8 refs/tags/10
      d9dd5d8553889ef24dff0678a3fcbae8aed3259b refs/tags/11
      1914a125e76aa97c497f4bd2c2f455b58cf079b8 refs/tags/prod_latest
   ```
     
1. Select the inventory state to revert to `refs/tags/8`. The following command lists all the versions or commits between the current state (`refs/tags/prod_latest`) and the last known good state (`refs/tags/8`).
     
   ```bash
     # /c/usr/devsecops/compliance-inventory (master)
     $ git rev-list --no-merges HEAD...83f7a87ee59185eaeac554bd3abeebfd2c1b4ad8
      67cc8babdff3e09c1f0e632f897798c1b5424f38
      6fab5ce3d60590cd858206424ecfd7d3a8c9ceb4
      22a575d48008299116ea426bdac45417d9df6238
      cb6f4d53c17f0c2554c039708989c403eb0ead18     
   ```
     
1. Revert the inventory state to `refs/tags/8`.
     
   ```bash
     # /c/usr/devsecops/compliance-inventory (master)
     $ git revert -n $(git rev-list --no-merges HEAD...83f7a87ee59185eaeac554bd3abeebfd2c1b4ad8)
   ```
     
1. Commit the new state of the inventory.

   ```bash
     # /c/usr/devsecops/compliance-inventory (master|REVERTING)
     $ git commit -m "revert master to 83f7a87ee59185eaeac554bd3abeebfd2c1b4ad8"
      [master af82538] revert master to 83f7a87ee59185eaeac554bd3abeebfd2c1b4ad8
      2 files changed, 19 insertions(+), 19 deletions(-)
      rewrite compliance-app (94%)
   ```
 
1. Push to the update to the master branch.
    
   ```bash
    # /c/usr/devsecops/compliance-inventory (master)
    $ git push --set-upstream origin master
      Enumerating objects: 7, done.
      Counting objects: 100% (7/7), done.
      Delta compression using up to 8 threads
      Compressing objects: 100% (4/4), done.
      Writing objects: 100% (4/4), 1.50 KiB | 766.00 KiB/s, done.
      Total 4 (delta 2), reused 0 (delta 0)
      To https://us-south.git.cloud.ibm.com/jaunin.b/compliance-inventory.git
        67cc8ba..af82538  master -> master
      Branch 'master' set up to track remote branch 'master' from 'origin'.
   ```
 
1. Create a pull request for the rollback promotion pull request.
1. Review the pull request and merge the pull request.
1. Trigger the **Manual CD** pipeline run within the CD Toolchain. 

The summary of steps that the CD Pipeline follows for forced redeployment include: 

1. The CD Pipeline starts and tags the current commit with the pipeline run ID.
2. The pipeline picks up the content of the corresponding environment branch from that tag.
3. The pipeline calculates the deployment delta between the current commit and the contents of the `<target-environment>_latest` tag.
4. A successful deployment concludes by attaching the `<target-environment>_latest` tag to the commit that you work with.

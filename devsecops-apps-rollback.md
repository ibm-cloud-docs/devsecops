---

copyright:
  years: 2021, 2022
lastupdated: "2022-03-21"

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

# Rolling back apps from production
{: #cd-devsecops-apps-rollback}

When you work with {{site.data.keyword.contdelivery_full}}, you might need to roll back a deployed app to a previous version on prod because of an issue.
{: shortdesc}

Choose a point in the Inventory to roll back to. All commits are reverted up to this point, and then promoted as a pull request. The following commands show how to do this by using the Git CLI: 

1. List the tags to identify the commit ID to roll back to. For example: `refs/tags/8`.  
 
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
     
1. You can choose the expected point to revert `refs/tags/8`. The following command lists the commit IDs from the chosen commit. This is for informational purposes only.
     
   ```bash
     # /c/usr/devsecops/compliance-inventory (master)
     $ git rev-list --no-merges HEAD...83f7a87ee59185eaeac554bd3abeebfd2c1b4ad8
      67cc8babdff3e09c1f0e632f897798c1b5424f38
      6fab5ce3d60590cd858206424ecfd7d3a8c9ceb4
      22a575d48008299116ea426bdac45417d9df6238
      cb6f4d53c17f0c2554c039708989c403eb0ead18     
   ```
     
1. The following command reverts in the inventory.
     
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
 
1. Push to the master branch.
    
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
 
1. Create a pull request for this Rollback promotion pull request.

The Rollback promotion pull request is merged and the continuous delivery pipeline starts. The pipeline picks up the content of the prod branch from that tag, calculates the deployment delta between the current commit and the contents of the `prod_latest` tag, and attempts to deploy. Successful deployment concludes by attaching the `prod_latest` tag to the commit that you work with.

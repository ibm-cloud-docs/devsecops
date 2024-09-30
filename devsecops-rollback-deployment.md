---

copyright:
  years: 2024
lastupdated: "2024-09-27"

keywords: DevSecOps, inventory model, inventory, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Rolling back a deployment
{: #devsecops-rollback-deployment}

 In DevSecOps, use either of the following methods to roll back a deployment:

 1. Rollback trigger by using rollback listener.

 1. Rollback by using raw GitOps

## Rollback Trigger
{: #devsecops-rollback-deployment-trigger}

 Use the rollback listener to trigger a rollback to a last known good version.

Follow the following steps to create a rollback trigger:

1. Go to the CD pipeline.
2. Add a manual trigger or duplicate the **Manual CD Trigger**.
3. Edit this trigger to set the listener to `cd-rollback-listener`.
4. Select **Save**.
5. Create triggers according to the region and target environment combinations.

A rollback pipeline takes in the following inputs as environment properties:

| Environment properties | Description |
|----------|---------|
| `rollback-change-request-id` | The change request ID of the concluded deployment that you want to rollback against. This is a mandatory field. |
| `rollback-limit` | The integer value specifies how many deployments back you can roll back. The default value is `1`, limiting rollback to the last completed deployment. |
| `region` | Specifies the region of the rollback. |
| `target-environment` | Specifies the target environment for the rollback. For example, stage or production.|
{: caption="Table 1. Types of environment properties" caption-side="bottom"}

The following criteria must be met for a rollback to occur, otherwise the pipeline is terminated:

* The `rollback-change-request-id` must be the change request ID of an older concluded deployment ID for the same `region` and `target-environment`.
* The `rollback-change-request-id` must not be older than `n` concluded deployments older than what's specified for `rollback-limit` value. The default value is `1`.
{: important}

### Overview of the Rollback pipeline
{: #devsecops-rollback-deployment-trigger}

The rollback pipeline contains the following stages:

 * prod-rollback-start
 *  prod-setup
 * prod-rollback-change-request
 * prod-deployment
 * prod-acceptance-tests
 * prod-rollback-finish

A rollback pipeline run uses information from the specified rollback changed request to create a new change request.

After deployment and acceptance testing, the pipeline reopens issues that are related to the previous rollback to reflect the current compliance status. Also, the candidate list of issues to be reopened after a rollback is captured as an attachment in the Change Request for further reference. Due dates, if there were any, remains unchanged from the original schedule.

A successful rollback pipeline deployment moves the `_latest` tag in the inventory to a previous commit.

To indicate that the pipeline is performing a rollback, the Tekton environment property `PIPELINE_NAME` must be set to `cd-rollback-pipeline`. If you are writing custom branching logic, use the variable to indicate whether a CD pipeline is deploying new code or rolling back a deployment.

 A tekton environment property `PIPELINE_NAME` is set to `cd-rollback-pipeline` to indicate whether a CD pipeline is getting deployed or performing a rollback. You can also use this property to write custom branching logic for rollback vs non-rollback.

`PIPELINE_NAME`, a tekton environment property determines a CD pipeline deployment or rollback. The default value `cd-rollback-pipeline` indicates whether the CD pipeline is deploying or rolling back. Customize branching logic using this property.

## Rollback using raw GitOps
{: #devsecops-rollback-deployment-gitops-raw}

 Use the continuous deployment pipeline to deploy a previous version of the inventory to the target environment.
{: shortdesc}

To roll back the deployment, complete these steps:

1. Select the version of the deployment (identified by `commit-id`) in the inventory to roll back to.
1. Revert the repository state to the identified commit.
1. Create a pull request to promote the reverted state.

   The following commands show the scenario by using `git` commands:

     1. List the commits and tags to identify the commit ID (version) to roll back to. as the latest stable deployment.

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
          {: codeblock}

     1. Select the inventory state before reverting to `refs/tags/8`. The following command lists all the versions or commits between the current state (`refs/tags/prod_latest`) and the last known good state (`refs/tags/8`).

          ```bash
              # /c/usr/devsecops/compliance-inventory (master)
              $ git rev-list --no-merges HEAD...83f7a87ee59185eaeac554bd3abeebfd2c1b4ad8
              67cc8babdff3e09c1f0e632f897798c1b5424f38
              6fab5ce3d60590cd858206424ecfd7d3a8c9ceb4
              22a575d48008299116ea426bdac45417d9df6238
              cb6f4d53c17f0c2554c039708989c403eb0ead18
          ```
           {: codeblock}

     1. Revert the inventory state to `refs/tags/8`.

          ```bash
              # /c/usr/devsecops/compliance-inventory (master)
              $ git revert -n $(git rev-list --no-merges HEAD...83f7a87ee59185eaeac554bd3abeebfd2c1b4ad8)
          ```
          {: codeblock}

     1. Commit the new state of the inventory.

          ```bash
              # /c/usr/devsecops/compliance-inventory (master|REVERTING)
              $ git commit -m "revert master to 83f7a87ee59185eaeac554bd3abeebfd2c1b4ad8"
              [master af82538] revert master to 83f7a87ee59185eaeac554bd3abeebfd2c1b4ad8
              2 files changed, 19 insertions(+), 19 deletions(-)
              rewrite compliance-app (94%)
          ```
          {: codeblock}

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
          {: codeblock}


1. Create a pull request for the rollback promotion pull request.
1. Review the pull request and merge the pull request.
1. Trigger the **Manual CD** pipeline run within the CD Toolchain.


Here's a summary of all  steps that the CD Pipeline follows for forced redeployment:

* The CD Pipeline starts and tags the current commit with the pipeline run ID.
* The pipeline picks up the content of the corresponding environment branch from that tag.
* The pipeline calculates the deployment delta between the current commit and the contents of the `<target-environment>_latest` tag.
* A successful deployment concludes by attaching the `<target-environment>_latest` tag to the commit that you work with.

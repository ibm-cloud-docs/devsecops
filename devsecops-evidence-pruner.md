---

copyright: 
  years: 2023
lastupdated: "2023-05-22"

keywords: DevSecOps, compliance evidence, IBM Cloud, evidence pruner

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Prune Evidence
{: #devsecops-prune-evidence}

Collecting evidence is one of the essential aspects of the DevSecOps reference architecture. Compliance evidence creates the audit trail that auditors look for during a compliance audit. One of the goals of DevSecOps is automated evidence generation and storage in auditable evidence lockers. Primary locker for the evidence is the Git Repository. As we keep generating alot of evidences, the repository size is keep on growing, so evidences which are very old needs to be cleaned up. This evidence pruner helps to  clean up all th evidences generated befoare a specific time. Cloud object sotrage buckets which is used to stored the evidences are recommended to have a retention policy of 365 days, Similiary the evidences in the Git are also recommended to clean up which are generated beofre 365 days.
{: shortdesc}

**Warning**: Rollback can be done till the point evidences are retained in the evidence locker, if you clean the evidence and then later tries to rollback to certain version then rollback wont be possible, you may need to rebuild the app on that commit on which you want to rollback.

### Parameters
{: #evidence-pruner-parm}

To run the Evidence pruner scan, the trigger needs the have following parameters:

|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|evidence-retention-days 		|Text 		|The number of days till which evidence should be retained.	Recommended to set as `365`|Required			|
|evidence-pruner-batch-size		|Text		|The number of evidence files which will be cleaned up before updating the Pull request.  Default is `1000`.	|Optional			|
|evidence-pruner-branch		|Text 		|The branch which will be created to clean up evidences and used to promote them in the default branch.	Default is `chore/prune-evidences`|Optional			|
|opt-in-v1-evidence-pruner		|Text		|If it is set to `1`, all the v1 evidence files will be cleaned up.	Default is `0`.|Optional			|
|opt-in-v2-evidence-pruner		|Text		|If it is set to `1`, all the v2 evidence files will be cleaned up, which are generated before `evidence-retention-days` days. Default is `0`.|Optional			|
{: caption="Table 1. Prune Evidence Trigger parameters}

### Trigger and Listener
{: #evidence-pruner-trigger}

To enable evidence pruner in one pipeline, take the following steps:
  
1. Go to the **Defintions** page of your CI or CD or CC pipeline.
2. Click the **Actions** icon ![Actions icon](../icons/actions-icon-vertical.svg) on the repository entry and click **Edit**.
3. Update the branch to `v10` or `v10-master` and click **Update**.
![Pipeline definitions](images/cd-devsecops-pipeline-definitions.png){: caption="Figure 1. pipeline definitions" caption-side="bottom"}

If the toolchain is already created, then you can create a new trigger usig below steps, else you will find these trigger in the Trigger section:
1. Go to the **Triggers** page of your CI or CD or CC pipeline and click **ADD**.
2. Provide a name to the trigger, Select the evidence listener as `prune-evidence-listener`, provide the required proerty `evidence-retention-days` as the number of days to retain the evidences. and click on **Save**

For more information about Tekton pipeline triggers, see [Working with Tekton pipelines](/docs/ContinuousDelivery?topic=ContinuousDelivery-tekton-pipelines).

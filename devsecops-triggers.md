---

copyright: 
  years: 2021, 2023
lastupdated: "2023-08-16"

keywords: DevSecOps, triggers

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Triggers
{: #cd-devsecops-triggers}

DevSecOps continuous integration and continuous deployment pipelines provide a set of default triggers.
{: shortdesc}

|Pipeline |Name	|Description |State |
|:----------|:------------------------------|:------------------|:----------|
|Pull request		|Git PR Trigger		|Runs the pull request pipeline on pull requests within the Application Repository.			|Enabled			|
|Pull request 		|Manual Trigger 		|Runs the pull request pipeline on a specified commit or branch, on demand.			|Enabled			|
|Promotion 		|Git Promotion Validation Trigger 		|Runs the promotion validation pipeline when a pull request is opened/updated on the `target-environment` branch of the Inventory.	|Disabled			|
|Continuous integration		|Git CI Timed Trigger		|Runs the continuous integration pipeline on the `master` branch of the Application Repository every day at 4 AM. |Disabled			|
|Continuous integration		|Git CI Trigger		|Runs the continuous integration pipeline when a commit is pushed to the master branch of the Application Repository.	|Enabled			|
|Continuous integration		|Manual Trigger 		|Runs the continuous integration pipeline on a specified commit or branch, on demand.		|Enabled			|
|continuous deployment		|Git CD Timed Trigger		|Runs the continuous deployment pipeline every day at 4 AM.	|Disabled			|
|continuous deployment		|Git CD Trigger		|Runs the continuous deployment pipeline when a commit is pushed to the `target-environment` branch of the Inventory.	|Disabled			|
|continuous deployment		|Manual CD Trigger		|Runs the continuous deployment pipeline on the `target-environment` branch of the Inventory, on demand.	|Enabled			|
|continuous deployment		|Manual Promotion Trigger		|Runs the promotion pipeline to promote inventory changes from the `source-environment` branch to the `target-environment` branch.	|Enabled			|
|Continuous compliance		|Git CC Timed Trigger		|Runs the continuous compliance pipeline every day at 4 AM.	|Enabled			|
|Continuous compliance		|Manual CC Trigger		|Runs the continuous compliance pipeline on the `environment-tag` tag of the Inventory, on demand.	|Enabled			|
|Continuous integration, continuous deployment, continuous compliance		|Evidence Pruner Timed Trigger		|Runs the pipeline every day at 2AM to clean up all the old evidence generated before `evidence-retention-days` days.	|Disabled by default.			|
|Continuous integration, continuous deployment, continuous compliance		|Evidence Pruner Manual Trigger 	|Runs the pipeline to clean up all the old evidence generated before `evidence-retention-days` days. Runs on demand.	|Enabled			|
{: caption="Table 1. Triggers" caption-side="top"}

You can [add triggers](/docs/devsecops?topic=devsecops-cd-devsecops-add-pipeline-triggers) to the set of default triggers.
{: tip}

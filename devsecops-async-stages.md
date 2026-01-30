---

copyright:
  years: 2022
lastupdated: "2022-03-29"

keywords: DevSecOps, async sub pipelines, async stages, ibm cloud

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

# Async, parallel stage runs
{: #devsecops-async-stages}

You can run a pipeline stage from the pipeline configuration YAML as an async stage in a lightweight parallel pipeline.
{: shortdesc}

This feature includes the following potential benefits:

* Optimized pipeline run time: You can run time-consuming stages in a parallel "process," while the parent pipeline might process other stages without awaiting the results of the triggered async stage.
* Improved pipeline resiliency: By using the rerun feature of Tekton pipelines, you can rerun a failed stage and keep the state from its first initialization. This feature might be useful to remediate transient infrastructural errors, like network timeouts.

## Glossary
{: #devsecops-async-stages-glossary}

async stage
:   A custom pipeline stage defined in the pipeline config YAML, running asynchronously along with the `/parent pipeline/` run.

parallel pipeline
:   A lightweight pipeline that runs a single async stage along with the `/parent pipeline/` run.

parent pipeline
:   The pipeline from the async stage that was initiated.

exported parameter or variable
:   A value that is saved for the pipeline run by using the `set_env` command of [`pipelinectl`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#set_env). The value is exported for async pipeline runs by using the `export_env` command.

## Capabilities and gaps
{: #async-stages-features}

One stage per triggered parallel run
:   Each parallel pipeline that runs an async stage can run only one stage. Currently, it’s not possible to run more than one in a single parallel pipeline. However, any number of stages can be created.

Serialized data
:   Data that is available in the parent pipeline run and is required for the async stage must be exported for serialization before the stage is triggered.

One-way data flow
:   While it is possible to export data to async stages from the parent pipeline run, sending data back directly to the parent pipeline run is not possible.

Existing stages only
:   Only stages in the pipeline configuration YAML can be loaded into async stages. It is not possible to create new stages runtime, and the trigger command fails if a stage does not exist in the pipeline configuration YAML.

Rerun async stages with the initial state
:   Rerunning a failed async stage repeats the original triggered state (including parameters and payloads). This is a feature of Tekton that we’re taking advantage of.

## Relying on existing pipeline features
{: #async-stages-using-tekton}

This feature relies on features that are provided by IBM Cloud CD and Tekton pipelines:

generic webhook triggers
:   The parallel pipeline is triggered by using a generic webhook trigger set to the same pipeline as the parent.

payloads
:   Serialized data is passed to the parallel pipeline run by using the payload of the generic webhook trigger.

pipeline API
:   The parallel pipeline webhook trigger and the parameters that are required to use it are set up through the Pipeline API.

pipeline secrets and variables
:   The webhook secret and other variables that are needed by the webhook are stored as pipeline variables.

## Setup and trigger steps
{: #async-stages-setup-triggers}

Setup and triggers occur in the following steps:

1. Check whether the webhook trigger already exists. on the pipeline. If not, the script adds the webhook trigger and its parameters by using the Pipeline API.
2. Check whether the webhook secrets are available. If not, the script updates the secrets by using the Pipeline API.
3. Data is serialized in a JSON payload.
4. The webhook is called, which sends the payload.
5. Register the triggered run in the parent pipeline and list at the end of the pipeline run.

## Data serialization and transfer
{: #async-stages-data-serialization}

Data is available in every triggered async stage by default:

* Every parameter of the pipeline UI is also available in the async stage by using the `get_env` [`pipelinectl`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#get_env) command.
* The parent pipeline serializes and sends the following data every time:

	* Registered repos (added with `save_repo`)
	* Registered artifacts (added with `save_artifact`)
	* Pipeline debug mode
	* Pipeline name
	* Pipeline type (CI, CD, CC, or PR)
	* The pipeline configuration (repository information on a commit level)

Any data that is necessary to run the stage must be marked for export before the async stage is triggered. This means not just data created run-time in the parent pipeline run, but any data that is passed to the parent pipeline run as a trigger parameter. 

To mark pipeline data for export and serialization, make sure that the data is added to the pipeline run by using the `set_env` [`pipelinectl`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#set_env) command, and mark them for export by using the `export_env` command.

While it is possible to export data to async stages from the parent pipeline run, sending data back directly to the parent pipeline run is impossible at the moment.
{: important}

## The environment of the async stage
{: #async-stages-environment}

The initialization of the async stage sets up an environment for the stage scripts to be as usable as possible.

* Exported data is de-serialized and loaded, so scripts can access them using `get_env`.
* Git repos that are registered in the parent pipeline run are cloned on the commit level.

The created environment does not contain any changes that you made on the workspace in the parent pipeline run (installing dependencies, building binaries, and so on). The new pipeline run does not share the workspace with its parent.
{: important}

## Related information
{: #devsecops-async-stages-related}

For more information, see the following topics:
- [Updating the async stage webhooks](/docs/devsecops?topic=devsecops-update-async-webhook)
- [Triggering a stage by using async sub pipelines](/docs/devsecops?topic=devsecops-devsecops-async-sub-pipelines)

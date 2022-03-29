---

copyright:
  years: 2022
lastupdated: "2022-03-29"

keywords: DevSecOps, async stage webhooks, ibm cloud

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

# Updating the async stage webhooks
{: #update-async-webhook}

If you need to update the async webhook token in the pipeline for some reason (for example secret token update), take the following steps:
{: shortdesc}

1. Find the async webhook trigger, it is added to the triggers of your pipeline, named "Subpipeline Webhook Trigger"
1. Update the "Secret" value of the trigger, and copy its value and save the trigger
1. In the Pipeline Environment properties, set the new value to the secret `subpipeline-webhook-token`

The pipeline run now picks up the new secret value on the next new run, and will trigger your async stages.
{: important}

The trigger name "Subpipeline Webhook Trigger" and the header name `x-async-stage-token` in the trigger are fixed, do not change these.

The pipeline variable `subpipeline-webhook-token` is also fixed. The triggering scripts will look for its content.

You can change the webhook URL and the worker of the trigger as well, if necessary, also any trigger parameters added to the Subpipeline Webhook Trigger will be available in the async stage.
{: note}

## Related information
{: #devsecops-async-webhook-related}

For more information, see the following topics:
- [Triggering a stage by using async sub pipelines](/docs/devsecops?topic=devsecops-devsecops-async-sub-pipelines)
- [Async, parallel stage runs](/docs/devsecops?topic=devsecops-devsecops-async-stages)

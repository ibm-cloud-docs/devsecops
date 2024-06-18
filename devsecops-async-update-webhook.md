---

copyright:
  years: 2022, 2023
lastupdated: "2023-01-16"

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

To update the async webhook token in the pipeline, for example, to update a secret token, complete the following steps:
{: shortdesc}

1. Search for the async webhook trigger.
    Your async webhook trigger is added to the triggers of your pipeline, named `Subpipeline Webhook Trigger`. Therefore, we must generate a new secret key from this trigger to use it with the async webhook trigger. {: note}

1. Update the "Secret" value of the trigger. Copy this value and save the trigger.
1. In your desired pipeline's **Environment properties**, replace the value with the new generated secret value (value copied in the previous step ) to the secret `subpipeline-webhook-token`.

The pipeline run now picks up the new secret value on the next new run, and triggers your async stages. The triggering scripts searches in the content.

Do not change the following default varaiable names :
- Trigger name "Subpipeline Webhook Trigger" and the
- Header name `x-async-stage-token` in the trigger
-  pipeline variable `subpipeline-webhook-token`
{: important}

You can change the webhook URL and the worker of the trigger as well, if necessary, also any trigger parameters added to the Subpipeline Webhook Trigger will be available in the async stage.

## Related information
{: #devsecops-async-webhook-related}

For more information, see the following topics:
- [Triggering a stage by using async sub pipelines](/docs/devsecops?topic=devsecops-devsecops-async-sub-pipelines)
- [Async, parallel stage runs](/docs/devsecops?topic=devsecops-devsecops-async-stages)

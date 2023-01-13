---

copyright:
  years: 2021, 2022
lastupdated: "2022-09-26"

keywords: DevSecOps

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Adding triggers to pipelines
{: #cd-devsecops-add-pipeline-triggers}

DevSecops continuous integration and continuous deployment pipelines provide a set of [default triggers](/docs/devsecops?topic=devsecops-cd-devsecops-triggers). You can add triggers or duplicate existing triggers to pipelines from the Pipeline UI.
{: shortdesc}

For more information about Tekton pipeline triggers, see [Working with Tekton pipelines](/docs/ContinuousDelivery?topic=ContinuousDelivery-tekton-pipelines).

You can access the event payload JSON in the `.pipeline-config.yaml` stages by using the `$TRIGGER_PAYLOAD` environment variable. For more information about webhook payloads, see [Accessing arguments from webhook payloads](/docs/devsecops?topic=devsecops-cd-devsecops-webhook-payloads).
{: tip}

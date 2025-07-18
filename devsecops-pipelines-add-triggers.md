---

copyright:
  years: 2021, 2022
lastupdated: "2022-08-02"

keywords: DevSecOps

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Adding triggers to pipelines
{: #cd-devsecops-add-pipeline-triggers}

DevSecops continuous integration and continuous deployment pipelines provide a set of [default triggers](/docs/devsecops?topic=devsecops-cd-devsecops-triggers). You can add triggers or duplicate existing triggers to pipelines from the Pipeline UI. Refer to **Step 6** of [Configuring a Delivery Pipeline for Tekton by using the console](/docs/ContinuousDelivery?topic=ContinuousDelivery-tekton-pipelines&interface=ui#configure_tekton_pipeline)
{: shortdesc}


For more information about Tekton pipeline triggers , see [Working with Tekton pipelines](/docs/ContinuousDelivery?topic=ContinuousDelivery-tekton-pipelines).

# Triggering pipelines using CLI or API
{: #cd-devsecops-trigger-pipeline-cli}

You can trigger a pipeline by using either the IBM Cloud CLI or the API. With the CLI, you initiate the pipeline by specifying the required toolchain and pipeline identifiers. Alternatively, the API allows you to trigger a pipeline run by sending a POST request with the appropriate authentication headers and payload.

For more information , see [Using triggers](/docs/ContinuousDelivery?topic=ContinuousDelivery-tekton#using-triggers)

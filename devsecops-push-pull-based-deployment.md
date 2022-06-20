---

copyright:
  years: 2022
lastupdated: "2022-06-20"

keywords: DevSecOps, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Deployment models
{: #deployment-model}

Deployment models help you to understand your application deployment process across clusters.
{: shortdesc}

## Push-based deployment
{: #deployment-model-push}

The push-based deployment model deploys your application on a single cluster. It does not require any operator installation on your cluster. 

For a push-based deployment, complete these steps:

1. Log in to your Kubernetes or {{site.data.keyword.openshiftlong_notm}} cluster.
2. Apply the deployment files by using the native [kubectl](https://kubernetes.io/docs/reference/kubectl/kubectl/){: external} tool.

## Pull-based deployment
{: #deployment-model-pull}

The pull-based deployment model is suitable for multi-cluster application deployments. This model uses {{site.data.keyword.satellitelong_notm}} Config as the deployment tool for deploying across multiple clusters. For more information, see [Understanding {{site.data.keyword.satelliteshort}} Config](/docs/satellite?topic=satellite-cluster-config).

For a pull-based deployment, complete these steps:

1. Create or update the required {{site.data.keyword.satelliteshort}} config and their versions for the deployment files.
2. Create or update the {{site.data.keyword.satellitelong_notm}} subscriptions with {{site.data.keyword.satelliteshort}} config and the versions that need to be deployed.
3. After the subscriptions are updated, the Razee agent that is installed on the clusters pulls the most recent deployments from {{site.data.keyword.satelliteshort}} config and deploys them across the clusters.

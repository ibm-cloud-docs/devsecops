---

copyright:
  years: 2022, 2026
lastupdated: "2026-10-26"

keywords: DevSecOps, IBM Cloud, push-based deployment, pull-based deployment, kubectl, Helm, Satellite Config

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Deployment models
{: #deployment-model}

Deployment models help you to understand your application deployment process across clusters.
{: shortdesc}

## Push-based deployment
{: #deployment-model-push}

The push-based deployment model deploys your application on a single cluster. The deployment pipeline connects to the cluster and applies the deployment files. This model does not require any operator or agent installation on your cluster.

You can use any deployment tool that can run from the deployment pipeline against the target cluster, for example:

- [kubectl](https://kubernetes.io/docs/reference/kubectl/kubectl/){: external} (or `oc` for {{site.data.keyword.openshiftlong_notm}}) to apply Kubernetes manifests.
- [Helm](https://helm.sh/docs/){: external} to install or upgrade a chart release. The chart can be stored in the inventory repository, like any other deployment asset.

The deployment scripts are part of the deployment repository, and you can customize them to fit your application and cluster requirements. DevSecOps provides helper scripts refered to as [commons library](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-commons#deploy-stage---tools) that cover deployment actions.

## Pull-based deployment
{: #deployment-model-pull}

The pull-based deployment model is suitable for multi-cluster application deployments. This model uses {{site.data.keyword.satellitelong_notm}} Config as the deployment tool for deploying across multiple clusters. For more information, see [Understanding {{site.data.keyword.satelliteshort}} Config](/docs/satellite?topic=satellite-cluster-config).

The deployment pipeline does not connect to each cluster. Instead, it publishes the deployment files to {{site.data.keyword.satelliteshort}} Config, and an agent that runs on each cluster retrieves them. The clusters that receive the same deployment are grouped in a {{site.data.keyword.satelliteshort}} cluster group.

{{site.data.keyword.satelliteshort}} Config deploys Kubernetes resource definitions. If your application is packaged as a Helm chart or as Kustomize overlays, render it into Kubernetes manifests first, for example with `helm template` or `kustomize build`, and then upload the rendered manifests as the configuration version.

For a pull-based deployment, complete these steps:

1. Create or update the required {{site.data.keyword.satelliteshort}} configurations and their versions for the deployment files.
2. Create or update the {{site.data.keyword.satelliteshort}} Config subscriptions with the {{site.data.keyword.satelliteshort}} configurations and the versions that need to be deployed.
3. After the subscriptions are updated, the Razee agent that is installed on the clusters pulls the most recent deployments from {{site.data.keyword.satelliteshort}} Config and deploys them across the clusters.

## Choosing a deployment model
{: #deployment-model-choose}

| Consideration | Push-based deployment | Pull-based deployment |
|---------------|-----------------------|-----------------------|
| Target | A single cluster | A group of clusters |
| Deployment tool | `kubectl`, `oc`, Helm, Kustomize, or any tool that runs in the pipeline | {{site.data.keyword.satelliteshort}} Config |
| Component on the cluster | None | {{site.data.keyword.satelliteshort}} Config agent |
| Cluster access | The pipeline needs credentials to the cluster | The agent on the cluster retrieves the deployment, and the pipeline needs access only to {{site.data.keyword.satelliteshort}} Config |
| Helm charts | Installed or upgraded directly with Helm | Rendered to manifests before upload |
{: caption="Comparison of deployment models" caption-side="bottom"}

For more information about selecting the deployment target, see [Deploying your application on a target](/docs/devsecops?topic=devsecops-custom-deployment-target).

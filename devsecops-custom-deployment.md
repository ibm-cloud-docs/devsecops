---

copyright:
  years: 2023, 2025
lastupdated: "2025-02-05"

keywords: DevSecOps, COS, secure toolchain, compliance, ibm cloud, cloud object storage, satellite , Push based deployment , Pull based deployment

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Deploying your application on a target
{: #custom-deployment-target}

You can deploy your application on one of three targets: Push-based deployment, Pull-based deployment, or a custom target.
{: shortdesc}

## Deploying using Push-based deployment
{: #custom-deployment-push-based-deployment}

You can select `Push based deployment` as the deployment target when you want to deploy your application on Red Hat&reg; OpenShift&reg; on {{site.data.keyword.cloud_notm}}. [DevSecOps](#x9892260){: term} provides scripts to deploy your application on the cluster. You might need to customize these scripts according to your application and cluster requirements.

These scripts are located in the deployment repo and are specified in the `pipeline-config.yml` file.

```bash
deploy:
  image: icr.io/continuous-delivery/pipeline/pipeline-base-image:2.12@sha256:ff4053b0bca784d6d105fee1d008cfb20db206011453071e86b69ca3fde706a4
  script: |
    #!/usr/bin/env bash

    if [[ "$PIPELINE_DEBUG" == 1 ]]; then
      trap env EXIT
      env
      set -x
    fi

    source scripts/deploy_setup.sh
    source scripts/deploy.sh
    export DEPLOY_EXIT=$?
    source scripts/doi-publish-deploy.sh
```
{: codeblock}

## Deploying using Pull-based deployment
{: #custom-deployment-target-pull-based-deployment}

DevSecOps provides an option to deploy your application using Pull-based deployment using {{site.data.keyword.satellitelong_notm}} Config.

[{{site.data.keyword.satellitelong_notm}}](https://www.ibm.com/products/satellite){: external} is an extension of the {{site.data.keyword.cloud_notm}} Public that can run inside the customer's data center or out at the edge. Each {{site.data.keyword.satellitelong_notm}} location is connected by using {{site.data.keyword.satellitelong_notm}} Link, which provides the connection to the {{site.data.keyword.cloud}} control plane. It provides audit, packet capture, and visibility to the security team, while a configuration utility provides a global view of applications and services. {{site.data.keyword.satellitelong_notm}} Link provides a simple way to manage the connection between {{site.data.keyword.cloud_notm}} and the {{site.data.keyword.satelliteshort}} location with visibility into all the traffic that goes back and forth with control of the endpoints on both sides of the link.

Users can have groups of Red Hat&reg; OpenShift&reg; clusters in a [{{site.data.keyword.satelliteshort}} cluster group](/docs/satellite?topic=satellite-setup-clusters-satconfig). You can use [{{site.data.keyword.satelliteshort}} Config](/docs/satellite?topic=satellite-cluster-config) to deploy the application into a [{{site.data.keyword.satelliteshort}} cluster group](/docs/satellite?topic=satellite-setup-clusters-satconfig).

Follow a two-step process to enable your Red Hat&reg; OpenShift&reg; clusters to leverage {{site.data.keyword.satelliteshort}} Config to perform simultaneous deployments.

1. [Create a cluster group](/docs/satellite?topic=satellite-setup-clusters-satconfig-groups), which is a logical grouping of clusters that run on a {{site.data.keyword.satelliteshort}} location.

2. [Attach a cluster to a cluster group](/docs/satellite?topic=satellite-setup-clusters-satconfig#auto-setup-clusters-satconfig), which grants {{site.data.keyword.satelliteshort}} Config access to manage the resources within the cluster.

As an advanced configuration, you can also deploy the application to the compute infrastructure in your on-premises data center or other cloud providers by using [{{site.data.keyword.satellitelong_notm}}](/docs/satellite?topic=satellite-getting-started).

1. [Create a {{site.data.keyword.satelliteshort}} location](/satellite/locations/create){: external} for the compute infrastructure in your on-premises data center or other cloud providers by using predefined templates.

2. [Deploy a Red Hat&reg; OpenShift&reg; cluster](/kubernetes/catalog/create?platformType=openshift){: external} to the {{site.data.keyword.satelliteshort}} location by using setup.

3. [Create a cluster group](/docs/satellite?topic=satellite-setup-clusters-satconfig-groups), which is a logical grouping of clusters that run on a {{site.data.keyword.satelliteshort}} location.

4. [Attach a cluster to a cluster group](/docs/satellite?topic=satellite-setup-clusters-satconfig#auto-setup-clusters-satconfig), which grants {{site.data.keyword.satelliteshort}} Config access to manage the resources within the cluster.

### Prerequisites
{: #custom-deployment-target-prereqs}

* This toolchain uses [{{site.data.keyword.satelliteshort}} Config](/docs/satellite?topic=satellite-cluster-config) to deploy an application to a group of clusters.
* This toolchain assumes that you have a [{{site.data.keyword.satelliteshort}} cluster group](/docs/satellite?topic=satellite-setup-clusters-satconfig) with the required clusters.
* This toolchain supports a {{site.data.keyword.satelliteshort}} cluster group that contains only one type of cluster (Red Hat&reg; OpenShift&reg;).

### Deployment
{: #custom-deployment-target-scripts}

DevSecOps provides out of the box scripts to deploy your application on the group of clusters. You might need to customize these scripts according to your application and cluster group requirements.

These scripts are located in the deployment repository and are specified in the `pipeline-config.yml` file.

```bash
deploy:
  image: icr.io/continuous-delivery/pipeline/pipeline-base-image:2.12@sha256:ff4053b0bca784d6d105fee1d008cfb20db206011453071e86b69ca3fde706a4
  script: |
    #!/usr/bin/env bash

    if [[ "$PIPELINE_DEBUG" == 1 ]]; then
      trap env EXIT
      env
      set -x
    fi

    source scripts/deploy_setup.sh
    source scripts/deploy.sh
    export DEPLOY_EXIT=$?
    source scripts/doi-publish-deploy.sh
```
{: codeblock}

## Deploying to a custom target
{: #custom-deployment-target-custom}

Deploy to a custom target if you want to:

* Deploy your application on your own choice of infrastructure, such as Virtual Server Instances (VSI).
* Perform custom tasks, such as updating some configurations in a Red Hat&reg; OpenShift&reg; cluster.

In these cases, select "custom" as a deployment target.

### Performing a custom deployment
{: #custom-deployment-target-how}

* DevSecOps templates are fully customizable. You can provide your own stages and steps in the `pipeline-config.yml` file of the deployment repository.
* You can provide your custom scripts in the `setup`, `deploy`, and `acceptance-test` stages in the `pipeline-config.yml` file.
* These scripts are run during the continuous deployment (CD) pipeline run.

---

copyright:
  years: 2021
lastupdated: "2021-09-03"

keywords: troubleshoot, DevSecOps

subcollection: devsecops

---

{:tsSymptoms: .tsSymptoms}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:note:.deprecated}
{:tip: .tip}
{:important: .important}
{:troubleshoot: data-hd-content-type='troubleshoot'}
{:support: data-reuse='support'}

# Troubleshooting for DevSecOps
{: #troubleshoot-devsecops}

## General troubleshooting methods
{: #troubleshoot-general}
{: troubleshoot}
{: support}

* Reload the page in case the UI is slow or the logs fail to load.
* Check for outages on the [status page](https://cloud.ibm.com/status){: external}
* Run the pipeline again.

  ![Run the pipeline again](images/rerun.png)

## Issues related to the IBM environment
{: #troubleshoot-ibm-env}
{: troubleshoot}
{: support}

### The check-registry step of the containerize task fails with error.

![Storage quota error](images/exceeded-quota.png)

IBM Cloud registry offers a limited quota, pushing too many images it can be exceeded.

Go to images, delete some and rerun the pipeline.
You can check your quota limits and usage by issuing ibmcloud cr quota command.

### Logs do not show for step.

![Logs do not show](images/no-logs.png)

This is an issue with the tekton environment.

Try reloading the page.
Download the logs using the download button.

![Download logs](images/download-logs.png)

## Issues related to template and pipeline
{: #troubleshoot-template-pipeline}
{: troubleshoot}
{: support}

### Task is cancelled because base image cannot be accessed.

![Base image is not accessed](images/artifactory-bad-credentials.png)

Check if your artifactory credentials are correct. A new artifactory token can be created here.
You can create a secret manually by running:

```bash
kubectl create secret docker-registry mysecret \
--dry-run \
--docker-server=wcp-compliance-automation-team-docker-local.artifactory.swg-devops.com  \
--docker-username=<username> \
--docker-password=<artifactory token> \
--docker-email=<email> \
-o yaml
```

It will output something similar:

```bash
apiVersion: v1
data:
  .dockerconfigjson: <your secret>
kind: Secret
metadata:
  creationTimestamp: null
  name: regcred
type: kubernetes.io/dockerconfigjson
```

In the pipeline properties, update the `artifactory-dockerconfigjson` parameter with the .dockerconfigjson value.

![Update artifactory-dockerconfigjson](images/artifactory-update-credentials.png)

For more information, check out the [kubectl documentation on creating a secret](https://kubernetes.io/docs/concepts/configuration/secret/)(: external).

## Image signing issues
{: #troubleshoot-signing}
{: troubleshoot}
{: support}

If your image signing tasks fails, refer to the [image signing documentation](/docs/devsecops?topic=devsecops-cd-devsecops-image-signing) to verify that the signing key was correctly generated and stored.

## Troubleshooting Continuous Delivery
For more general issues related to the Continuous Delivery service, refer to the {{site.data.keyword.deliverypipeline}} [troubleshooting page](/docs/ContinuousDelivery?topic=ContinuousDelivery-troubleshoot_delivery_pipeline)

## Getting help from the community
Not finding your answer, solution? Get help fast directly from the IBM Cloud development teams and other users.

The DevSecOps team actively monitors our Slack channel for questions about the DevSecOps, {{site.data.keyword.contdelivery_short}} services and toolchains.

| Get Help | Ask Us a Question |
|----------|---------|
| Reach out to us on our Slack channel | <a class="xref" href="https://ic-devops-slack-invite.us-south.devops.cloud.ibm.com/" target="_blank" title="(Opens in a new tab or window)"><img class="image" src="images/slack_us.png" width="148" style="width:148px; border-style: none" alt="Slack us"/></a> |
{: caption="Help and support for {{site.data.keyword.contdelivery_short}}" caption-side="top"}

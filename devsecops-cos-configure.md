---

copyright:
  years: 2021
lastupdated: "2021-09-15"

keywords: DevSecOps, COS, secure toolchain, compliance

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

# Configuring {{site.data.keyword.cos_full_notm}} for storing evidence
{: #cd-devsecops-cos-config}

Complete these steps to create a {{site.data.keyword.cos_full_notm}} instance and {{site.data.keyword.cos_full_notm}} bucket to store evidence that is used by the {{site.data.keyword.cloud_notm}} DevSecOps continuous integration pipeline.
{: shortdesc}

## Create a {{site.data.keyword.cos_short}} instance
{: #cd-devsecops-cos-instance-create}

Create a [{{site.data.keyword.cos_short}} instance](https://cloud.ibm.com/catalog/services/cloud-object-storage){: external}.

## Create a {{site.data.keyword.cos_short}} bucket
{: #cd-devsecops-cos-bucket-create}

In {{site.data.keyword.cos_short}}, files are grouped in buckets. (You could think of buckets like directories, except there are no subdirectories in buckets.) 

1. In the {{site.data.keyword.cos_short}} GUI, click **Buckets**.
2. Click **Create bucket**.
3. Give the bucket a name that is unique and helpful for remembering what the bucket is being used for (over time, you might create many buckets!)
4. Set Resiliency to "Regional." In general, regional resiliency will have the best performance with the lowest cost. However if the ability to survive a regional outage is essential to you, set the resiliency to "Cross Region."
5. For best performance, set Location to the same location as your Toolchain location.
6. Usually, the default for Storage class is suitable for use and usually, you won't need to use **ADVANCED CONFIGURATION**.

## Create a service credential
{: #cd-devsecops-cos-service-credential}

1. In the {{site.data.keyword.cos_short}} GUI, click **Service Credential**.
2. Click **New Credential**.
3. Provide the details:
   *  **Name:** Name of the credential
   *  **Role:** Role for the credential. (**writer** role is suggested for uploading the file)

## Provide bucket access to the service credential
{: #cd-devsecops-cos-bucket-access}

1. In the {{site.data.keyword.cos_short}} GUI, click **Buckets**.
2. Select the bucket which was created in earlier step.
3. Click on the **Access policies** and select the **Service IDs** option
4. Now select the service credential which was created in earlier step and select the **writer** role.
5. Now click in the **Create Access Policy** Button.

## Copy the API Key of the service credential
{: #cd-devsecops-cos-apikey-copy}

1. In the {{site.data.keyword.cos_short}} GUI, click **Service Credential**.
2. Now click on the **expand** the service credential. This will show you the **apikey**. 
3. Note down the *apikey*

## Endpoints
{: #cd-devsecops-cos-endpoints}

1. In the {{site.data.keyword.cos_short}} GUI, click **Buckets**.
2. Select the bucket which was created in earlier step.
3. Now select **Configuration** tab and select the **Endpoints**.
4. Note down the **public endpoint**.

## Related information
{: #cd-devsecops-cos-related-content}

* [{{site.data.keyword.cos_short}} buckets as evidence locker](/docs/devsecops?topic=devsecops-cd-devsecops-cos-bucket-evidence)
* [{{site.data.keyword.cos_short}} evidence](/docs/devsecops?topic=devsecops-cd-devsecops-evidence#cd-devsecops-lockers-cos)

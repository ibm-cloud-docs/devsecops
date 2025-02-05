---

copyright: 
  years: 2021, 2025
lastupdated: "2025-02-05"

keywords: DevSecOps, COS, secure toolchain, compliance, bucket

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Configuring {{site.data.keyword.cos_full_notm}} for storing evidence
{: #cd-devsecops-cos-config}

Complete these steps to create an {{site.data.keyword.cos_full_notm}} instance and {{site.data.keyword.cos_full_notm}} bucket to store evidence that is used by the {{site.data.keyword.cloud_notm}} DevSecOps pipelines.
{: shortdesc}

## Create a Cloud {{site.data.keyword.cos_short}} instance
{: #cd-devsecops-cos-instance-create}

Create an [{{site.data.keyword.cos_full_notm}} instance](https://cloud.ibm.com/objectstorage/create){: external}.

## Create a Cloud {{site.data.keyword.cos_short}} bucket
{: #cd-devsecops-cos-bucket-create}

In Cloud {{site.data.keyword.cos_short}}, files are grouped in buckets. You might think of buckets like directories, except that no subdirectories are in buckets.

1. In Cloud {{site.data.keyword.cos_short}}, click **Buckets**.
2. Click **Create bucket**.
3. Choose the **Customize your bucket** option.
4. Give the bucket a unique, memorable name to help you remember what the bucket is for. Over time, you might create many buckets.
5. Set Resiliency to **Regional**. Regional resiliency has the best performance with the lowest cost. However, if the ability to survive a regional outage is essential, set the resiliency to "Cross Region."
6. For best performance, set **Location** to the same location as your toolchain location.
7. Usually, the default for Storage class is suitable for use, so you don't need to use **ADVANCED CONFIGURATION**.

## Create a service credential
{: #cd-devsecops-cos-service-credential}

1. In Cloud {{site.data.keyword.cos_short}}, click **Service Credential**.
2. Click **New Credential**.
3. Select **Include HMAC Credential** to include HMAC keys in the credential.
3. Provide the details:
   *  **Name:** Name of the credential.
   *  **Role:** Role for the credential. The **Object writer** and **Reader** role is suggested for handling evidence.

## Provide bucket access to the service credential
{: #cd-devsecops-cos-bucket-access}

1. In Cloud {{site.data.keyword.cos_short}}, click **Buckets**.
2. Select the bucket that was created in the previous step.
3. Click **Access policies**, and select the **Service IDs** option
4. Select the service credential that was created in the previous step, and select the **Object writer** and **Reader** role.
5. Click **Create Access Policy**.

## Copy the API Key of the service credential
{: #cd-devsecops-cos-apikey-copy}

1. In Cloud {{site.data.keyword.cos_short}}, click **Service Credential**.
2. Click **expand** the service credential to view the **apikey**. 
3. Note the *apikey* because you need it later when setting up your DevSecOps pipelines.

## Related information
{: #cd-devsecops-cos-related-content}

* [{{site.data.keyword.cos_full_notm}} buckets as evidence locker](/docs/devsecops?topic=devsecops-cd-devsecops-cos-bucket-evidence)
* [{{site.data.keyword.cos_full_notm}} evidence](/docs/devsecops?topic=devsecops-devsecops-evidence#devsecops-v2-evidence-lockers)

---

copyright:
  years: "2025"
lastupdated: "2025-10-07"

keywords: DevSecOps, IBM Cloud, revert version

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}


# Revert to older versions
{: #devsecops-revert-older-version}

When new releases prevent you from running your pipelines, you can revert to an older version that ensures the smooth functioning of your pipelines. 

## Before you begin
{: #devsecops-revert-prerequisites}

The supported pipeline versions are v10 and v11.

Identify a stable version to which you want to revert to. For example, v11.8.0 or v10.52.0.


## Steps to revert to an older version
{: #devsecops-revert-steps}

To revert to an older version, perform the following steps:

1. Open the pipeline page and click **Settings**. The pipeline **Settings** page is displayed.

1. Select **Definition**s, and click the overflow menu to edit the pipeline details. The **Definition Repository** window opens.

1. Select **Tag** and enter the version you want to revert to, and click on **Update**. The definitions table is updated.

1. Click **Save**. The selected version is updated and **Save** is disabled.


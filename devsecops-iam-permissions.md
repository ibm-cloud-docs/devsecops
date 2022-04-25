---

copyright:
  years: 2021, 2022
lastupdated: "2022-04-25"

keywords: DevSecOps, COS, secure toolchain, compliance, ibm cloud, object storage

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# IAM permissions for {{site.data.keyword.cloud_notm}} resources
{: #cd-devsecops-iam-permissions}

Certain IAM permissions are required for the various {{site.data.keyword.cloud_notm}} resources that are used with the DevSecOps continuous integration and continuous delivery pipeline templates.
{: shortdesc}

| Role | Resource |
|--|--|
|  Administrator, Writer  |  The {{site.data.keyword.cos_full_notm}} service in your team's resource group. |
|  Administrator, Writer  |  The {{site.data.keyword.contdelivery_full}} service in your team's resource group. |
|  Administrator  |  The toolchain service in your team's resource group. |
|  KeyPurge, Writer, Editor, Manager, Administrator  |  The {{site.data.keyword.keymanagementservicefull}} service in your team's resource group. |
|  Viewer, Reader, Writer  |  The {{site.data.keyword.containerlong}}. |
|  Viewer, ReaderPlus  |  The {{site.data.keyword.keymanagementserviceshort}} service in your team's resource group. |
|  Viewer, SecretsReader  |  The Secrets Manager service in your team's resource group. |
{: caption="Table 1. IAM roles and resources" caption-side="top"}

## Related information
{: #cd-devsecops-iam-related-content}

* [IAM roles and actions](/docs/account?topic=account-iam-service-roles-actions)
* [Access management in {{site.data.keyword.cloud_notm}}](/docs/account?topic=account-cloudaccess)
* [IAM access](/docs/account?topic=account-userroles)

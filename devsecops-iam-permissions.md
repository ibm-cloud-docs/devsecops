---

copyright:
  years: 2021
lastupdated: "2021-10-12"

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

# IAM permissions for {{site.data.keyword.cloud_notm}} resources
{: #cd-devsecops-iam-permissions}

Certain IAM permissions are required for the various {{site.data.keyword.cloud_notm}} resources that are used with the DevSecOps continuous integration and continuous delivery pipeline templates.
{: shortdesc}

| Role | Resource |
|--|--|
|  Administrator, Writer  |  The {{site.data.keyword.cos_full_notm}} service in your team's resource group.  |
|  Administrator, Writer  |  The {{site.data.keyword.contdelivery_full}} service in your team's resource group.  |
|  Administrator  |  The toolchain service in your team's resource group.  |
|  KeyPurge, Writer, Editor, Manager, Administrator  |   The {{site.data.keyword.keymanagementservicefull}} service in your team's resource group.  |
|  Viewer, Reader, Writer  |  The {{site.data.keyword.containerlong}}.  |
|  Viewer, ReaderPlus  |  The {{site.data.keyword.keymanagementserviceshort}} service in your team's resource group.  |
{: caption="Table 1. IAM roles and resources" caption-side="top"}

## Related information
{: #cd-devsecops-iam-related-content}

* [IAM roles and actions](/docs/account?topic=account-iam-service-roles-actions)
* [Access management in {{site.data.keyword.cloud_notm}}](/docs/account?topic=account-cloudaccess)
* [IAM access](/docs/account?topic=account-userroles)

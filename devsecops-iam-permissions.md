---

copyright:
  years: 2021, 2022
lastupdated: "2022-06-01"

keywords: DevSecOps, COS, secure toolchain, compliance, ibm cloud, object storage

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# IAM permissions for toolchains 
{: #iam-permissions}

Certain IAM permissions are required for the various toolchains and {{site.data.keyword.cloud_notm}} resources that are used with the DevSecOps continuous integration and continuous delivery pipeline templates.
{: shortdesc}

IAM services restrict the user permissions to create, manage, and run the toolchains. {{site.data.keyword.cloud_notm}} provides various roles per resource group to provide access to toolchains as follows: 

| Role | Description |
|--|--|
|  Viewer  |  Users can view the toolchains. |
|  Operator  |  Users can run the toolchains. |
|  Editor  |  Users can manage the toolchains, which includes creating and deleting toolchains along with performing all platform actions except for managing the account and assigning access policies.|
|  Administrator  | Users can perform all platform actions based on the resource this role is being assigned, including assigning access policies to other users.|
{: caption="Table 1. Roles per resource group" caption-side="bottom"}

If you want to assign a role to a user, take the following steps:

1. Go to **Manage** > **Access (IAM)**.
2. Go to **Users** and select the user.
3. Click **Access** and select **Assign access**.
4. Click **Access policy**.
5. Search and select the service name `Toolchain`.
6. Select the scope of the access and provide the appropriate role.

# IAM permissions for {{site.data.keyword.cloud_notm}} resources
{: #iam-permissions-resources}

Certain IAM permissions are required for the various {{site.data.keyword.cloud_notm}} resources that are used with the DevSecOps continuous integration and continuous delivery pipeline templates as follows:

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

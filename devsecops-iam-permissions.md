---

copyright:
  years:  2021, 2022
lastupdated: "2022-09-05"

keywords: IAM access for toolchain, permissions for toolchain, identity and access management for toolchain, roles for toolchain, actions for toolchain, assigning access for toolchain

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}


# Managing IAM access for toolchains
{: #iam-permissions}

Access to toolchains for users in your account is controlled by {{site.data.keyword.cloud}} Identity and Access Management (IAM). Every user that accesses the toolchains in your account must be assigned an access policy with an IAM role. Review the following roles, actions, and more to determine the best way to assign access to toolchains.
{: shortdesc}

The access policy that you assign users in your account determines what actions a user can perform within the context of the toolchain that you select. The allowable actions are customized and defined by the toolchain as operations that are allowed to be performed. Each action is mapped to an IAM platform or role that you can assign to a user.

If a specific role and its actions don't fit the use case that you want to address, you can [create a custom role](/docs/account?topic=account-custom-roles#custom-access-roles) and pick the actions to include.
{: tip}

IAM access policies enable access to be granted at different levels. Some of the options include the following: 

* Access across all instances of the service in your account

Review the following tables that outline what types of tasks each role allows for when you're working with the toolchain service. Platform management roles enable users to perform tasks on service resources at the platform level, for example, assign user access to the service, create or delete instances, and bind instances to applications. Service access roles enable users access to toolchain and the ability to call the toolchain's API. For information about the actions that are mapped to each role, see [IAM roles and actions - Toolchain](/docs/account?topic=account-iam-service-roles-actions#toolchain).

| Platform role |  Description of actions | 
|---------------|-------------------------|
| Viewer                 |  View toolchains and delivery pipelines. |
| Operator               |  Run toolchains and delivery pipelines.  |
| Editor                 |  Manage the toolchains, which include creating and deleting toolchains along with performing all platform actions except for managing the account and assigning access policies.  | 
| Administrator          |  Perform all platform actions based on the resource this role is being assigned, including assigning access policies to other users.  |
{: row-headers}
{: class="simple-tab-table"}
{: caption="Table 1. IAM platform roles" caption-side="bottom"}
{: #iamrolesplatform}
{: tab-title="Platform roles"}
{: tab-group="IAM"}

| Service role |  Description of actions | 
|--------------|------------------------|
|  Administrator, Writer  |  The {{site.data.keyword.cos_full_notm}} service in your team's resource group. |
|  Administrator, Writer  |  The {{site.data.keyword.contdelivery_full}} service in your team's resource group. |
|  Administrator  |  The toolchain service in your team's resource group. |
|  Viewer, Reader, Writer  |  The {{site.data.keyword.containerlong}}. |
|  Viewer, ReaderPlus  |  The {{site.data.keyword.keymanagementserviceshort}} service in your team's resource group. |
|  Viewer, SecretsReader  |  The Secrets Manager service in your team's resource group. |
{: row-headers}
{: class="simple-tab-table"}
{: caption="Table 1. IAM service access roles" caption-side="bottom"}
{: #iamrolesservice}
{: tab-title="Service roles"}
{: tab-group="IAM"}

## Assigning access to toolchains in the console
{: #assign-access-console}

Assign access in the console in one of the following ways:

* Access policies per user. You can manage access policies per user from the **Manage** > **Access (IAM)** > **Users** page in the console. For information about the steps to assign IAM access, see [Assigning access to resources in the console](/docs/account?topic=account-assign-access-resources&interface=ui#access-resources-console).
* Access groups. Access groups are used to streamline access management by assigning access to a group once, then you can add or remove users as needed from the group to control their access. You manage access groups and their access from the **Manage** > **Access (IAM)** > **Access groups** page in the console. For more information, see [Assigning access to a group in the console](/docs/account?topic=account-groups&interface=ui#access_ag).


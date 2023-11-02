---

copyright:
  years: 2021, 2023
lastupdated: "2023-09-26"

keywords: tool integrations, IBM Cloud Public, Security and Compliance Center

subcollection: ContinuousDelivery

---

{{site.data.keyword.attribute-definition-list}}   

# Configuring {{site.data.keyword.compliance_short}}
{: #scc}

You can use the {{site.data.keyword.compliance_full}} to embed security checks into your everyday workflows to monitor for security and compliance. By monitoring for risks, you can identify security vulnerabilities and quickly work to mitigate the impact and fix the issues.
{: shortdesc}

Currently, you can use this tool integration only with toolchains that are created from the DevSecOps continuous integration and continuous delivery templates. Before you configure a {{site.data.keyword.compliance_short}} tool integration, make sure that you have a Git repository (repo) that contains the evidence locker for your toolchain and a configured Git tool integration that points to this repo.
{: important}

This tool integration verifies the security and compliance posture of your toolchain by identifying the location of the evidence locker and the path to the evidence summary. For more information about evidence format and structure, see [Evidence](/docs/devsecops?topic=devsecops-devsecops-evidence). You can also configure a pipeline task to trigger security and compliance scans by using this tool.

{{site.data.keyword.compliance_short}} supports two scan types: the older hybrid cloud scan and the new cloud scan. Both of these scan types can find toolchains and associated evidence, as it is configured in the **Name**, **Evidence repository name or URL**, and **Evidence namespace** fields within the {{site.data.keyword.compliance_short}} tool integration.

Configure {{site.data.keyword.compliance_short}} to embed security checks into your workflows to monitor for security and compliance:

1. If you are configuring this tool integration as you are creating the toolchain, and a {{site.data.keyword.compliance_short}} tool integration exists within the template that you are configuring, click the **{{site.data.keyword.compliance_short}}** tab. Alternatively, in the **More tools** section, click **{{site.data.keyword.compliance_short}}**.
1. If you have a toolchain and are adding this tool integration to it, from the {{site.data.keyword.cloud_notm}} console, click the menu icon ![hamburger icon](images/icon_hamburger.svg) and select **DevOps**. On the Toolchains page, click the toolchain to open its Overview page. Alternatively, on your app's Overview page, on the Continuous delivery card, click **View toolchain**. Then, click **Overview**.  

   a. Click **Add tool**.

   b. In the Tool Integrations section, click **{{site.data.keyword.compliance_short}}**.

1. Type the name that you want to display for this tool integration on the {{site.data.keyword.compliance_short}} card in your toolchain. This name is used to identify the tool integration in your toolchain.

1. Specify the **Evidence repository name or URL**. This is the name or URL of the repo that stores the evidence locker for your toolchain. This repo must correspond to the Git tool integration you previously added to your toolchain to store your evidence. Currently, only GitHub, GitLab, and IBM-hosted Git are supported. {{site.data.keyword.compliance_short}} scans run with the user credentials that are configured in the {{site.data.keyword.compliance_short}} scope. That user must have Git OAuth credentials for this toolchain's region and read access to the evidence repo.
1. Click **Create Integration**.
1. On your Toolchain's Overview page, on the **IBM Cloud tools** card, click **{{site.data.keyword.compliance_short}}**.
 
## Configuring {{site.data.keyword.compliance_short}} by using the API
{: #scc-config-parameters}

The {{site.data.keyword.compliance_short}} tool integration supports the following configuration parameters that you can use with the [Toolchain HTTP API and SDKs](https://cloud.ibm.com/apidocs/toolchain){: external} when you [create](https://cloud.ibm.com/apidocs/toolchain#create-tool){: external}, [read](https://cloud.ibm.com/apidocs/toolchain#get-tool-by-id){: external}, and [update](https://cloud.ibm.com/apidocs/toolchain#update-tool){: external} tool integrations.

You must specify the `tool_type_id` property in the request body with the `security_compliance` value.
{: important}

| Parameter | Usage | Type | Terraform argument | Description |
| --- | --- | --- | --- | --- |
| attachment_id | optional, updatable | String | attachment_id | An attachment ID. An attachment is configured under a profile to define how a scan will be run. To find the attachment ID, in the browser, in the attachments list, click on the attachment link, and a panel appears with a button to copy the attachment ID. This parameter is only relevant when the `use_profile_attachment` parameter is `enabled`. |
| evidence_namespace | required, updatable | String | evidence_namespace | The type of pipeline evidence to display in {{site.data.keyword.compliance_short}} for this toolchain. Valid values are `cd` (uses evidence that is generated by a continuous deployment pipeline) or `cc` (uses evidence that is generated by a continuous compliance pipeline). |
| evidence_repo_name | required, updatable | String | evidence_repo_url | The URL of a Git repo evidence locker. The DevSecOps toolchain templates collect and store evidence for scans and tasks in an evidence repo. Make sure that this URL matches the `repo_url` for a Git tool integration in this toolchain. The DevSecOps toolchain goals in the {{site.data.keyword.compliance_short}} check the evidence repo for the pass or fail results for those goals. |
| instance_crn | optional, updatable | String | instance_crn | The {{site.data.keyword.compliance_short}} service instance CRN (Cloud Resource Name). It is recommended to provide an instance CRN, but when absent, the oldest service instance will be used. This parameter is only relevant when the `use_profile_attachment` parameter is `enabled`. |
| name | required, updatable | String | name | The name of this tool integration. |
| profile_name | optional, updatable | String | profile_name | The name of a {{site.data.keyword.compliance_short}} profile. Usually, use the "IBM Cloud for Financial Services" predefined profile, which contains the DevSecOps Toolchain rules. Or use a user-authored customized profile that has been configured to contain those rules. This parameter is only relevant when the `use_profile_attachment` parameter is `enabled`. |
| profile_version | optional, updatable | String | profile_version | The version of a {{site.data.keyword.compliance_short}} profile, in SemVer format, like '0.0.0'. This parameter is only relevant when the `use_profile_attachment` parameter is `enabled`. |
| scc_api_key | optional, updatable | Password | scc_api_key | The IBM Cloud API key used to access the {{site.data.keyword.compliance_short}} service, for the use profile with attachment setting. This parameter is only relevant when the `use_profile_attachment` parameter is `enabled`. You can use a toolchain secret reference for this parameter. For more information, see [Protecting your sensitive data in Continuous Delivery](https://cloud.ibm.com/docs/ContinuousDelivery?topic=ContinuousDelivery-cd_data_security#cd_secure_credentials). |
| use_profile_attachment | optional, updatable | String | use_profile_attachment | Set to `enabled` to enable use profile with attachment, so that the scripts in the pipeline can interact with the {{site.data.keyword.compliance_short}} service. When enabled, other parameters become relevant; `scc_api_key`, `instance_crn`, `profile_name`, `profile_version`, `attachment_id`. |
{: caption="Table 1. {{site.data.keyword.compliance_short}} tool integration parameters" caption-side="bottom"}

## Learn more about {{site.data.keyword.compliance_short}}
{: #learn_scc}

To learn more about {{site.data.keyword.compliance_short}}, see [Getting started with Security and Compliance Center](/docs/security-compliance?topic=security-compliance-getting-started).

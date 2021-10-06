---

copyright:
  years: 2019, 2021
lastupdated: "2021-06-16"

keywords: customer responsibilities, IBM responsibilities, terms and conditions, disaster recovery, toolchain backup

subcollection: devsecops

---

{:shortdesc: .shortdesc}
{:external: target="_blank" .external}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:deprecated: .deprecated}
{:download: .download}
{:preview: .preview}

# Your responsibilities with using {{site.data.keyword.contdelivery_short}}
{: #responsibilities-cd}

Learn about the management responsibilities and terms and conditions that you have when you use {{site.data.keyword.contdelivery_full}}. For a high-level view of the service types in {{site.data.keyword.cloud}} and the breakdown of responsibilities between the customer and {{site.data.keyword.IBM_notm}} for each type, see [Shared responsibilities for {{site.data.keyword.cloud_notm}} offerings](/docs/overview?topic=overview-shared-responsibilities).
{: shortdesc}

You can integrate third-party tools (such as Public GitHub), or tools that you manage (such as a private Tekton worker that is running on your infrastructure), into {{site.data.keyword.contdelivery_short}} toolchains. However, IBM’s management responsibilities do not extend to any tools that are managed by you or by third-parties.
{: important}

Review the following sections for the specific responsibilities for you and for {{site.data.keyword.IBM_notm}} when you use {{site.data.keyword.contdelivery_short}}. For the overall terms of use, see [{{site.data.keyword.cloud_notm}} Terms and Notices](/docs/overview/terms-of-use?topic=overview-terms).

  
## Incident and operations management
{: #incident-and-ops}

Incident and operations management includes tasks such as monitoring, event management, high availability, problem determination, recovery, and full state backup and recovery.

| Task | IBM responsibilities | Your responsibilities |
|----------|-----------------------|--------|
|Maintain high availability and respond to incidents.| Operate the {{site.data.keyword.contdelivery_short}} service in accordance with the {{site.data.keyword.cloud_notm}} Public [Service Level Agreements (SLAs)](/docs/overview/terms-of-use?topic=overview-slas).  | Plan the deployment of your apps and data to meet your availability objectives. For more information about managing availability, see [How do I ensure zero downtime?](/docs/overview/terms-of-use?topic=overview-zero-downtime) |
{: caption="Table 1. Responsibilites for incident and operations" caption-side="top"}


## Change management
{: #change-management}

Change management includes tasks such as deployment, configuration, upgrades, patching, configuration changes, and deletion.

| Task | IBM responsibilities | Your responsibilities |
|----------|-----------------------|--------|
|Update {{site.data.keyword.deliverypipeline}} images.| Provide updated {{site.data.keyword.deliverypipeline}} job container images. These images might include updates to command tools (such as `ibmcloud`) that might affect the function of existing pipeline job scripts.  | Update {{site.data.keyword.deliverypipeline}} job scripts to respond to changes in underlying container images, or configure pipeline job scripts to use specific versions of container images instead of the latest version. |
|Deploy apps and other workloads by using the {{site.data.keyword.deliverypipeline}}.| Maintain the {{site.data.keyword.deliverypipeline}} service.  | Maintain the job scripts that are run by the {{site.data.keyword.deliverypipeline}} service, the inputs to pipeline job stages (such as source code), and the apps and other workloads that are deployed by job scripts. |
|Support {{site.data.keyword.deliverypipeline}} private workers to allow pipeline workloads to run on customer infrastructure.| Provide and maintain the private worker agent that integrates customer-managed infrastructure into the {{site.data.keyword.deliverypipeline}} service.  | Install the private worker agent into your Kubernetes cluster with enough capacity to run your pipeline workloads. Update the agent as new versions become available. |
{: caption="Table 1. Responsibilities for change management" caption-side="top"}


## Identity and access management
{: #iam-responsibilities}

Identity and access management includes tasks such as authentication, authorization, access control policies, and approving, granting, and revoking access.

| Task | IBM responsibilities | Your responsibilities |
|----------|-----------------------|--------|
|Manage access to toolchains in resource groups and their associated IBM-hosted tools, except for {{site.data.keyword.gitrepos}}.| N/A  | Grant, revoke, and manage access to toolchains by using {{site.data.keyword.Bluemix_notm}} Identity and Access Management (IAM). For more information about access management, see [Managing user access to toolchains in resource groups](/docs/services/ContinuousDelivery?topic=ContinuousDelivery-toolchains-iam-security). |
|Manage access to toolchains in Cloud Foundry orgs.| N/A  | Add, remove, and manage access to toolchains by updating the members and roles of users in both Cloud Foundry orgs and Cloud Foundry-based toolchains. For more information about access management, see [Managing access to toolchains in Cloud Foundry orgs](/docs/services/ContinuousDelivery?topic=ContinuousDelivery-toolchains-cf-security). |
|Manage the {{site.data.keyword.contdelivery_short}} service's access to third-party tools that are integrated into toolchains. | N/A  | Add, update, or delete third-party tool integration configurations (including access credentials for tool integrations) in toolchains. For more information about working with tool integrations, see [Configuring tool integrations](/docs/services/ContinuousDelivery?topic=ContinuousDelivery-integrations). |
|Manage access to repos in {{site.data.keyword.gitrepos}}. | N/A  | From the **Settings** > **Members** page in the {{site.data.keyword.gitrepos}} dashboard, manage project members and role permissions. |
|Manage all other access to third-party tools that are integrated with {{site.data.keyword.contdelivery_short}}. | N/A  | Manage access by using the capabilities that are provided by the third-party tools.  |
{: caption="Table 2. Responsibilities for identity and access management" caption-side="top"}

## Security and regulation compliance
{: #security-compliance}

Security and regulation compliance includes tasks such as security controls implementation and compliance certification.

| Task | IBM responsibilities | Your responsibilities |
|----------|-----------------------|--------|
|Meet security and compliance objectives.| Provide a secure {{site.data.keyword.contdelivery_short}} service that complies with key standards. For more information about data security, see [How do I know that my data is safe?](/docs/overview/terms-of-use?topic=overview-security)  | Secure your workloads and data. To learn more about securing your cloud apps, see [Security to safeguard and monitor your cloud apps](https://www.ibm.com/cloud/garage/architectures/securityArchitecture){: external}. To learn more about securing your data while you are using the {{site.data.keyword.contdelivery_short}} service, see [Securing your data](/docs/services/ContinuousDelivery?topic=ContinuousDelivery-cd_data_security). |
{: caption="Table 4. Responsibilites for security and regulation compliance" caption-side="top"}

## Disaster recovery
{: #disaster-recovery}

Disaster recovery includes tasks such as providing dependencies on disaster recovery sites, provision disaster recovery environments, data and configuration backup, replicating data and configuration to the disaster recovery environment, and failover on disaster events.

IBM is responsible for maintaining backups and high availability of the {{site.data.keyword.contdelivery_short}} service in each supported region. However, because {{site.data.keyword.contdelivery_short}} toolchains are region-dependent, automated global failover is not supported. If a regional disaster occurs, the recovery time for toolchains depends on the recovery time for the region. You can implement manual failover to another region by creating and maintaining copies of your toolchains and Git repos that have idle pipelines, in another {{site.data.keyword.cloud_notm}} region.
{: important}

| Task | IBM responsibilities | Your responsibilities |
|----------|-----------------------|--------|
|Back up toolchain data.| Maintain regular backups of toolchain and pipeline definitions, Git repos, and any other toolchain integration data that is stored and managed by IBM.  | To support global failover, create and maintain copies of your toolchain and pipeline definitions, including tool integration data and your Git repos, in another IBM region. |
|Restore toolchain data.| Restore all toolchain and Git repos to the original {{site.data.keyword.cloud_notm}} region, when that region is available.    | To support global failover, manually switch to using the copied toolchains and repos in another region. |
{: caption="Table 5. Responsibilites for disaster recovery" caption-side="top"}

### Backing up your toolchains to a different region
{: #toolchains-backup}

IBM backs up and restores your toolchain data if a disaster occurs. You can also back up your toolchain to a different region by mirroring your Git repositories (repos), and then saving and restoring your toolchain. Backing up a toolchain provides faster failover and redundancy. It also ensures smooth operation if issues arise in the original region.

#### Mirroring your Git repos
{: #mirror_git_repo}

If you use {{site.data.keyword.gitrepos}} for both your source and target repos, you can use the built-in GitLab mirroring feature. This feature automatically syncs your source and target repos. If you use a different Git provider, you must set up your own mirroring solution. Alternatively, to mirror changes in the source repo, manually clone and push your Git repos to the target repo.

If you cannot mirror your projects, you can [export](https://us-south.git.cloud.ibm.com/help/user/project/settings/import_export.md#exporting-a-project-and-its-data){: external} and [import](https://us-south.git.cloud.ibm.com/help/user/project/settings/import_export.md#importing-the-project){: external} your projects instead. However, the source and target repos are not synchronized if changes are made to the source repo.
{: tip}

To mirror a {{site.data.keyword.gitrepos}} repo, complete the following steps:

1. [Create a blank GitLab project](https://us-south.git.cloud.ibm.com/help/gitlab-basics/create-project.md#blank-projects){: external} in the target repo. Do not initialize this project with a README file.
1. [Create a personal access token](https://us-south.git.cloud.ibm.com/help/user/profile/personal_access_tokens.md#creating-a-personal-access-token){: external} with the `write_repository` scope in the target region. Save a copy of this token.
1. Use [Push mirroring](https://us-south.git.cloud.ibm.com/help/user/project/repository/repository_mirroring.md#pushing-to-a-remote-repository-core){: external} to push the source repo to the target repo:     

   a. From the source repo, select **Settings** > **Repository**.

   b. In **Mirror repository** section of the Repository page, type the URL of the target repo.

   c. Select **Push** to specify the direction of the mirror.

   d. Select **Password** to use the password authentication method.

   e. Type the personal access token that you created in step 2.

   f. Click **Mirror repository** to start the mirroring process. It might take up to five minutes for the process to start.
  
1. In the target region, locate the mirror of your repo to verify that it was successfully mirrored.
  
#### Saving and restoring your toolchain
{: #save_restore_toolchain}

To save a toolchain, download and run a script on your local workstation that creates a toolchain template in text form. This template is used to instantiate a copy of the source toolchain. For security reasons, secrets are not included in this toolchain template.  

After you create a toolchain by using this template, you must configure your tool integrations. Then, restore secrets from the source toolchain by copying their values from the source toolchain to the target toolchain. Unlike GitLab mirroring, this procedure creates a one-time copy of the toolchain. Any subsequent changes to the source toolchain are not reflected in the copied version of the toolchain.

Because the toolchain template is a copy, when instantiated, it references the source Git repo. To fix this issue, reconfigure the target toolchain with the target repo.
{: tip}

To save and restore a toolchain, complete the following steps:

1. Create a temporary directory on your local workstation.
1. Change directory into the temporary directory.
1. Generate a textual representation of the source toolchain in the temporary directory by using the [toolchain-to-template script](https://github.com/open-toolchain/toolchain-to-template){: external}.
1. [Create a temporary blank GitLab project](https://us-south.git.cloud.ibm.com/help/gitlab-basics/create-project.md#blank-projects){: external} in the target region.
1. Type the following commands to initialize the temporary directory for Git operations and push the contents to the temporary GitLab project:     

   a. git init
 
   b. git add --all
 
   c. git remote add origin *target repository url*
 
   d. git commit -m "add template yaml files"
 
   e. git push -u origin --all

1. [Create a personal access token](https://us-south.git.cloud.ibm.com/help/user/profile/personal_access_tokens.md#creating-a-personal-access-token){: external} in the target region with the `api`, `read_user`, `read_api`, `read_repository`, and `write_repository` scope.
1. Create a toolchain in the target region by constructing a URL and pasting it into your browser:     

   a. Construct a target toolchain URL that contains the temporary repo, personal access token, and target region: 
  
    ```bash
    https://cloud.ibm.com/devops/setup/deploy?repository=<temporary repository>&repository_token=<personal-access-token>& env_id=ibm:yp:<target region>
    ```
    For example, `https://cloud.ibm.com/devops/setup/deploy?repository=https://us-east.git.cloud.ibm.com/user/temp-repo& repository_token=aG7junk9dafiiT6w6&env_id=ibm:yp:us-east`.

   b. Replace the source repo URL with the target repo URL that you created when mirroring your Git repos.

   c. Click **Create**. Because you didn't update your secrets, your pipeline runs but does not succeed.     

1. [Revoke the personal access token](https://us-south.git.cloud.ibm.com/help/user/profile/personal_access_tokens.md#creating-a-personal-access-token){: external} that you created in step 6.
1. [Configure pipeline secrets](/docs/ContinuousDelivery?topic=ContinuousDelivery-deliverypipeline_about#environment_properties) by adding the secure property values that are required by your pipeline.
1. [Run your target pipelines](/docs/ContinuousDelivery?topic=ContinuousDelivery-deliverypipeline_about#deliverypipeline_stages) to make sure that they are working.
1. If you are mirroring repos, disable commit triggers so that your target pipelines don't run when a change is mirrored from the target repo to the source repo:     

   a. Select the **Run jobs only when this stage is run manually** option for the [Input stage](/docs/ContinuousDelivery?topic=ContinuousDelivery-deliverypipeline_about#deliverypipeline_stages) of your pipeline.

   b. Verify that your pipelines can be [started manually](/docs/ContinuousDelivery?topic=ContinuousDelivery-deliverypipeline_about#deliverypipeline_stages).  
  
#### Using your backup toolchain
{: #use_backup_toolchain}

Backup toolchains are intended to provide short-term continuity if issues exist in a particular region. Do not commit to target repos directly or enable pipeline commit triggers on target pipelines. If you commit to the target repo directly and then return to using the source repo, you risk conflicts and inconsistencies because the repos are not synchronized. If you enable commit triggers and then return to using the source repo, both pipelines run when code is updated. To avoid these scenarios, run your pipelines manually and return to the source toolchain as soon as possible.

#### Known limitations
{: #backup_known_limitations}

Backing up your toolchain creates a copy of the toolchain itself, but it does not copy all of the data that is referenced by the toolchain. Consider the following limitations when you back up a toolchain to a different region:

* You cannot back up and restore Tekton pipelines.
* You cannot back up and restore pipelines that use pipeline private workers.
* Eclipse Orion {{site.data.keyword.webide}} workspaces are not included in the copied toolchain.
* Pipeline logs and artifacts are not included in the copied toolchain.
* Tool configuration secrets that are stored in Key Protect or HashiCorp Vault are not included in the copied toolchain.
* {{site.data.keyword.DRA_short}} data is not included in the copied toolchain.

If your toolchain is subject to these limitations, you cannot use the script to back up and restore these aspects of your toolchain. Whenever the original toolchain is updated, you must keep the backup copy current.

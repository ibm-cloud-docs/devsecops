---

copyright:
  years: 2021, 2024
lastupdated: "2024-02-23"

keywords: DevSecOps, secrets in toolchains, managing secrets in toolchains, secrets manager

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Using secrets integrations in your toolchains
{: #cd-devsecops-toolchains-secrets}


The secrets management capabilities that are provided in the toolchain setup and pipeline user interfaces enable selection of vaulted secrets by using secrets integrations for {{site.data.keyword.secrets-manager_full}}, {{site.data.keyword.keymanagementservicelong}}, or HashiCorp Vault. By using the Secrets Picker dialog, a toolchain or pipeline editor can select named secrets from a bound secrets integration that is either configured **by CRN** (Cloud Resource Name) or **by name**, that is then resolved by reference at run time within the toolchain and pipeline. After a secret is chosen, a CRN or canonical secret reference is injected into the corresponding toolchain or pipeline secure property where the format is either `crn:v1:...secret:<secret-guid>` if it's a Secrets Manager integration that is configured **by CRN**, or alternatively `{vault::integration-name.secret-name}` if it's a vault integration that uses any of the supported providers and configured **by name**.

The canonical **by name** reference format currently does not resolve a secret that includes the period character in the secret name because this character is used to delimit each section of the canonical path.
{: note}

Regardless of which type of secret reference is used, either **by CRN** or **by name**, the front-end user interface components and Secrets Picker dialog only use a secret reference. The resolved value of a **by CRN** or **by name** secret reference is never exposed to the front-end and is always dynamically resolved at run time within a toolchain and pipeline based on a permitted [authorization](https://cloud.ibm.com/iam/authorizations){: external} being available (configured by using IAM Authorizations and Access policies).

Within {{site.data.keyword.cloud}}, the dynamic process of resolving **by CRN** and **by name** secrets references in toolchains and pipelines is performed by using internal virtual private endpoints (VPE) to all {{site.data.keyword.secrets-manager_full}} and {{site.data.keyword.keymanagementservicelong}} provider instances in all regions. This ensures all request and response data between toolchains, pipelines, and {{site.data.keyword.secrets-manager_full}} and {{site.data.keyword.keymanagementservicelong}} provider instances is kept within the in-boundary private IBM Cloud network and does not travel over any public network channels.
{: note}

In addition to manually selecting chosen secrets on a one-by-one basis from any bound secrets integrations in a toolchain, the option of using a `Secret Hint` is also available. This option enables a toolchain template to be predefined with suggested secrets names (also known as `Hints`) that are a short form secret reference. The format of a secret hint is `{vault::secret-name}` whereby no secret integration name is included. This provides flexibility to the toolchain author in that all required secret names can be prepopulated into a `toolchain.yml` and then these names are automatically resolved against whatever secrets integrations are configured for the toolchain.

As previously described, you can configure {{site.data.keyword.secrets-manager_short}} to reference secrets **by CRN**. For more information, see [Cloud Resource Names (CRN)](/docs/account?topic=account-crn). This format allows for greater flexibility because you can reference secrets from an {{site.data.keyword.secrets-manager_short}} instance in a different account if the correct [authorization](https://cloud.ibm.com/iam/authorizations){: external} is in place. For more information, see [Configuring Secrets Manager](/docs/ContinuousDelivery?topic=ContinuousDelivery-secretsmanager).

A `Hint` is a suggested default name that is automatically resolved against the first matching secret with the same name across any of the available **by name** secrets integrations that are bound to the toolchain.
{: note}

## DevSecOps pipeline secrets
{: #devsecops-pipeline-secrets}

The secrets that are used in both CI and CD are outlined as follows:

| Secret | Hint | Information |
| -------|------|-------------|
| {{site.data.keyword.cloud_notm}} API Key | `ibmcloud-api-key` | **Required: CI & CD** _Used to authenticate with IBM public cloud and perform a wide range of operations_ |
| GPG Private Key | `signing_key` | **Required: CI only** _This is the certificate that is used to sign images built by the CI pipeline_ |
| {{site.data.keyword.IBM_notm}} Private Worker Service API Key | `private-worker-service-api-key`  | **Required: CI only** _A Service ID API Key Used to run delivery pipeline workloads on a Tekton Private Worker Service_|
| GitHub Access Token | `git-token` | **Optional: CI & CD** _Used to authenticate with GitHub and provide access to the repositories_ |
| Artifactory API token | `artifactory-token` | **Required: CI & CD** _Used to access images used by pipeline tasks_|
| Slack webhook | `slack-webhook` | **Optional: CI & CD** _This webhook is required if you choose to use the Slack tool integration to post toolchain status notifications_ |
| ServiceNow API Token | `servicenow-api-token` | **Required: CD only** _Used to access Service Now for change management operations_ |
| HashiCorp Vault Role ID | `role-id` | **Required: CI & CD** _Used to authenticate with the HashiCorp Vault server_ |
| HashiCorp Vault Secret ID | `secret-id` | **Required: CI & CD** _Used to authenticate with the HashiCorp Vault server_ |
| {{site.data.keyword.cos_full_notm}} Writer API Key | `cos-api-key` | **Required: CI & CD** _Used to authenticate with the {{site.data.keyword.cos_short}} service - This key must have `writer` permission_ |
| SonarQube password or authentication token | `sonarqube-password` | **Optional: CI** _Used to authenticate with the SonarQube source code analyzer_ |
{: caption="DevSecOps Secrets" caption-side="top"}

If you are using a HashiCorp Vault server, ensure that the HashiCorp Vault tool integration uses the [AppRole Auth Method](https://developer.hashicorp.com/vault/docs/auth/approle){: external} method.
When you use the AppRole authentication method, you need `role-id` and `secret-id` to successfully integrate the HashiCorp Vault server with the toolchain. Because `role-id` and `secret-id` are secrets in themselves, it is recommended to store them by using a [{{site.data.keyword.keymanagementservicelong_notm}} tool integration](/docs/ContinuousDelivery?topic=ContinuousDelivery-keyprotect) so that they can be securely retrieved and applied in the toolchain workflow. All other toolchain secrets should be stored and retrieved by using the HashiCorp Vault tool integration.

If the pipeline environment property `git-token` is not set, `ibmcloud-api-key` is used to retrieve the {{site.data.keyword.gitrepos}} Access Token by default. However, if `ibmcloud-api-key` does not have access to `git`, `git-token` must be set.
{: note}

### Configuring the secrets stores
{: #configure-secret-stores}

With {{site.data.keyword.cloud_notm}}, you can choose from various secrets management and data protection offerings that help you protect your sensitive data and centralize your secrets. You can choose between the vault integrations depending on your requirements as explained in [Managing {{site.data.keyword.cloud_notm}} secrets](/docs/secrets-manager?topic=secrets-manager-manage-secrets-ibm-cloud). This documentation provides information about prerequisites and how to use a list of prescribed secret names that are otherwise known as hints. By using hints in a template, a toolchain can be automatically populated with preconfigured secrets without any need to manually select them from various vault integrations that are attached to the toolchain.

Use [{{site.data.keyword.secrets-manager_full}}](/docs/secrets-manager?topic=secrets-manager-getting-started) to securely store and apply secrets like API keys, Image Signature, or HashiCorp Vault credentials that are part of your toolchain.

![{{site.data.keyword.secrets-manager_short}} tool integration form](images/devsecops-secrets-manager.png){: caption="IBM Secrets Manager Tool Integration" caption-side="bottom"}

The templates also come with a HashiCorp Vault tool integration like the following example:

![HashiCorp Vault Tool Integration form with required fields and example values](images/hc-tool-int.png "HashiCorp Vault Tool Integration form with required fields and example values"){: caption="HashiCorp Vault Tool Integration" caption-side="bottom"}

To use HashiCorp Vault, you must provide the following information:

Name
:   A name for this tool integration. This name is displayed in the toolchain.

Server URL
:   The server URL for your HashiCorp Vault Instance. For example, `https://<vault-service>.<org>.com:8200`.

Integration URL
:   The URL that you want to navigate to when you click the HashiCorp Vault Integration tile.

Secrets Path
:   The mount path where your secrets are stored in your HashiCorp Vault Instance.

Authentication Method
:   The Authentication method for your HashiCorp Vault Instance. Use `AppRole`.

Role ID
:   Identifier that selects the AppRole against which the other credentials are evaluated.

Secret ID
:   Credential that is required by default for any login (with secret_id) and is intended to always be secret.

The templates also come with an {{site.data.keyword.keymanagementservicefull}} tool integration:

![{{site.data.keyword.keymanagementserviceshort}} tool integration form with required fields and example values](images/kp-int.png "{{site.data.keyword.keymanagementserviceshort}} tool integration form with required fields and example values"){: caption="IBM Key Protect tool integration" caption-side="bottom"}

If you stored the `role id` and `secret id` in {{site.data.keyword.keymanagementserviceshort}} in advance, then you can select the {{site.data.keyword.keymanagementserviceshort}} instance that contains those secrets in the tool card as shown in Figure 2. After that is done, then you can click the key icons on the **role id** and **secret id** fields in the HashiCorp Vault tool card, and use the picker to apply the secrets to those fields.

Similarly, any other secrets that are used in the toolchain have a key icon that is attached to the text field. You can use the same picker control to apply the HashiCorp Vault secrets to all the remaining instances.

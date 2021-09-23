---

copyright:
  years: 2021
lastupdated: "2021-08-13"

keywords: DevSecOps, secrets in toolchains, managing secrets in toolchains

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

# Managing secrets in your toolchains
{: #cd-devsecops-toolchains-secrets}

Many of the tool integrations in your CI and CD toolchains require secrets to be provided, for example passwords, API keys, certificates, or other tokens. For instance, an {{site.data.keyword.cloud}} API key is used to perform the most basic of pipeline tasks, such as to log in to {{site.data.keyword.cloud_notm}}. Similarly, the service ID API key is required to write evidences to the bucket in Cloud Object Store instance.
{: shortdesc}

For security reasons, these secrets should not belong to or be affiliated with a person's identity or account. This is because people often have greater access permissions than the toolchain automation requires, and it would violate the security principle of ["least privilege"](https://en.wikipedia.org/wiki/Principle_of_least_privilege){: external}. Also, people often change roles or even companies and their credentials should be removed which might break the toolchain automation. By using an identity that is affiliated specifically for automation purposes provides a separation of duties between automation and people who use the automation.

Instead, the secrets that are used for non-{{site.data.keyword.cloud_notm}} resources (such as GitHub Enterprise) should be affiliated with a functional ID within your enterprise with only the appropriate access that is needed by the toolchains. Likewise, secrets for {{site.data.keyword.cloud_notm}} resources should be affiliated with an [IAM service ID API key](/docs/account?topic=account-serviceidapikeys) that is affiliated with an [IAM service ID](/docs/account?topic=account-serviceids). The IAM service ID access permissions should be scoped to the least privilege required by the toolchains.

Managing credentials like these must be done securely and in compliance with best practices in the field of secrets management. In particular, this means vaulting the required secrets by using an approved in-boundary vault provider, such as [Hashicorp](https://www.vaultproject.io){: external} and then linking your toolchain secrets to those resources.

The secrets management capabilities that are provided in the toolchain setup and pipeline user interfaces enable easy selection of vaulted secrets by using Secrets Integrations for HashiCorp Vault and {{site.data.keyword.keymanagementservicelong}}. By using the Secrets Picker dialog, a toolchain or pipeline editor can easily select named secrets from a bound secrets integration that will then be resolved by reference within the toolchain and pipeline. After a secret is chosen, a canonical secret reference is injected into the corresponding toolchain or pipeline secure property where the format is `{vault::integration-name.secret-name}`. This long form canonical reference is used by the front-end user interface components and importantly, the resolved value of the actual secret is never exposed to a user or permitted service.

The secrets used in both CI and CD are outlined as follows:

A `Hint` is a suggested default name that is automatically resolved against the first matching secret with the same name across any of the available secrets integrations that are bound to the toolchain.
{: note}

## DevSecOps Pipeline Secrets
{: #devsecops-pipeline-secrets}

| Secret |  Information | 
|---------|------------|
| {{site.data.keyword.cloud_notm}} API key | **Required: CI & CD** Used to authenticate with IBM public cloud and perform a wide range of operations |
| {{site.data.keyword.IBM_notm}} Image Signing Key | **Required: CI only** This is the certificate that is used to sign images built by the CI pipeline |
| {{site.data.keyword.cos_full_notm}} Writer API Key |  **Optional: CI & CD** Used to authenticate with IBM Cloud Object Storage service - This key must have writer permission | 
| {{site.data.keyword.IBM_notm}} Private Worker Service API Key | **(Optional): CI only** A Service ID API Key used to run delivery pipeline workloads on a Tekton Private Worker Service |
| GRIT Access Token | **Optional: CI & CD** Used to authenticate with GRIT and provide access to the repositories |
| Slack Web Hook | **Optional: CI & CD** This webhook is required if you choose to use the Slack tool integration to post toolchain status notifications |
| HashiCorp Vault Role ID | **Optional: CI & CD** Used to authenticate with the Hashicorp vault server | 
| HashiCorp Vault Secret ID | **Optional: CI & CD** Used to authenticate with the Hashicorp vault server |
| SonarQube password or authentication token | **Optional: CI** Used to authenticate with the SonarQube source code analyzer |

{: caption="Table 1. DevSecOps Secrets" caption-side="top"}

While using Hashicorp vault service, ensure that the vault service uses [approle authentication](https://www.vaultproject.io/docs/auth/approle) method. When you use the approle authentication method, you need `role-id` and `secret-id` to successfully integrate Hashicorp server with the toolchain. Since `role-id` and `secret-id` are secrets in themselves, it is recommended to store them by using a [{{site.data.keyword.keymanagementservicelong_notm}} tool integration](/docs/ContinuousDelivery?topic=ContinuousDelivery-keyprotect) so that they can be securely retrieved and applied in the toolchain workflow. All other toolchain secrets should be stored and retrieved by using Hashicorp.

If the pipeline environment property `git-token` is not set, `ibmcloud-api-key` is used to retrieve the GRIT Access Token by default. However, if `ibmcloud-api-key` does not have access to `git`, `git-token` must be set.
{: note}

### Configuring the Secrets Stores
{: #configure-secret-stores}

When you create an instance of the CI or CD toolchain, you will see a Hashicorp tool integration like the following example:

![HashiCorp Vault Tool Integration form with required fields and example values](images/hc-tool-int.png "HashiCorp Vault Tool Integration form with required fields and example values"){: caption="Figure 1. HashiCorp Vault Tool Integration" caption-side="bottom"}

<!-- Next 3 links need new targets or to be removed -->

To use Hashicorp, you must provide the following information:

* **Name**: A name for this tool integration. This name is displayed in the toolchain.
* **Server URL**: The server URL for your HashiCorp Vault Instance. For example, `https://<vault-service>.<org>.com:8200`.
* **Integration URL**: The URL that you want to navigate to when you click the HashiCorp Vault Integration tile.
* **Secrets Path**: The mount path where your secrets are stored in your HashiCorp Vault Instance.
* **Authentication Method**: The Authentication method for your HashiCorp Vault Instance. Use `AppRole`.
* **Role ID**: Identifier that selects the AppRole against which the other credentials are evaluated.
* **Secret ID**: Credential that is required by default for any login (via secret_id) and is intended to always be secret.

The templates also come with a Key Protect tool integration:

![IBM Key Protect Tool Integration form with required fields and example values](images/kp-int.png "IBM Key Protect Tool Integration form with required fields and example values"){: caption="Figure 2. IBM Key Protect Tool Integration" caption-side="bottom"}

If you stored the `role id` and `secret id` in {{site.data.keyword.keymanagementserviceshort}} in advance, then you can select the {{site.data.keyword.keymanagementserviceshort}} instance that contains those secrets in the tool card as shown in Figure 2. Once that is done, then you can click the key icons on the `role id` and `secret id` fields in the Hashicorp tool card, and use the picker to apply the secrets to those fields.

Similarly, any other secrets that are used in the toolchain has a key icon attached to the text field. You can use the same picker control to apply the Hashicorp secrets to all the remaining instances.

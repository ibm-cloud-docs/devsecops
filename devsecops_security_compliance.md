---

copyright:
  years:  2021
lastupdated: "2021-09-15"

keywords: security and compliance for {{site.data.keyword.contdelivery_short}}, security for {{site.data.keyword.contdelivery_short}}, compliance for {{site.data.keyword.contdelivery_short}}

subcollection: devsecops

---

{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:download: .download}


# Managing security and compliance with {{site.data.keyword.contdelivery_short}}
{: #cd-manage-security-compliance}

{{site.data.keyword.contdelivery_short}} is integrated with the {{site.data.keyword.compliance_short}} to help you manage security and compliance for your organization. 
{: shortdesc}

With the {{site.data.keyword.compliance_short}}, you can:
- Monitor for controls and goals that pertain to {{site.data.keyword.contdelivery_short}}.
- Define rules for {{site.data.keyword.contdelivery_short}} that can help to standardize resource configuration.

## Monitoring security and compliance posture with {{site.data.keyword.contdelivery_short}}
{: #monitor-cd}

As a security or compliance focal, you can use the {{site.data.keyword.contdelivery_short}} goals to help ensure that your organization is adhering to the external and internal standards for your industry. By using the {{site.data.keyword.compliance_short}} to validate the resource configurations in your account against a profile, you can identify potential issues as they arise.

All of the goals for {{site.data.keyword.contdelivery_short}} are added to the {{site.data.keyword.cloud_notm}} Best Practices Controls 1.0 profile but can also be mapped to other profiles.
{: tip}

To start monitoring your resources, check out [Getting started with {{site.data.keyword.compliance_short}}](/docs/security-compliance?topic=security-compliance-getting-started).

### Available goals for {{site.data.keyword.contdelivery_short}}
{: #cd-available-goals}

#### DevSecOps goals
{: #cd-DevSecOps-goals}

The following goals are available as part of the DevSecOps toolchain templates. To get started, see [Setting up Tekton continuous integration pipelines with compliance](/docs/devsecops?topic=devsecops-cd-devsecops-tekton-ci-compliance).

- Check whether {{site.data.keyword.contdelivery_short}} toolchain continuously scans source code to identify critical and high vulnerabilities
- Check whether {{site.data.keyword.contdelivery_short}} toolchain has unit tests that are continuously run to validate source code changes
- Check whether {{site.data.keyword.contdelivery_short}} has Code Risk Analyzer configured that collects a bill of materials for pipeline run
- Check whether {{site.data.keyword.contdelivery_short}} toolchain is configured with image signing
- Check whether {{site.data.keyword.contdelivery_short}} toolchain source code meets Center for Internet Security benchmarks
- Check whether {{site.data.keyword.contdelivery_short}} toolchain has branch protection rules enabled	
- Check whether {{site.data.keyword.contdelivery_short}} toochain has secret detection scans enabled for source code	
- Check whether {{site.data.keyword.contdelivery_short}} production change request exists and is approved	
- Check whether {{site.data.keyword.contdelivery_short}} acceptance tests exist and have passed
- Check whether {{site.data.keyword.contdelivery_short}} Vulnerability Advisor scans images for OS vulnerability detection exist and have passed

Use the {{site.data.keyword.cloud_notm}} Best Practices Controls 1.0 profile for DevSecOps toolchains.
{: tip}

#### Toolchain properties
{: #cd-toolchain-goals}

- Ensure that only the tool integrations within the toolchain are included in the allow list parameter array

#### Identity and Access Management
{: #cd-iam-goals}

- Check whether {{site.data.keyword.contdelivery_short}} has no more than # users with the IAM administrator role
- Check whether {{site.data.keyword.contdelivery_short}} has no more than # service IDs with the IAM administrator role
- Check whether {{site.data.keyword.contdelivery_short}} has at least # users with the IAM manager role
- Check whether {{site.data.keyword.contdelivery_short}} has at least # service IDs with the IAM manager role
- Check whether {{site.data.keyword.contdelivery_short}} access is managed only by IAM access groups

## Governing {{site.data.keyword.contdelivery_short}} resource configuration
{: #govern-cd}

As a security or compliance focal, you can use the {{site.data.keyword.compliance_short}} to define configuration rules for the instances of {{site.data.keyword.contdelivery_short}} that you create.

[Config rules](#x3084914){: term} are used to enforce the configuration standards that you want to implement across your accounts. To learn more about the data that you can use to create a rule for {{site.data.keyword.contdelivery_short}}, review the following table.

| Resource kind | Property | Operator | Value | Description |
|---------------|----------|---------------|-------|-------------|
| *instance* | *toolchain_allowed_tool_integration_ids* | *strings_in_list* | [Services that can be added to the list](https://github.com/open-toolchain/sdk/wiki/services.md){: external} | An array list of Strings that indicates the tools that can be bound to a toolchain. |
{: caption="Table 1. Rule properties for {{site.data.keyword.contdelivery_short}}" caption-side="top"}

To learn more about config rules, check out [What is a config rule?](/docs/security-compliance?topic=security-compliance-what-is-rule).

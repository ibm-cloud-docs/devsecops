---

copyright:
  years:  2021, 2022
lastupdated: "2022-06-01"

keywords: DevSecOps, IBM Cloud, goals, Security and Compliance Center

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Managing security and compliance with {{site.data.keyword.contdelivery_short}}
{: #scc-manage-cd}

{{site.data.keyword.contdelivery_short}} is integrated with the {{site.data.keyword.compliance_short}} to help you manage security and compliance for your organization. 
{: shortdesc}

With the {{site.data.keyword.compliance_short}}, you can:
- Monitor for controls and goals that pertain to {{site.data.keyword.contdelivery_short}}.
- Define rules for {{site.data.keyword.contdelivery_short}} that can help to standardize resource configuration.

## Monitoring security and compliance posture with {{site.data.keyword.contdelivery_short}}
{: #scc-monitor}

As a security or compliance focal, you can use the {{site.data.keyword.contdelivery_short}} goals to ensure that your organization adheres to the external and internal standards for your industry. By using the {{site.data.keyword.compliance_short}} to validate the resource configurations in your account against a profile, you can identify potential issues as they arise.

All of the goals for {{site.data.keyword.contdelivery_short}} are added to the {{site.data.keyword.cloud_notm}} Security Best Practices v1.0.0 profile but can also be mapped to other profiles.
{: tip}

To start monitoring your resources, see [Getting started with {{site.data.keyword.compliance_short}}](/docs/security-compliance?topic=security-compliance-getting-started).

### Available goals for {{site.data.keyword.contdelivery_short}}
{: #scc-available-goals}

#### DevSecOps goals
{: #scc-goals}

The following goals are available as part of the DevSecOps toolchain templates. To get started, see [Setting up Tekton continuous integration pipelines with compliance](/docs/devsecops?topic=devsecops-cd-devsecops-tekton-ci-compliance).

- Check whether {{site.data.keyword.contdelivery_short}} toolchain continuously scans source code to identify critical and high vulnerabilities.
- Check whether {{site.data.keyword.contdelivery_short}} toolchain has unit tests that run continuously to validate source code changes.
- Check whether {{site.data.keyword.contdelivery_short}} has Code Risk Analyzer configured that collects a bill of materials for pipeline run.
- Check whether {{site.data.keyword.contdelivery_short}} toolchain is configured with image signing.
- Check whether {{site.data.keyword.contdelivery_short}} toolchain source code meets Center for Internet Security benchmarks.
- Check whether {{site.data.keyword.contdelivery_short}} toolchain has branch protection rules enabled.
- Check whether {{site.data.keyword.contdelivery_short}} toochain has secret detection scans enabled for source code.
- Check whether {{site.data.keyword.contdelivery_short}} production change request exists and is approved.
- Check whether {{site.data.keyword.contdelivery_short}} acceptance tests exist and have passed.
- Check whether {{site.data.keyword.contdelivery_short}} Vulnerability Advisor scans images for OS vulnerability detection exist and have passed.

Use the {{site.data.keyword.cloud_notm}} Security Best Practices v1.0.0 profile for DevSecOps toolchains.
{: tip}

#### Toolchain properties
{: #scc-toolchain-properties}

Ensure that the tool integrations within the toolchain are included in the allow list parameter array only.

#### Identity and Access Management
{: #scc-iam-goals}

- Check whether {{site.data.keyword.contdelivery_short}} has no more than # users with the IAM administrator role.
- Check whether {{site.data.keyword.contdelivery_short}} has no more than # service IDs with the IAM administrator role.
- Check whether {{site.data.keyword.contdelivery_short}} has at least # users with the IAM manager role.
- Check whether {{site.data.keyword.contdelivery_short}} has at least # service IDs with the IAM manager role.
- Check whether {{site.data.keyword.contdelivery_short}} access is managed only by IAM access groups.

## Governing {{site.data.keyword.contdelivery_short}} resource configuration
{: #scc-govern}

As a security or compliance focal, you can use the {{site.data.keyword.compliance_short}} to define configuration rules for the instances of {{site.data.keyword.contdelivery_short}} that you create.

[Config rules](#x3084914){: term} enforce the configuration standards that you want to implement across your accounts. To learn more about the data that you can use to create a rule for {{site.data.keyword.contdelivery_short}}, review Table 1.

| Resource type | Property | Operator | Value | Description |
|---------------|----------|---------------|-------|-------------|
| *instance* | *toolchain_allowed_tool_integration_ids* | *strings_in_list* | [Services that can be added to the list](https://github.com/open-toolchain/sdk/wiki/services.md){: external} | An array list of Strings that indicates the tools that can be bound to a toolchain. |
{: caption="Table 1. Rule properties for {{site.data.keyword.contdelivery_short}}" caption-side="top"}

To learn more about config rules, see [What is a config rule?](/docs/security-compliance?topic=security-compliance-what-is-rule).

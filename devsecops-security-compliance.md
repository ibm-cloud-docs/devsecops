---

copyright:
  years:  2021, 2022
lastupdated: "2022-06-07"

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

- 3000603 - Check whether Toolchain scans during continuous integration the source code to identify vulnerabilities.
- 3000604 - Check whether Toolchain has unit tests that are continuously run to validate source code changes.
- 3000605 - Check whether Toolchain has Code Risk Analyzer configured that collects a bill of materials for pipeline run.
- 3000606 - Check whether Toolchain is configured with image signing.
- 3000607 - Check whether Toolchain source code meets Center for Internet Security Docker benchmarks.
- 3000608 - Check whether Toolchain has branch protection rules enabled.
- 3000609 - Check whether Toolchain has secret detection scans enabled for source code.
- 3000610 - Check whether Toolchain production change request exists and is approved.
- 3000624 - Check whether Toolchain acceptance tests exist and have passed.
- 3000611 - Check whether Container Registry Vulnerability Advisor scans images for OS vulnerability detection.
- 3000641 - Check whether Toolchain static scan exists and have passed
- 3000642 - Check whether Toolchain dynamic scan exists and have passed

Use the {{site.data.keyword.cloud_notm}} Security Best Practices v1.0.0 profile for DevSecOps toolchains.
{: tip}

#### Toolchain properties
{: #scc-toolchain-properties}

Ensure that the tool integrations within the toolchain are included in the allow list parameter array only.

#### Identity and Access Management
{: #scc-iam-goals}

- 3000622 - Check whether Toolchain has no more than # users with the IAM administrator role.
- 3000629 - Check whether Toolchain has no more than # service IDs with the IAM administrator role.
- 3000633 - Check whether {{site.data.keyword.contdelivery_short}} has at least # users with the IAM manager role.
- 3000634 - Check whether {{site.data.keyword.contdelivery_short}} has at least # service IDs with the IAM manager role.
- 3000638 - Check whether Toolchain service access is managed only by IAM access groups.

## Governing {{site.data.keyword.contdelivery_short}} resource configuration
{: #scc-govern}

As a security or compliance focal, you can use the {{site.data.keyword.compliance_short}} to define configuration rules for the instances of {{site.data.keyword.contdelivery_short}} that you create.

[Config rules](#x3084914){: term} enforce the configuration standards that you want to implement across your accounts. To learn more about the data that you can use to create a rule for {{site.data.keyword.contdelivery_short}}, review Table 1.

| Resource type | Property | Operator | Value | Description |
|---------------|----------|---------------|-------|-------------|
| *instance* | *toolchain_allowed_tool_integration_ids* | *strings_in_list* | [Services that can be added to the list](https://github.com/open-toolchain/sdk/wiki/services.md){: external} | An array list of Strings that indicates the tools that can be bound to a toolchain. |
{: caption="Table 1. Rule properties for {{site.data.keyword.contdelivery_short}}" caption-side="top"}

To learn more about config rules, see [What is a config rule?](/docs/security-compliance?topic=security-compliance-what-is-rule).

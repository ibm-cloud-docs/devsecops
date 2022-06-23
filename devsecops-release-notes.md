---

copyright:
  years: 2022
lastupdated: "2022-06-23"

keywords: IBM Cloud DevSecOps release notes, Cloud DevSecOps changes, Cloud DevSecOps updates

subcollection: devsecops
content-type: release-note

---

{{site.data.keyword.attribute-definition-list}}

# Release notes for DevSecOps
{: #release-notes}

Use the release notes to learn about the latest changes to {{site.data.keyword.cloud_notm}} DevSecOps.
{: shortdesc}

## 24 March 2022
{: #devsecops-mar2422}
{: release-note}

### Introducing {{site.data.keyword.cloud_notm}} DevSecOps
{: #devsecops-intro}

DevSecOps is a methodology that integrates security practices with the software development and operations lifecycle. The goal of the merge is to prioritize the balance of development speed and security.

- New Continuous Compliance template that periodically scans your deployed application code.
- Support for Zap Dynamic scans that use async pipelines.
- Support for tracing the origin of default scripts that are running in the compliance pipelines.
- Incident issues support a "Due Date" field based on the issue severity.
- Format issues created by Continuous Compliance pipeline for better readability.
- Support for disabling static and dynamic scans that use optional environment properties.
- Updated {{site.data.keyword.cos_full_notm}} tool integration UI for easy setup.
- Support for querying environment tags from inventory repository while setting up CC and CD toolchain.
- New wizard support for customizing the welcome page.

{{site.data.keyword.DRA_full}} is not supported in the Continuous Compliance pipeline. Also, there is limited support for triggering dynamic scans for multiple apps out of the box.
{: note}

## 16 March 2022
{: #devsecops-mar1622}
{: release-note}

- Enhanced DevSecOps pipeline code to publish CRA results to {{site.data.keyword.DRA_short}} including BOM, vulnerability, and deployment records.
- Added support for the optional CRA plug-in parameter `--dockerfilepattern`.

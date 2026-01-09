---

copyright:
  years: 2021, 2026
lastupdated: "2026-01-09"

keywords: DevSecOps, architecture, shift left, compliance, secure, CI, CD, CC, IBM Cloud

subcollection: devsecops


---

{{site.data.keyword.attribute-definition-list}}

# DevSecOps Toolchain building blocks
{: #cd-devsecops-reusable-assets}

The DevSecOps reference implementation is composed of the following building blocks:

## Continuous Integration (CI) toolchain - Develop a secure app with DevSecOps practices
{: #cd-devsecops-reusable-ci}

A template that quickly provisions a complete CI solution. The template uses Tekton pipelines and integrates with various tools, such as Git repos, secrets storage, image registry, code scans, and object storage. The CI DevSecOps toolchain uses the guided wizard that is provided by the toolchain template to configure the toolchain and integrate different tools with the CI toolchain. When the toolchain is configured, you can customize the toolchain as follows, for example:

* Integrate more tools supported by the Continuous Delivery service. For example, update pipeline triggers.
* Update pipeline triggers that are provided by the toolchain or add more triggers to your pipelines.
* Update environment properties for your pipelines or add more properties to your pipelines.

## Continuous Deployment (CD) toolchain - Deploy a secure app with DevSecOps practices
{: #cd-devsecops-reusable-cd}

A template that quickly provisions a complete CD solution. The template uses Tekton pipelines and integrates with various tools and the [{{site.data.keyword.compliance_full}}](https://www.ibm.com/products/security-and-compliance-center){: external}. Tools include Git repos, secrets storage, image registry, and a change management system. The CD DevSecOps Toolchain uses the guided wizard that is provided by the toolchain template to configure the toolchain and integrate different tools with the CD toolchain. When the toolchain is configured, you can customize the toolchain as follows, for example:

* Integrate more tools supported by the CD Service. For example, update pipeline triggers.
* Update pipeline triggers that are provided by the toolchain or add more triggers to your pipelines.
* Update environment properties for your pipelines or add more properties for your pipelines.

Effective 15 Dec 2025, IBM Cloud Security and Compliance Center is deprecated. Any existing service instances are non-functional.
{: deprecated}

## Continuous Compliance (CC) toolchain - Ensure that you are running a secure and compliant application
{: #cd-devsecops-reusable-cc}

A template that ensures that your deployed artifacts and their source repositories are always compliant. The template uses Tekton pipelines and integrates with various tools and the [{{site.data.keyword.compliance_full}}](https://www.ibm.com/products/security-and-compliance-center){: external}. Tools include Git repos, secrets storage, image registry, and a change management system. The CC DevSecOps Toolchain uses the guided wizard that is provided by the toolchain template to configure the toolchain and integrate different tools with the CC toolchain. When the toolchain is configured, you can customize the toolchain as follows, for example:

* Integrate more tools supported by the CD Service. For example, update pipeline triggers.
* Update pipeline triggers provided by the toolchain or adds more triggers to your pipelines
* Update environment properties for your pipelines or add more properties for your pipelines

Effective 15 Dec 2025, IBM Cloud® Security and Compliance Center is deprecated. Any existing service instances are non-functional.
{: deprecated}

## Compliance Pipelines
{: #cd-devsecops-reusable-cp}

The Tekton pipeline definitions are referenced from a {{site.data.keyword.gitrepos}} repository that is hosted in the {{site.data.keyword.cloud_notm}} region. For instance, the pipeline definitions that are hosted in the Dallas region are in [compliance-pipelines](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-pipelines). During the pipeline runs, these definitions are used by the underlying Tekton worker nodes to run the pipeline.

These reference implementations of Tekton definitions are managed and updated periodically by IBM. During a new release, IBM updates the definitions in repositories that are hosted within different cloud regions. Predefined Tekton pipelines are configured to pull the latest pipeline definitions, which are automatically updated by IBM to provide fixes, including security fixes to all pipeline material. If new pipeline features become available, see [Release notes for DevSecOps](/docs/devsecops?topic=devsecops-release-notes). Although {{site.data.keyword.cloud_notm}} Continuous Delivery pipelines provide the flexibility to customize pipeline definitions, you should link DevSecOps toolchains to their reference implementation of compliance pipelines to maximize the benefits of IBM DevSecOps best practices.

## Continuous integration and continuous deployment resources
{: #cd-devsecops-learning-resources}

There are several useful enablement resources available to help you evaluate and adopt the continuous integration and continuous deployment templates. You might choose to start with the [Deploy a secure app with DevSecOps best practices](/docs/devsecops?topic=devsecops-tutorial-cd-devsecops) tutorial, which takes you through various common continuous integration, continuous deployment, and continuous compliance paths.

Review the additional useful learning aids to get started:

* [Getting started with {{site.data.keyword.contdelivery_full}}](/docs/ContinuousDelivery?topic=ContinuousDelivery-help-and-support)
* [Installing {{site.data.keyword.deliverypipeline}} private workers](/docs/ContinuousDelivery?topic=ContinuousDelivery-install-private-workers)
* [Working with Tekton pipelines](/docs/ContinuousDelivery?topic=ContinuousDelivery-tekton-pipelines)
* [Getting started with the {{site.data.keyword.cloud}} Command Line Interface (CLI)](/docs/cli?topic=cli-getting-started)
* [Continuous Deployment to Kubernetes](/docs/ContinuousDelivery?topic=ContinuousDelivery-tutorial-cd-kubernetes)
* [Getting started with {{site.data.keyword.containerlong}}](/docs/containers?topic=containers-getting-started)
* [Toolchain availability, templates, and tutorials](/docs/ContinuousDelivery?topic=ContinuousDelivery-cd_about)

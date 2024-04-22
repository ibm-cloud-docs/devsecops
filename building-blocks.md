---

copyright:
  years: 2021, 2024
lastupdated: "2024-03-08"

keywords: DevSecOps, architecture, shift left, compliance, secure, CI, CD, CC, IBM Cloud

subcollection: devsecops


---

{{site.data.keyword.attribute-definition-list}}

# DevSecOps toolchain building blocks
{: #cd-devsecops-reusable-assets}

Develop a secure app with DevSecOps practices and ensure that you are running a secure and compliant application. The DevSecOps reference implementation is composed of three different toolchains: Continuous Integration (CI), Continuous Deployment (CD), and Continuous Compliance (CC). Toolchains use a provided guide wizard that configures and integrates different tools, for example, Git repos, secrets storage, image registry, code scans, and object storage. CD and CC also integrate with the [{{site.data.keyword.compliance_full}}](https://www.ibm.com/cloud/security-and-compliance-center){: external}. For more information about toolchain templates, start with [Part 2: Set up a Continuous Integration (CI) toolchain](/docs/devsecops?topic=devsecops-tutorial-ci-toolchain).
{: shortdesc}

When each toolchain is configured, you can customize the toolchain with the following:

* Integrate more tools supported by the CD Service. For example, update pipeline triggers.
* Update pipeline triggers that are provided by the toolchain or add more triggers to your pipelines.
* Update environment properties for your pipelines or add more properties for your pipelines.

<!--thinking about removing these three sections with the revised version above
## Continuous Integration (CI) toolchain

A template that quickly provisions a complete CI solution. The template uses Tekton pipelines and integrates with various tools, such as Git repos, secrets storage, image registry, code scans, and object storage. The CI DevSecOps toolchain uses the guided wizard that is provided by the toolchain template to configure the toolchain and integrate different tools with the CI toolchain. When the toolchain is configured, you can customize the toolchain as follows, for example:

* Integrate more tools supported by the Continuous Delivery service. For example, update pipeline triggers.
* Update pipeline triggers that are provided by the toolchain or add more triggers to your pipelines.
* Update environment properties for your pipelines or add more properties to your pipelines.

## Continuous Deployment (CD) toolchain

A template that quickly provisions a complete CD solution. The template uses Tekton pipelines and integrates with various tools and the [{{site.data.keyword.compliance_full}}](https://www.ibm.com/cloud/security-and-compliance-center){: external}. Tools include Git repos, secrets storage, image registry, and a change management system. The CD DevSecOps Toolchain uses the guided wizard that is provided by the toolchain template to configure the toolchain and integrate different tools with the CD toolchain. When the toolchain is configured, you can customize the toolchain as follows, for example:

* Integrate more tools supported by the CD Service. For example, update pipeline triggers.
* Update pipeline triggers that are provided by the toolchain or add more triggers to your pipelines.
* Update environment properties for your pipelines or add more properties for your pipelines.

## Continuous Compliance (CC) toolchain

A template that ensures that your deployed artifacts and their source repositories are always compliant. The template uses Tekton pipelines and integrates with various tools and the [{{site.data.keyword.compliance_full}}](https://www.ibm.com/cloud/security-and-compliance-center){: external}. Tools include Git repos, secrets storage, image registry, and a change management system. The CC DevSecOps Toolchain uses the guided wizard that is provided by the toolchain template to configure the toolchain and integrate different tools with the CC toolchain. When the toolchain is configured, you can customize the toolchain as follows, for example:

* Integrate more tools supported by the CD Service. For example, update pipeline triggers.
* Update pipeline triggers provided by the toolchain or adds more triggers to your pipelines
* Update environment properties for your pipelines or add more properties for your pipelines
-->

## Compliance pipelines

Tekton pipeline definitions are referenced from a {{site.data.keyword.gitrepos}} repository that is hosted in the {{site.data.keyword.cloud_notm}} region. For instance, the pipeline definitions that are hosted in the Dallas region are in [compliance-pipelines](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-pipelines). During the pipeline runs, these definitions are used by the underlying Tekton worker nodes to run the pipeline. For more information, see [Working with Tekton pipelines](/docs/ContinuousDelivery?topic=ContinuousDelivery-tekton-pipelines).

These reference implementations of Tekton definitions are managed and updated periodically by IBM. During a new release, IBM updates the definitions in repositories that are hosted within different cloud regions. Predefined Tekton pipelines are configured to pull the latest pipeline definitions, which are automatically updated by IBM to provide fixes, including security fixes to all pipeline material. You can find out when new pipeline features become available by viewing the [Release notes for DevSecOps](/docs/devsecops?topic=devsecops-release-notes).

Although {{site.data.keyword.cloud_notm}} Continuous Delivery pipelines provide the flexibility to customize pipeline definitions, you should link DevSecOps toolchains to their reference implementation of compliance pipelines to maximize the benefits of IBM DevSecOps best practices.
{: note}

## Continuous integration and continuous deployment resources
{: #cd-devsecops-learning-resources}

There are useful enablement resources available to help you evaluate and adopt the continuous integration and continuous deployment templates. You might choose to start with the [Deploy a secure app with DevSecOps best practices](/docs/devsecops?topic=devsecops-tutorial-cd-devsecops) tutorial, which takes you through various common continuous integration, continuous deployment, and continuous compliance paths.

Review the additional useful learning aids to get started:

* [Installing {{site.data.keyword.deliverypipeline}} private workers](/docs/ContinuousDelivery?topic=ContinuousDelivery-install-private-workers) <!--I feel like this one could make a good tip earlier in the doc-->
* [Toolchain availability, templates, and tutorials](/docs/ContinuousDelivery?topic=ContinuousDelivery-cd_about)<!--this doc has toolchain links, but I don't see any specifically for the toolchains mentioned in this doc: CI, CC, or CD-->
* [Bringing your own app to DevSecOps](/docs/devsecops?topic=devsecops-cd-devsecops-apps-byoa)

<!--I feel like the following shouldn't be linked to in this doc because the information isn't referenced
* [Continuous Deployment to Kubernetes](/docs/solution-tutorials?topic=solution-tutorials-continuous-deployment-to-kubernetes)
* [Getting started with the {{site.data.keyword.cloud}} Command Line Interface (CLI)](/docs/cli?topic=cli-getting-started)
* [Getting started with {{site.data.keyword.containerlong}}](/docs/containers?topic=containers-getting-started)
-->

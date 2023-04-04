---

copyright: 
  years: 2021, 2022
lastupdated: "2023-03-30"

keywords: DevSecOps, architecture, compliance, secure, CI, CD, CC, IBM Cloud

subcollection: devsecops


---

{{site.data.keyword.attribute-definition-list}}

# DevSecOps architecture
{: #cd-devsecops-arch}

The DevSecOps reference architecture streamlines compliance and audit-readiness by using building blocks like Tekton task library and toolchain templates.
{: shortdesc}

Because the continuous integration, continuous deployment and continuous compliance processes are standardized, application development teams can be sure that they are following security best practices. The [DevSecOps](#x9892260){: term} reference architecture provides a set of predefined continuous integration, continuous deployment and continuous compliance toolchain templates. These templates use a collection of tool integrations and customizable reference Tekton pipelines for build, scan, test, change management, and deploy. The pipelines definitions are managed and updated by IBM periodically unless you change the default setting when creating the toolchains. 

The Tekton pipelines provide a framework of custom scripts that you can use to ensure the compliant and automated orchestration of code and configuration changes. The pipelines can also help to maintain a GitOps release inventory, while it collects and stores evidence that can be used to generate auditable change requests.

You can use the pipelines to deploy to public cloud or hybrid target environments by using Tekton private pipeline workers. The Tekton pipelines run in predefined container images along with some user scripts. You can run anything that can be scripted, within boundaries of the Tekton pipelines.

## Shift-Left continuous integration, continuous deployment design
{: #cd-devsecops-design}

The following diagram shows the implementation workflow.

![Reference implementation workflow](images/cm-arch.png "Reference implementation workflow"){: caption="Figure 1. Reference implementation workflow" caption-side="bottom"}

### Main features
{: #cd-devsecops-features}

* Pull or merge request validation uses a specific pull request pipeline that echoes status back to the pull request. This feature allows developers to find problems early in the development cycle.
* Integration is achieved by using a continuous integration pipeline that implements many controls out of the box and collects normalized evidence for these integrations in an evidence repository (repo). It also writes metadata on the artifacts to deploy into an artifacts inventory repo, which enables GitOps practices.
* Delivery is performed by using a continuous deployment pipeline to process content from the artifacts inventory and collect evidence to generate a change request for each deployment. It can also record compliance facts in the {{site.data.keyword.compliance_short}}, and these facts can be assessed against a compliance profile. 

As part of a continuous deployment staging deployment, continuous integration pipelines typically emit to the inventory master branch by using pull requests to promote the changes to a staging branch. Then, when ready, the changes are promoted again from staging to production with a subsequent branch promotion. 

To drive a coordinated release deployment, you can have multiple continuous deployment pipelines that output to a shared inventory and evidence repo. 

Only one change request is created when multiple artifacts in that inventory are promoted.
{: tip}

You can have multiple continuous deployment pipelines that pull from a shared inventory and evidence repo, which enables multiple environments to be targeted from the same continuous integration input.

## DevSecOps Toolchain building blocks
{: #cd-devsecops-reusable-assets}

The DevSecOps reference implementation is composed of the following building blocks:

* **Continuous Integration (CI) Toolchain - Develop a secure app with DevSecOps practices**: A template for quickly provisioning a complete continuous integration solution. The template uses Tekton pipelines and integrates with various tools, such as Git repos, secrets storage, image registry, code scans, and object storage. The users of CI (Continuous Integration) DevSecOps Toolchain utilizes the guided wizard provided by the toolchain template to setup the toolchain and integrate different tools with their CI toolchain. Once the toolchain is setup, users can customize the toolchain for instance to:

     - Integrate additional tools supported by the Continuous Delivery Service, update pipeline triggers 
     - Update pipeline triggers provided by the toolchain or add additional triggers to your pipelines
     - Update environment properties for your pipelines or add additional properties for your pipelines  

* **Continuous Deployment (CD) Toolchain - Deploy a secure app with DevSecOps practices**:  A template for quickly provisioning a complete continuous deployment solution. The template uses Tekton pipelines and integrates with various tools and the [{{site.data.keyword.compliance_full}}](https://www.ibm.com/cloud/security-and-compliance-center){: external}. Tools include Git repos, secrets storage, image registry, and a change management system. The users of CD (Continuous Deployment) DevSecOps Toolchain utilizes the guided wizard provided by the toolchain template to setup the toolchain and integrate different tools with their CD toolchain. Once the toolchain is setup, users can customize the toolchain for instance to:

    - Integrate additional tools supported by the Continuous Delivery Service, update pipeline triggers 
    - Update pipeline triggers provided by the toolchain or add additional triggers to your pipelines
    - Update environment properties for your pipelines or add additional properties for your pipelines

* **Continuous Compliance (CC) Toolchain - Ensure running a secure and compliant application**: A template for ensuring that your deployed artifacts and their source repositories are always compliant. The template uses Tekton pipelines and integrates with various tools and the [{{site.data.keyword.compliance_full}}](https://www.ibm.com/cloud/security-and-compliance-center){: external}. Tools include Git repos, secrets storage, image registry, and a change management system. The users of CC (Continuous Compliance) DevSecOps Toolchain utilizes the guided wizard provided by the toolchain template to setup the toolchain and integrate different tools with their CC toolchain. Once the toolchain is setup, users can customize the toolchain for instance to:

     - Integrate additional tools supported by the Continuous Delivery Service, update pipeline triggers 
     - Update pipeline triggers provided by the toolchain or add additional triggers to your pipelines
     - Update environment properties for your pipelines or add additional properties for your pipelines

* **Compliance Pipelines**: The tekton pipeline definitions are referenced from a GRIT (Git Repos and Issue Tracking) repository hosted in the IBM Cloud Region. These reference implementation of tekton definitions are managed and updated periodically by IBM. While IBM Cloud Continuous Delivery pipelines provides flexibility to customize pipeline definitions, it is strongly recommended that users of the DevSecOps Toolchains link only to the reference implementation of compliance pipelines as it adheres to DevSecOps best practices.

## Learning resources
{: #cd-devsecops-learning-resources}

There a number of useful enablement resources available to help you evaluate and adopt the continuous integration and continuous deployment templates. You might choose to start with the [Deploy a secure app with DevSecOps best practices](/docs/devsecops?topic=devsecops-tutorial-cd-devsecops) tutorial, which takes you through various common continuous integration, continuous deployment, and continuous compliance paths.

Other useful learning aids are available as follows:

* [Getting started with {{site.data.keyword.contdelivery_full}}](/docs/ContinuousDelivery?topic=ContinuousDelivery-getting-started)
* [Installing {{site.data.keyword.deliverypipeline}} private workers](/docs/ContinuousDelivery?topic=ContinuousDelivery-install-private-workers)
* [Working with Tekton pipelines](/docs/ContinuousDelivery?topic=ContinuousDelivery-tekton-pipelines)
* [Getting started with the {{site.data.keyword.cloud}} Command Line Interface (CLI)](/docs/cli?topic=cli-getting-started)
* [Continuous Deployment to Kubernetes](/docs/solution-tutorials?topic=solution-tutorials-continuous-deployment-to-kubernetes)
* [Getting started with {{site.data.keyword.containerlong}}](/docs/containers?topic=containers-getting-started)
* [Toolchain availability, templates, and tutorials](/docs/ContinuousDelivery?topic=ContinuousDelivery-cd_about)

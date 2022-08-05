---

copyright:
  years:  2021, 2022
lastupdated: "2022-08-05"

keywords: IBM Cloud DevOps, DevSecOps

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# DevSecOps with {{site.data.keyword.contdelivery_short}}
{: #devsecops_intro}

[DevSecOps](https://www.ibm.com/cloud/learn/devsecops){: external} is an evolution of Agile and DevOps, integrating secure development best practices as early as possible in the software delivery lifecycle (also known as "shift left"). This approach prevents security problems from reaching production systems and failing corporate audits.
{: shortdesc}

[DevSecOps](#x9892260){: term} requires automating security and compliance controls as part of continuous integration and continuous delivery processes. Evidence of these controls is also collected to demonstrate to auditors that every change in history meets the necessary controls. 

Aligned with the requirements of the Financial Services industry, {{site.data.keyword.contdelivery_short}} provides a reference implementation of [NIST Configuration Management](https://csrc.nist.gov/Projects/risk-management/sp800-53-controls/release-search#!/controls?version=5.1&family=CM){: external} controls as a service. You can configure this service in a few clicks by using templates.

DevSecOps uses {{site.data.keyword.contdelivery_short}} ({{site.data.keyword.gitrepos}}, Tekton Pipelines, {{site.data.keyword.DRA_short}}, Code Risk Analyzer, and Eclipse Orion {{site.data.keyword.webide}}), and Secrets Manager and Key Protect, Cloud Object Storage, and Container Registry. It can also be used with more tools such as external Git providers and artifact stores. DevSecOps supports hybrid deployments, in particular by using private pipeline workers, and can be interfaced with other deployment tools such as Satellite Config and ArgoCD.

## Background
{: #cd-devsecops-background}

Agile and DevOps provide for automation and greater development velocity, software quality, and consistency and reliability in the delivery processes while they remove the potential for human error. DevSecOps integrates a set of security and compliance controls into the DevOps processes. This feature allows organizations to deliver rapidly and often while they maintain a strong security posture and continuous state of audit-readiness.

Like Agile and DevOps, DevSecOps introduces a culture change and removes the traditional barriers between Developers, Operations, and Security and Compliance officers. Development teams take greater ownership of security rather than relying on external teams to evaluate their software for security, often late in the development cycle when discovered vulnerabilities are  more disruptive and expensive to remediate. The old model of having a siloed security team does not scale and does not meet the needs of a development project in a cloud-native DevOps domain. With DevSecOps, a security team's purpose evolves to more of a supportive, advising, and governance role.

With DevSecOps, developers can continue to deliver updates frequently with a minimal amount of friction. Integration of automated security scans and controls into the DevOps pipelines provides confidence with evidence to the change management process. This integration also supports automated approvals of new deployments to production when all secure criteria are met and specific checks pass. Introducing security and compliance early into the development and deployment process reduces costs and risk by identifying problems early before the software reaches a production environment. This approach is often referred to as "shift-left". Moreover, even if secure best practices are part of the training in every organization, developers might lack deep expertise at the security level. Applying DevSecOps and shift-left to the delivery lifecycle reduces the risk of naively releasing known vulnerabilities into production.

Organizations can take advantage of the significant benefits of using a standard set of processes for continuous integration and continuous delivery by multiple disparate teams that develop heterogeneous offerings and components. These benefits include consistency and economies of scales. When a single set of prescriptive reference pipelines that are instrumented for compliance are used for all of the components across an organization, developers spend less time to develop automation solutions and can focus on feature development. The development leaders and security officers can be confident that the necessary controls are in place to ensure secure, compliant software and provide evidence that can be used in an audit. Security audits within an organization can be streamlined when a standard implementation is used, as opposed to each component or offering team that uses their own compliance implementation.

Learn more about [DevSecOps](https://www.ibm.com/cloud/learn/devsecops){: external}.

For more information about the DevSecOps reference architecture, see [DevSecOps architecture](/docs/devsecops?topic=devsecops-cd-devsecops-arch).

Get started and [deploy a secure app with DevSecOps best practices](/docs/devsecops?topic=devsecops-tutorial-cd-devsecops).

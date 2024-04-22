---

copyright:
  years: 2022, 2024
lastupdated: "2024-03-11"

keywords: IBM Cloud DevOps, DevSecOps

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# DevSecOps with {{site.data.keyword.contdelivery_short}}
{: #devsecops_intro}

[DevSecOps](#x9892260){: term} has the mantra "shift left" because it integrates secure development best practices as early as possible in the software delivery lifecycle. Like Agile and [DevOps](#x5784896), DevSecOps introduces a culture change and removes the traditional barriers between developers, operations, and security and compliance officers. This approach prevents security problems from reaching production systems and failing corporate audits because it requires automating security and compliance controls as part of continuous integration and continuous delivery processes. For more information about DevSecOps, see [What is DevSecOps?](https://www.ibm.com/cloud/learn/devsecops){: external}.
{: shortdesc}

DevSecOps uses Continuous Delivery Tekton with tools integration such as Secrets Manager, Key Protect, Cloud Object Storage, and Container Registry. DevSecOps also uses popular scanning tools such as SonarQube (static scan), Gosec (static scan), Code Risk Analyzer (static scan), OWASP Zap (dynamic scan), and many more. For more information, see [Supported scanning tools](/docs/devsecops?topic=devsecops-cd-devsecops-supported-scanning-tools).

Continuous Delivery provides a core set of tools for any DevSecOps toolchain. Aligned with the requirements of the Financial Services industry, Continuous Delivery provides a reference implementation of [NIST Configuration Management](https://csrc.nist.gov/Projects/risk-management/sp800-53-controls/release-search#!/controls?version=5.1&family=CM){: external} controls as a service. You can configure this service in a few clicks by using templates. To get started, see [Part 1: Set up prerequisites](/docs/devsecops?topic=devsecops-tutorial-cd-devsecops).

## How does DevSecOps work?
{: #devsecops-background}

Agile and DevOps provide for automation and greater development velocity, software quality, and consistency and reliability in the delivery processes while they remove the potential for human error. DevSecOps integrates a set of security and compliance controls into the DevOps processes. This feature allows organizations to deliver rapidly and often while they maintain a strong security posture and a continuous state of audit-readiness.

Development teams take greater ownership of security rather than relying on external teams to evaluate their software for security, often late in the development cycle when discovered vulnerabilities are more disruptive and expensive to remediate. The old model of having a siloed security team does not scale and does not meet the needs of a development project in a cloud-native DevOps domain. With DevSecOps, a security team's purpose evolves to more of a supportive, advising, and governance role.

## Why should I use DevSecOps?
{: #why-devsecops}

With DevSecOps, developers can continue to deliver updates frequently with a minimal amount of friction. Integration of automated security scans and controls into the DevOps pipelines provides confidence with evidence to the change management process. This integration also supports automated approval of new deployments to production when all secure criteria are met and specific checks pass. Introducing security and compliance early into the development and deployment process reduces costs and risk by identifying problems early before the software reaches a production environment. This approach is often referred to as "shift left". Moreover, even if secure best practices are part of the training in every organization, developers might lack deep expertise at the security level. Applying DevSecOps and shift left to the delivery lifecycle reduces the risk of naively releasing known vulnerabilities into production.

## Standardization across organizations
{: #standardization}

With a standard set of processes for continuous integration and continuous delivery, you can work with different teams to develop diverse offerings with consistency and economies of scale. When using a single set of prescriptive pipelines for compliance, for all components across an organization, developers can spend less time developing automation solutions to focus on feature development. Development leaders and security officers can be confident that the necessary controls are in place to ensure secure, compliant software and provide evidence that can be used in an audit. Security audits within an organization can be streamlined when a standard implementation is used, as opposed to each component or offering team that uses their own compliance implementation.

Ready to incorporate DevSecOps? Check out our tutorial series that guides you to [deploy a secure app with DevSecOps best practices](/docs/devsecops?topic=devsecops-tutorial-cd-devsecops). You can also learn more about the [benefits of DevSecOps](https://www.ibm.com/topics/devsecops#Benefits+of+DevSecOps){: external} and related [solutions](https://www.ibm.com/topics/devsecops#Related+solutions){: external}.

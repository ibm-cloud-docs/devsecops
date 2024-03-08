---

copyright:
  years: 2022, 2024
lastupdated: "2024-03-08"

keywords: IBM Cloud DevOps, DevSecOps

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# OnePipeline and DevSecOps
{: #OnePipeline_intro}

OnePipeline helps streamline internal compliance audits by standardizing CI/CD processes for services adhering to the IBM Cloud Service Framework (see also System architecture). OnePipeline provides a set of predefined CI/CD templates kept current as the Service Framework addresses FS-Cloud, then FedRAMP and more in the future.

# DevSecOps vs OnePipeline {{site.data.keyword.contdelivery_short}}
{: #devsecops_onepipeline_difference}

### Complex table
{: #complex-table-test}

|                    | OnePipeline (Internal)                                                      | Devsecops (External)                |
|--------------------|--------------------------------------------------------------------------------------|-----------------------------------------------------|
| Tools          | Change,management tool used here is Service Now                                   | Change management tool used here is  Github  |
| Repository     | github.ibm.com                         |  github.com                                     |
| Image sign-in       |  Users use internal sign-in methods like W3ID.  | GPT is used to sign image |
{: row-headers}
{: caption="Table 1. Example platform management roles and actions for services in an account" caption-side="bottom"}
{: summary="The first row of the table describes separate options that you can choose to your required service. The remaining cells provide the major differences between DevSecOps and OnePipeLine."}
{: #diffdevsecopsstable1}


# Standardization across organizations
{: #standardization}

Organizations can take advantage of the significant benefits of using a standard set of processes for continuous integration and continuous delivery by multiple disparate teams that develop heterogeneous offerings and components. These benefits include consistency and economies of scales. When a single set of prescriptive reference pipelines that are instrumented for compliance are used for all of the components across an organization, developers spend less time to develop automation solutions and can focus on feature development. The development leaders and security officers can be confident that the necessary controls are in place to ensure secure, compliant software and provide evidence that can be used in an audit. Security audits within an organization can be streamlined when a standard implementation is used, as opposed to each component or offering team that uses their own compliance implementation.

Ready to incorporate DevSecOps? Check out our tutorial series that guides you to [deploy a secure app with DevSecOps best practices](/docs/devsecops?topic=devsecops-tutorial-cd-devsecops). You can also learn more about the [benefits of DevSecOps](https://www.ibm.com/topics/devsecops#Benefits+of+DevSecOps){: external} and related [solutions](https://www.ibm.com/topics/devsecops#Related+solutions){: external}.

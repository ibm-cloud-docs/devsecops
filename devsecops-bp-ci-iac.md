---

copyright:
  years: 2022, 2024
lastupdated: "2024-02-22"

keywords: DevSecOps, ibm cloud, best practices, secure application, continuous integration,

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Best practices for CI toolchain IaC
{: #practices-ci-toolchain-iac}

The implementation of continuous integration (CI) toolchain for Infrastructure as Code (IaC) DevSecOps follows these practices.
{: shortdesc}

- Runs a static code scanner on the infrastructure code repositories and performs Terraform linting checks on the infrastructure code.
- Runs compliance checks on the infrastructure code to detect secrets and security vulnerabilities.
- Builds artifacts on every Git commit.
- Stores the built artifacts metadata in the inventory repository.
- Automatically builds and validates any code that is merged into the target Git repository branch.

![DevSecOps IaC CI toolchain](images/devsecops-ci-terraform-toolchain-diagram.png){: caption="DevSecOps CI toolchain" caption-side="bottom"}

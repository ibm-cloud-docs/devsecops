---

copyright:
  years: 2025, 2025
lastupdated: "2025-04-16"

keywords: DevSecOps, IBM Cloud, compliance, SBOM

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Software Bill Of Materials
{: #devsecops-sbom}

A SBOM (Software Bill Of Materials) is a structured inventory of all components, dependencies, and licenses in a software application. It provides detailed metadata, including component names, versions, licenses, vulnerabilities, and dependencies, helping organizations track open source and third-party software for security, compliance, and risk management.
{: shortdesc}

You can easily export your SBOM in standardized formats (SPDX, CycloneDX) and import third-party SBOMs while using VEX data to meet government and customer requirements.



During the pipeline run, there are certain tools that are used to generate SBOMs. The tool currently available to generate a SBOM is :

- [Code Risk Analyzer](/docs/devsecops?topic=devsecops-devsecops-cra-sbom-generate)

## What are package SBOMs?
{: #package-sbom}

A package SBOM details all information about all the packages that are used in a software project. It provides information on the versions, licenses, and vulnerabilities of each package used. Package SBOMs are generated at a microservice level; a SBOM is generated for each microservice in an architecture.

## SBOM Lifecycle
{: #sbom-lifecycle}

A Software Bill Of Materials is created at every step during the pipeline run: 

- Continuous Integration(CI) pipeline - During the CI pipeline run, a SBOM is generated for each microservice built. 
- Continuous Deployment(CD) pipeline - During the CD pipeline run, a release SBOM is generated for all microservices deployed.
- Continuous Compliance(CC) pipeline - After the CD pipeline run is completed, the CC pipeline continuously monitors the deployed application. During the run of this CC pipeline, the SBOMs generated for each microservice during the run of the CI pipeline are retrieved and scanned.

![Lifecycle of a SBOM](/images/sbom-lifecycle.png){: caption="Lifecycle of a SBOM" caption-side="bottom"}

### SBOM Lifecycle in the Continuous Integration Pipeline
{: #ci-pipeline-sbom}

The Continuous Integration pipeline builds the deployable artifacts from the application (app) repositories (repos). 

When the CI pipeline runs, at the `code-compliance-checks` step,  CRA runs, and creates a package SBOM for the repository from which the deployed artifact is created. This package SBOM contains information about the packages that are used by each microservice that is being built in the CI pipeline. It gives information about the license, version, and vulnerabilities of each package. 

This package SBOM is stored in the evidence locker of the pipeline for further use during deployment to production.

### SBOM Lifecycle in the Continuous Deployment Pipeline
{: #cd-pipeline-sbom}

The continuous deployment pipeline gathers all evidence and generates the change request summary content.

When the deployment to production takes place through the CD pipeline, the package SBOM that is stored in the evidence locker is fetched. These SBOMs are then aggregated to form a release SBOM. The release SBOM is added as an attachment in the change request.

### SBOM Lifecycle in the Continuous Compliance Pipeline
{: #cc-pipeline-sbom}

The continuous compliance pipeline periodically scans the deployed artifacts and their source repositories for vulnerabilities, post deployment to production. 

When the CC pipeline runs, it retrieves the previously stored package SBOM that was generated during the CI pipeline run from the evidence locker. The CC pipeline does not re-create the Package SBOMs. 

## SBOM Utility for SBOM Validation.
{: #generate-cyclonedx-sbom-ci-pipeline}

When the pipeline is run, the `BOM` is added as an attachment to the `com.ibm.code_bom_check` evidence in both `standard` and `cyclonedx` format.
{: note}

The `sbom-utility` tool validates the software bill of materials (SBOM) by completing a series of default and custom checks to see whether the metadata contains:

* A component at the root level of the SBOM that contains data
* A timestamp
* A `name` and `bom-ref` for each component field
    
To collect evidence for SBOM validation, set `sbom-validation-collect-evidence` to `1`. The evidence is collected as an attachment to `com.ibm.code_bom_check` as evidence type and tool type as `sbom-utility`. 
{: note}

For more information, see the [`sbom-utility` tool docs](https://github.com/CycloneDX/sbom-utility/blob/main/sbom-validation-tests.md). 

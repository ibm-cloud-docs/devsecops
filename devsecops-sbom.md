---

copyright:
  years: 2025, 2025
lastupdated: "2025-04-15"

keywords: DevSecOps, IBM Cloud, compliance, SBOM

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Software Bill of Materials
{: #devsecops-sbom}

A SBoM (Software Bill of Materials) is a structured inventory of all components, dependencies, and licenses in a software application. It provides detailed metadata, including component names, versions, licenses, vulnerabilities, and dependencies, helping organizations track open-source and third-party software for security, compliance, and risk management.
{: shortdesc}

You can easily export your SBoM in standardized formats (SPDX, CycloneDX) and import third-party SBoMs while leveraging VEX data to meet government and customer requirements.



There are tools which run in a pipeline that generate SBoMs during the pipeline run. The tool currently available to generate a SBoM is :

- [Code Risk Analyser](/docs/devsecops?topic=devsecops-devsecops-cra-sbom-generate)

## What are package SBoMs?
{: #package-sbom}

A package SBoM details all information about all the packages utilized in a software project. It provides information on the versions, licenses and vulnerabilities of each package used. Package SBoMs are generated at a microservice level; a SBoM is generated for each microservice in an architecture.

## SBoM Lifecycle
{: #sbom-lifecycle}

A Software Bill of Materials is created at every step during the pipeline run: 

- Continuous Integration(CI) pipeline - During the CI pipeline run, a SBoM is generated for each microservice built. 
- Continuous Deployment(CD) pipeline - During the CD pipeline run, a release SBoM is generated for all microservices deployed.
- Continuous Compliance(CC) pipeline - Once the CD pipeline run is completed, the CC pipeline continuously monitors the deployed application. During the run of this CC pipeline, the SBoMs generated for each microservice during the run of the CI pipeline are retrieved and scanned.

![Lifecycle of a SBoM](/images/sbom-lifecycle.png){: caption="Lifecycle of a SBoM" caption-side="bottom"}

### SBoM Lifecycle in the Continuous Integration Pipeline
{: #ci-pipeline-sbom}

The Continuous Integration pipeline builds the deployable artifacts from the application (app) repositories (repos). 

When the CI pipeline runs, at the `code-compliance-checks` step ,  CRA runs, and creates a package SBoM for the repository from which the deployed artifact is created. This package SBoM contains information regarding the packages that are used by each microservice that is being built in the CI pipeline. It gives information about the license,version and vulnerabilities of each package. 

This package SBoM is stored in the evidence locker of the pipeline for further use during deployment to production.

### SBoM Lifecycle in the Continuous Deployment Pipeline
{: #cd-pipeline-sbom}

The continuous deployment pipeline gathers all of the evidence and generates the change request summary content.

When the deployment to production takes place through the CD pipeline , the package SBoM that is stored in the evidence locker is fetched. These SBoMs are then aggregated to form a release SBoM. The release SBoM is added as an attachment in the change request.

### SBoM Lifecycle in the Continuous Compliance Pipeline
{: #cc-pipeline-sbom}

The continuous compliance pipeline periodically scans the deployed artifacts and their source repositories. It checks for newer vulnerabilities, once the artifacts are deployed in production.

When the CC pipeline runs, it retrieves the previously stored package SBoM, that was generated during the CI pipeline run, from the evidence locker. The CC pipeline does not recreate the Package SBoMs. 

## SBoM Utility for SBoM Validation.
{: #generate-cyclonedx-sbom-ci-pipeline}

When the pipeline is run, the `BOM` is added as an attachment to the `com.ibm.code_bom_check` evidence in both `standard` and `cyclonedx` format.
{: note}

The `sbom-utility` tool validates the software bill of materials (SBoM) by completing a series of default and custom checks to see whether the metadata contains:
    * A component at the root level of the SBoM that contains data
    * A timestamp
    * A `name` and `bom-ref` for each component field
    
In order to collect evidence for sbom validation, set `sbom-validation-collect-evidence` to `1`.The evidence is collected as an attachment to `com.ibm.code_bom_check` as evidence type and tool type as `sbom-utility`. 
{: note}

For more information, see the [`sbom-utility` tool docs](https://github.com/CycloneDX/sbom-utility/blob/main/sbom-validation-tests.md). 

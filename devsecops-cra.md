---

copyright:
  years: 2023, 2023
lastupdated: "2023-05-26"

keywords: DevSecOps, IBM Cloud, compliance, cra

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Code Risk Analyzer scans
{: #cd-devsecops-cra-scans}

Code Risk Analyzer (CRA) analyzes your code for vulnerabilities and compliance with certain rules.
{: shortdesc} 

Code Risk Analyzer is available in all {{site.data.keyword.cloud_notm}} regions where toolchains are supported. For more information, see [Code Risk Analyzer plug-in for {{site.data.keyword.cloud_notm}}](/docs/code-risk-analyzer-cli-plugin?topic=code-risk-analyzer-cli-plugin-cra-cli-plugin).

CRA scans perform the following tasks:

1. Generates a Bill of Materials (BOM) file that lists the dependencies and available license information of all third-party OS packages and application packages. You can also generate this output in CycloneDX-specific format.
2. Discovers vulnerabilities in packages that are listed in the BOM file. You can also view the generated report in CycloneDX-specific format, or use vulnerability auto-remediation for Node.js, Maven, or Gradle applications.
3. Analyzes Kubernetes files for compliance with certain rules.
4. Analyzes Terraform plans for compliance with certain rules.

# Configuring CRA scans
{: #cd-devsecops-cra-scans-cfg}

No specific pipeline environment variables are needed to generate the Bill of Materials and to discover vulnerabilities. 

For generating the BOM file, the CRA scan command accesses artifacts in the specified directory path and performs deep discovery to identify all of the dependencies, including transitive dependencies. If the path contains Dockerfiles, it pulls down base images and build images for every build stage in each Dockerfile. 

If your Dockerfile requires ARGS, set an individual ARG as an environment variable in a custom script and provide the location of the custom script as the pipeline environment variable.

| Pipeline environment variables | Description | Required or optional | Purpose |
|----|----|----|----|
| `cra-custom-script-path` | Path to the script in your repo. | Optional | Sets environment variables that are needed by the Docker script. |
{: caption="Table 1. Docker script environment variables" caption-side="bottom"}

Analyzing Terraform files is turned off by default. You can turn on Terraform analysis by using the following pipeline environment variables.

| Pipeline environment variables | Description | Required or optional | Purpose |
|----|----|----|----|
| `opt-in-tfsec` | True or false. Set to false by default. | Optional | Scans Terraform files for security. |
| `opt-in-cra-tf-validate` | True or false. Set to false by default. | Optional | Validates Terraform files. |
{: caption="Table 2. Terraform analysis environment variables" caption-side="bottom"}

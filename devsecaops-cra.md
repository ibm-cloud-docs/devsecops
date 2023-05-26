---

copyright:
  years: 2022
lastupdated: "2023-05-23"

keywords: DevSecOps, IBM Cloud, compliance, cra

subcollection: devsecops

---

# CRA scans
{: #cd-devsecops-cra-scans}

Code Risk Analyzer (CRA) is a tool developed by IBM to analyze your code for vulnerabilities and compliance with certain rules. Code Risk Analyzer is available in all IBM Cloud regions where toolchains are supported. For more information, see [Code Risk Analyzer plug-in for IBM Cloud](https://cloud.ibm.com/docs/code-risk-analyzer-cli-plugin?topic=code-risk-analyzer-cli-plugin-cra-cli-plugin)

CRA scans performs the following tasks
1. Generate a Bill of Materials (BOM) file that lists the dependencies and available license information of all third-party OS packages and application packages. You can also generate this output in CycloneDX-specific format.
2. Discover vulnerabilities in packages that are listed in the BOM file. You can also view the generated report in CycloneDX-specific format, or use vulnerability auto-remediation for Node.js, Maven, or Gradle applications.
3. Analyze Kubernetes files for compliance with certain rules.
4. Analyze a Terraform plan for compliance with certain rules.

# Configure CRA scans
There are no specific pipeline environment variables needed to generate the Bill of Materials and discover vulnerabilities. 

For generating the BOM file, the CRA scan command accesses artifacts in the specified directory path and performs deep discovery to identify all of the dependencies, including transitive dependencies. If the path contains Dockerfiles, it pulls down base images and build images for every build stage in each Dockerfile. If your Dockerfile requires ARGS, set an individual ARG as an environment variable in a custom script and provide the location of the custom script as the pipeline environment variable.
| Pipeline environment variables | comment | required? | purpose |
|----|----|----|----|
| cra-custom-script-path | path to the script in your repo | optional | set environment variables needed by docker script |


Analyzing Terraform files is turned off by default and can be turned on by the following pipeline environment variables
| Pipeline environment variables | comment | required? | purpose |
|----|----|----|----|
| opt-in-tfsec | true / false, set to false by default | optional | Scan terraform files for security |
| opt-in-cra-tf-validate | true / false, set to false by default | optional | Validate terraform files |


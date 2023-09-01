---

copyright:
  years: 2023, 2023
lastupdated: "2023-09-01"

keywords: DevSecOps, IBM Cloud, compliance, cra

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Scanning code for risk
{: #cd-devsecops-cra-scans}
You can use {[cra-full]} to analyze your code for vulnerabilities and validate compliance against specified rules. 
{: shortdesc} 

When {[cra]} evaluates your app:
* A software bill of materials (SBOM) is generated that lists the dependencies and available license information of all third-party packages.
   This can be generated in `cycloneDX-specific` format if needed.
   {: tip}
   
* The packages listed in the SBOM file are scanned for vulnerabilities.
   This file can be generated in `cycloneDX-specific` format. Alternatively, you can enable auto-remediation of vulnerabilties that are found for applications written in Node.js, Maven, or Gradle.
   {: tip}
   
* All of your Kubernetes files are analyzed for compliance.
* All of your Terraform plans are analyzed for compliance.
{[cra]} is available in all {[cloud]} regions where toolchains are supported. For more information, see [the plug-in documentation](/docs/code-risk-analyzer-cli-plugin?topic=code-risk-analyzer-cli-plugin-cra-cli-plugin).
{: note}

## Configuring scans
{: #cd-devsecops-cra-scans-cfg}

To generate the SBOM file, a {[cra]} scan accesses artifacts in the specified directory path and performs deep discovery to identify all of the dependencies; including trasitive dependencies. There are no specific pipeline environment variables that are needed to generate the SBOM file and discover vulnerabilities, but if the path contains a Dockerfile, the base image is pulled and images are built for each build stage specified in the file.

If your Dockerfile requires ARGS, set an individual ARG as an environment variable in a custom script and provide the location of the custom script as the pipeline vairable.
| Environment variables | Description | Required or optional | Purpose |
|----|----|----|----|
| `cra-custom-script-path` | Path to the script in your repo. | Optional | Sets environment variables that are needed by the Docker script. |
{: caption="Table 1. Docker script environment variables" caption-side="bottom"}

By default, Terraform files are not analyzed. To scan your Terraform file, you must set the following pipeline environment variables.

| Environment variables | Description | Required or optional | Purpose |
|----|----|----|----|
| `opt-in-tfsec` | True or false. Set to false by default. | Optional | Scans Terraform files for security. |
| `opt-in-cra-tf-validate` | True or false. Set to false by default. | Optional | Validates Terraform files. |
{: caption="Table 2. Terraform analysis environment variables" caption-side="bottom"}

## Omitting vulnerabilities to deploy your app
{: #devsecops-omit-vulnerabilities}

The {[cra]} is designed to find vulnerable packages before an adivsory board does. In exceptional circumstances, you might need to deploy your application even if vulnerable packages have been detected and have not yet been fixed. Whenever possible, you should update the packages or remove them before redeploying your code.

To ignore specific files and complete your push to production, you can create a file called `.cra/.cveignore` in the scanned repository's root with the following schema:

```json
[
  {
    "cve": "string",
    "alwaysOmit": "bool",
    "untilRemediationAvailable": "bool",
    "expiration": "string"
  },
]
```
{: codeblock}

The following code snippet shows a sample `.cveignore` file:

```json
[
  {
    "cve": "CVE-2021-27290",
    "alwaysOmit": true
  },
  {
    "cve": "CVE-2020-8244",
    "untilRemediationAvailable": true,
  },
  {
    "cve": "SNYK-JAVA-ORGAPACHEHTTPCOMPONENTS-31517",
    "expiration": "2022-02-10T22:08:41+04:00",
    "comment": "Additional security measures are in place"
  }
]
```
{: screen}

This file used to be known as `.craveomit`. The original file format has been [deprecated](/docs/ContinuousDelivery?topic=ContinuousDelivery-cd-configure-cra-repos) and replaced by `.cra/.cveignore`. Please update your code to use the most recent file.
{: note}

You can learn more about the Code Risk Analyzer [plug-in's docs](/docs/code-risk-analyzer-cli-plugin), or you can also check the [deprecated CRA docs](/docs/ContinuousDelivery?topic=ContinuousDelivery-cd-configure-cra-repos).

## Automatically remediating vulnerabilities
{: #cd-devsecops-auto-remediate}

You can use the {[cra]} as part of the Continuous Compliance (CC) pipeline to not only find but automatically remediate vulnerabiltiies. The {[cra]} works as part of the `compliance-check` step of the pipeline. By default, auto-remediation is turned off.

Auto-remediation is available for the CC pipeline only.
{: note}

When auto-remediation is turned on, the {[cra]} attempts to update a vulnerable package to a non-vunlnerable version by creating a pull request with the identified fixes against the default branch of your repo. By default, the {[cra]} will update minor and patch versions for the main vulnerable package as well as dependent packages, but does not update the major version of a package. If the pull request is acceptable, you can manually approve and merge it. Each time the pipeline is run, a new pull request is generated, so be sure to review and merge as they are opened to avoid duplications.

Auto-remediation is available for the both Enterprise GitHub and GitLab repositories and can modify the files specified in the following table.

| Ecosystem | Modifiable files |
|-----|-----|
| Nodejs | `package.json` and `package-lock.json` |
| Gradle | `build.gradle`, `gradle.lockfile`, and `buildscript-gradle.lockfile` |
| Maven  | `pom.xml` |
{: caption="Table 1. Supported ecosystems" caption-side="bottom"}

To start automatically remediating vulnerabilities, set the following optional environment variables for your pipeline.

| Variable | Description | Default settings |
|-----|-----|-----|
| `opt-in-cra-auto-remediation` | Enables auto-remediation for your pipeline.  | `false`  \n  To start remediating, set this variable to `true`. |
| `opt-in-cra-auto-remediation-force` | Forces a major package update as part of the pull request that is opened. | `false`  \n To force a major package update, set this variable to `true`. |
| `opt-in-cra-auto-remediation-enabled-repos` | Specifies specific repos where you want to enable auto-remediation. | Enabled for all repositories that are returned when `list_repos` is called. To specify specific repos, provide a comma-separated list of repo names. |
{: caption="Table 2. Supported ecosystems" caption-side="bottom"}

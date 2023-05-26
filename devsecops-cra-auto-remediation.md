---

copyright:
  years: 2023, 2023
lastupdated: "2023-05-26"

keywords: DevSecOps, IBM Cloud, compliance, cra

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Configuring Code Risk Analyzer to auto remediate vulnerabilities
{: #cd-devsecops-auto-remediate}



The Continuous Compliance (CC) pipeline uses the Code Risk Analyzer (CRA) to find the vulnerabilities in the `compliance-check` step. The CRA CLI can also remediate some of the vulnerabilities it identifies, this feature is called auto remediation. Auto remediation is turned off by default. You can turn on auto remediation by setting the `opt-in-cra-auto-remediation` pipeline environment variable to `true`.
{: shortdesc} 

This feature is available in the CC pipeline only.
{: note}

When the auto remediation feature turned on, the CRA CLI attempts to update a vulnerable package to a nonvulnerable version. The top level and dependent packages are updated. The CRA CLI updates the minor and patch versions but does not update the major version of the package by default. You can force the CRA CLI to update the major version of the package by setting the pipeline environment variable `opt-in-cra-auto-remediation-force` to true. The `opt-in-cra-auto-remediation-force` variable is set to false by default.

Auto remediation creates a pull request with all the fixes against the default branch of the repo. If the pull request is acceptable, you can manually review, approve, and merge it.

The auto remediation runs for each source repository in your pipeline that is returned by the `list_repos` method. To selectively enable a specific repo or repos for auto remediation, add a third pipeline environment variable that is named `opt-in-cra-auto-remediation-enabled-repos`. You must provide a comma-separated list of repo names in this variable.

Each CC pipeline run with auto remediation enabled creates a new pull request or updates an existing one, if fixes are available. The CRA CLI scans the commit that is deployed to production so unless the fixes that are identified and fixed by CRA CLI are approved, merged, and deployed to production, a new and potentially identical PR is generated at each CC pipeline run.


The following repositories are supported:

* Enterprise GitHub
* GitLab 

| Ecosystem| Files modified by CRA auto remediation |
|-----|-----|
| Nodejs | `package.json` and `package-lock.json` |
| Gradle | `build.gradle`, `gradle.lockfile`, and `buildscript-gradle.lockfile` |
| Maven  | `pom.xml` |
{: caption="Table 1. Supported ecosystems" caption-side="bottom"}

| CC pipeline environment variables | Description | Required or optional |
|----|----|----|
| `opt-in-cra-auto-remediation` | True or false. Set to false by default. | Required |
| `opt-in-cra-auto-remediation-force` | True or false. Set to false by default. | Optional |
| `opt-in-cra-auto-remediation-enabled-repos` | Comma-separated list of repository names. Not set by default. | Optional |
{: caption="Table 2. CC pipeline environment variables" caption-side="bottom"}

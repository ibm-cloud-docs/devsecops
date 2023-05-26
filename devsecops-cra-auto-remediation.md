---

copyright:
  years: 2022
lastupdated: "2023-05-23"

keywords: DevSecOps, IBM Cloud, compliance, Auto Remediation

subcollection: devsecops

---

# Configuring CRA to auto remediate vulnerabilities
{: #cd-devsecops-auto-remediate}

This feature is currently available in CC pipeline only.

The CC pipeline uses CRA CLI to find the vulnerabilities in the compliance-check step. The CRA CLI can also be used to remediate some or all of the vulnerabilities it identifies. This feature is called auto remediation. Auto remediation is turned off by default. It can be turned on by setting the `opt-in-cra-auto-remediation` pipeline environment variable to true.

With the auto remediation feature turned on, the CRA CLI attempts to update a vulnerable package to a non-vulnerable version. The top level and dependent packages could get updated. The CRA CLI only updates the minor and patch versions and does not update the major version of the package by default. The user can force the CRA CLI to update the major version of the package by setting another pipeline environment variable `opt-in-cra-auto-remediation-force` to true. The `opt-in-cra-auto-remediation-force` variable is set to false by default.

The auto remediation creates a pull request with all the fixes against the default branch of the repo. The user has to manually review / approve and merge the pull request, if the pull request is acceptable.

The auto remediation runs for each source repository in your pipeline that is returned by the `list_repos` method. In order to selectively enable a specific repo or repos for auto remediation, add a third pipeline environment variable named `opt-in-cra-auto-remediation-enabled-repos`. The user should provide a comma separated list of repo names in this variable.

Other point to note is that each run of CC pipeline, with auto remediation turned on, will create a new pull request or update an existing one, if there are fixes available. CRA CLI scans the commit that has been deployed to production so unless the fixes identified and fixed by CRA CLI are approved / merged and deployed to production. A new and potentially identical PR will keep getting generated at each CC pipeline run.


| Repository type supported|
|-----|
| Enterprise github|
| Gitlab |

| Ecosystems supported| Files modified by the CRA auto remediation feature|
|-----|-----|
| Nodejs | package.json and package-lock.json |
| Gradle | build.gradle, gradle.lockfile and buildscript-gradle.lockfile |
| Maven  | pom.xml |

| CC pipeline environment variables | comment | required? |
|----|----|----|
| opt-in-cra-auto-remediation | true / false, set to false by default | required |
| opt-in-cra-auto-remediation-force | true / false, set to false by default | optional |
| opt-in-cra-auto-remediation-enabled-repos | comma separated list of repository names, not set by default | optional |
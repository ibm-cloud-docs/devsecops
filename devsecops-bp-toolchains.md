---

copyright:
  years: 2025, 2026
lastupdated: "2026-10-26"

keywords: DevSecOps, ibm cloud, best practices, ci toolchain, continuous integration, continuous deployment, continuous compliance, infrastructure as code, terraform, IaC

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Best practices for DevSecOps toolchains
{: #practices-toolchains}

The following sections describe the best practices for each DevSecOps toolchain: continuous integration (CI), CI for infrastructure as code (IaC), continuous deployment (CD), and continuous compliance (CC).
{: shortdesc}

## Best practices for the continuous integration (CI) toolchain
{: #practices-ci-toolchain}

The DevSecOps CI toolchain follows these practices:

- Runs `detect-secrets` on the application repositories to prevent secret leaks in the application source code.
- Runs unit tests and application tests on the application code.
- Runs static code scanners on the application repositories to identify security vulnerabilities in the source code.
- Runs compliance checks by using compliance scanners like Code Risk Analyzer on all application repositories, including vulnerability scanning and a Bill of Materials (BOM) check on all package dependencies.
- Collects compliance data on peer reviews for all pull requests merged since the last build.
- Builds a container image on every Git commit, setting a tag based on build number, timestamp, and commit ID for traceability.
- Signs the built container image.
- Scans the container image for security vulnerabilities.
- Deploys the built artifact to a dev environment and runs dynamic scanning (ZAP API and UI scans) against the running application to detect runtime vulnerabilities.
- Runs acceptance and integration tests on the deployed artifact in the dev environment.
- Collects all evidence (scan results, test results, logs, peer review data, and pipeline run data) into an evidence locker at the end of every pipeline run.

## Best practices for the continuous integration (CI) for infrastructure as code (IaC) toolchain
{: #practices-ci-toolchain-iac}

The DevSecOps CI for IaC toolchain follows these practices:

- Runs `detect-secrets` on the IaC repositories to prevent secret leaks in the Terraform configuration.
- Runs unit tests on the IaC configuration on every Git commit.
- Runs a static code scan on the IaC repositories, including Terraform-specific tools such as `tflint` for linting and best-practice enforcement, and `terraform fmt` and `terraform validate` for formatting consistency and configuration validity.
- Runs compliance checks on the IaC repositories by using IaC-specific tools (like Code Risk Analyzer or `checkov`) to scan for compliance issues.
- Builds a Tar file containing the Terraform configuration on every Git commit.
- Signs the built Tar artifact.
- Deploys the IaC configuration to a dev environment by using IBM Cloud Schematics or the Terraform CLI.
- Runs acceptance and integration tests on the deployed configuration in the dev environment.
- Adds the built artifacts to the inventory repository for traceability and promotion to downstream environments.
- Collects all evidence (scan results, test results, signing data, logs, and pipeline run data) into an evidence locker at the end of every pipeline run.

## Best practices for the continuous deployment (CD) toolchain to deploy a secure application
{: #practices-cd-sec-app}

To deploy a secure application, the DevSecOps CD toolchain follows these practices:

- Uses a promotion pull request against the inventory repository to define the deployment scope and calculate the deployment delta, which is the set of artifacts that changed since the last concluded deployment.
- Verifies that all pull requests in the current deployment scope are peer-reviewed and approved before proceeding. Halts the deployment if any pull request is unapproved.
- Validates that every artifact scheduled for deployment is correctly signed. Blocks the deployment if a valid signature is missing.
- Calculates the deployment Bill of Materials (BOM) from the deployment delta and collects an evidence summary from both the associated CI pipeline run and the current CD pipeline run.
- Creates a change request in the configured change management system and attaches the deployment BOM, evidence summary, and peer review compliance data.
- Automatically approves the change request if all compliance criteria are met. Supports manual approval or emergency tagging when criteria are not fully met.
- Deploys the artifacts from the inventory to the target environment (such as staging or production) once the change request is approved or tagged as emergency.
- Runs acceptance and integration tests after deployment to validate that the deployment succeeded and the application is working as expected.
- Automatically rolls back the deployment within the same pipeline run if a failure occurs during the deployment or acceptance-test stage, preserving the last known good state in the inventory.
- Closes the change request with a `close_category` of `Successful` or `Successful with issues`, and moves the `<target-environment>_latest` inventory tag to the deployed commit.
- Collects and uploads all log files, artifacts, and evidence to the evidence locker at the end of every pipeline run.
- Optionally labels merged pull requests with `deploy:{region}:{env}` after a successful deployment, to provide end-to-end deployment traceability without requiring access to pipeline details.

## Best practices for the continuous compliance (CC) toolchain
{: #practices-cc-toolchain}

The DevSecOps CC toolchain follows these practices:

- Runs `detect-secrets` on all inventory source repositories to identify secret leaks in the application source code.
- Runs a static code scan (SonarQube or custom implementation) on all source repositories found in the inventory.
- Runs a dynamic application security testing scan (OWASP ZAP) on the deployed application to find runtime vulnerabilities.
- Runs compliance checks by using tools (like Code Risk Analyzer) on all inventory source repositories, including vulnerability scanning, CIS checks on Kubernetes deployment manifests, BOM checks on all package dependencies, and branch protection verification.
- Scans all deployed container images for security vulnerabilities.
- Creates or updates incident issues for every individual CVE, vulnerability, or alert found during the pipeline run and sets a due date on each issue.
- Collects evidence for each stage into the evidence locker, attaches scan and check results to the corresponding issues, and updates the compliance status of all scanned artifacts.

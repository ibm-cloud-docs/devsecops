---

copyright:
  years: 2026, 2026
lastupdated: "2026-11-03"

keywords: DevSecOps, cheat sheet, best practices, quick reference, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# DevSecOps cheat sheet
{: #devsecops-cheat-sheet}

Use this cheat sheet as a quick reference for the key recommendations when you adopt {{site.data.keyword.contdelivery_short}} DevSecOps. Each item has a short statement and a link to the corresponding documentation.
{: shortdesc}

## Get started
{: #cheat-sheet-get-started}

| Recommendation | What to do | Learn more |
|----------------|------------|------------|
| Use supported pipeline versions | Use the `v10` or `v11` pipeline version. The `v9` version is no longer supported. | [Select a specific version branch or tag](/docs/devsecops?topic=devsecops-devsecops-select-version), [Improving compliance pipeline performance](/docs/devsecops?topic=devsecops-devsecops-conc) |
| Adopt the complete DevSecOps spectrum | Set up continuous integration (CI), continuous deployment (CD), and continuous compliance (CC) together, rather than a subset. | [CI pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-ci-pipeline), [CD pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-cd-pipeline-overview), [CC pipeline](/docs/devsecops?topic=devsecops-devsecops-cc-pipeline) |
| Follow the toolchain best practices | Start from the practices that each reference toolchain (CI, CI for IaC, CD, CC) already implements. | [Best practices for DevSecOps toolchains](/docs/devsecops?topic=devsecops-practices-toolchains) |
| Onboard your own application | Bring your application to DevSecOps with the inferred pipeline configuration instead of writing the configuration from scratch. | [Bringing your own app to DevSecOps](/docs/devsecops?topic=devsecops-cd-devsecops-apps-byoa), [Creating a pipeline with Inferred DevSecOps configuration](/docs/devsecops?topic=devsecops-devsecops-inferred-pipeline-configuration) |
| Use a PR pipeline | Run the PR pipeline on every pull request to catch issues before merge. | [Pull request pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-pr-pipeline) |
{: caption="Get started" caption-side="top"}

## Evidence and traceability
{: #cheat-sheet-evidence}

| Recommendation | What to do | Learn more |
|----------------|------------|------------|
| Reuse evidence | Opt in to evidence reuse so that PR and CI pipelines skip scans and tests when the content did not change. | [Reuse of evidence across PR and CI pipelines](/docs/devsecops?topic=devsecops-devsecops-evidence-reuse) |
| Use {{site.data.keyword.cos_short}} as the evidence locker | Store evidence only in {{site.data.keyword.cos_full_notm}} buckets. | [Using {{site.data.keyword.cos_full_notm}} buckets as an evidence locker](/docs/devsecops?topic=devsecops-cd-devsecops-cos-bucket-evidence), [Configuring {{site.data.keyword.cos_full_notm}} for storing evidence](/docs/devsecops?topic=devsecops-cd-devsecops-cos-config) |
| Collect evidence in the PR pipeline | Set `collect-evidence-in-pr` to collect evidence and manage issues for pull requests. | [Enable evidence collection in PR pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-pr-pipeline#cd-devsecops-pr-evidence-collection) |
| Gate deployments on evidence | Know why a deployment is blocked, and configure evidence checks for your deployment topology. | [Validating evidence](/docs/devsecops?topic=devsecops-evidence-checks-validation) |
| Keep peer review on | Keep `peer-review-compliance` set to `1` so that merged pull requests are checked for peer review. | [Peer review compliance](/docs/devsecops?topic=devsecops-cd-devsecops-peer-review) |
| Enable PR traceability | Set the `deployment-traceability` flag so that merged PRs are labeled with the environment they were deployed to. | [Enabling deployment traceability](/docs/devsecops?topic=devsecops-cd-devsecops-cd-pipeline-overview#enable-traceability) |
| Automate change management | Let the CD pipeline create, approve, and close change requests with the deployment evidence attached. | [Automated change management](/docs/devsecops?topic=devsecops-cd-devsecops-change-management) |
| Generate a software bill of materials | Produce SBOMs for each built microservice and keep them with the evidence. | [Software Bill Of Materials](/docs/devsecops?topic=devsecops-devsecops-sbom) |
{: caption="Evidence and traceability" caption-side="top"}

## Security and compliance
{: #cheat-sheet-security}

| Recommendation | What to do | Learn more |
|----------------|------------|------------|
| Manage issues | Set the `incident-assignees` and `incident-labels` properties in the CI and CC pipelines so that incident issues have owners and labels. | [Assigning incident issues to users](/docs/devsecops?topic=devsecops-assign-incident-issues), [Labeling incident issues](/docs/devsecops?topic=devsecops-incident-issues#label-incident-issues), [Pipeline parameters](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm) |
| Track due dates on issues | Use due dates and custom grace periods on incident issues, and extend them only through the documented process. | [Managing due dates for incident issues](/docs/devsecops?topic=devsecops-incident-issues#incident-issue-due-date-setting), [Extending the due date of an incident issue](/docs/devsecops?topic=devsecops-incident-issues#due-date-postpone) |
| Close fixed issues automatically | Keep `opt-in-auto-close` at its default of `1` so that issues close when the vulnerability is no longer detected. | [Use case 4: automatically closing fixed issues](/docs/devsecops?topic=devsecops-incident-issues#incident-issue-uc4) |
| Auto-remediate security issues | Turn on auto-remediation in the CC pipeline (`opt-in-cra-auto-remediation`) so that fix PRs are opened for vulnerable packages. | [Automatically remediating vulnerabilities](/docs/devsecops?topic=devsecops-cd-devsecops-cra-scans#cd-devsecops-auto-remediate) |
| Pick your scanners | Review the supported scanning tools and configure the ones that fit your stack. | [Supported scanning tools](/docs/devsecops?topic=devsecops-cd-devsecops-supported-scanning-tools), [Configuring detect-secrets scans](/docs/devsecops?topic=devsecops-cd-devsecops-detect-secrets-scans) |
| Sign your artifacts | Configure signing properties so that the CI pipeline signs images, and sign non-image artifacts too. The CD pipeline blocks unsigned artifacts. | [Generating a GPG key](/docs/devsecops?topic=devsecops-devsecops-image-signing), [Signing non-image artifacts](/docs/devsecops?topic=devsecops-devsecops-artifacts-verify) |
| Vault your secrets | Keep secrets in a vault and link them to the toolchain, rather than pasting values. | [Managing secrets in your toolchains](/docs/devsecops?topic=devsecops-cd-devsecops-toolchains-secrets-new), [Using secrets integrations in your toolchains](/docs/devsecops?topic=devsecops-cd-devsecops-toolchains-secrets) |
| Apply least-privilege access | Grant the Pipeline Administrator and Pipeline Runner roles only the IAM access that each role needs. | [Managing IAM access for cloud resources](/docs/devsecops?topic=devsecops-iam-permissions) |
{: caption="Security and compliance" caption-side="top"}

## Customization and operations
{: #cheat-sheet-customization}

| Recommendation | What to do | Learn more |
|----------------|------------|------------|
| Customize processes in a controlled way | Use `one-pipeline-config-repo` for your pipeline configuration and custom scripts. Reuse the common script tools that are provided in the compliance base image. | [Working with default and custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-advanced-pipelines-customization#default-and-custom-scripts), [Understanding Commons Scripts in DevSecOps](/docs/devsecops?topic=devsecops-devsecops-common-scripts-revise) |
| Organize your repositories | Plan your application, shared configuration, inventory, and issues repositories before you create toolchains. | [DevSecOps repositories considerations](/docs/devsecops?topic=devsecops-cd-devsecops-advanced-pipelines-customization#repo-considerations-devsecops) |
| Choose a deployment model | Decide between push-based and pull-based deployment for your clusters. | [Deployment models](/docs/devsecops?topic=devsecops-deployment-model) |
| Manage the inventory | Promote inventory changes through branches and partition the inventory for the CC pipeline when you have many artifacts. | [Understanding inventory for DevSecOps](/docs/devsecops?topic=devsecops-cd-devsecops-inventory), [Partitioning inventory for CC pipeline](/docs/devsecops?topic=devsecops-partition-inventory-cc-pipeline) |
| Develop pipeline changes in development mode | Test `.pipeline-config.yaml` changes with the simplified pipeline. Development mode skips compliance tasks and collects no evidence, so switch back for real runs. | [Running pipelines in development mode](/docs/devsecops?topic=devsecops-devsecops-devmode) |
| Run several apps from one toolchain | Configure the CI and PR pipelines for multiple application repositories. | [Configuring multiple apps on one CI toolchain](/docs/devsecops?topic=devsecops-cd-devsecops-multiple-apps) |
| Get notified | Add {{site.data.keyword.en_short}} to the toolchain and set `event-notifications` to `1`. | [{{site.data.keyword.en_short}} for DevSecOps pipelines](/docs/devsecops?topic=devsecops-cd-devsecops-configure-en) |
| Troubleshoot with debug mode | Turn on `pipeline-debug` to get more detail when a run fails. | [Pipeline parameters](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm#pipeline-parm-pipeline-debug), [Troubleshooting](/docs/devsecops?topic=devsecops-troubleshoot-devsecops), [FAQ](/docs/devsecops?topic=devsecops-faq_devsecops) |
{: caption="Customization and operations" caption-side="top"}

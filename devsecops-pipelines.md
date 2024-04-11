---

copyright:
  years: 2021, 2024
lastupdated: "2024-03-25"

keywords: DevSecOps

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Understanding DevSecOps pipelines
{: #cd-devsecops-pipelines}

The various pipelines that are provided in the reference continuous integration and continuous deployment toolchains are based on the {{site.data.keyword.contdelivery_short}} support for Tekton Pipelines. To learn more about Tekton Pipelines, see [Working with Tekton pipelines](/docs/ContinuousDelivery?topic=ContinuousDelivery-tekton-pipelines).
{: shortdesc}

You don't need to be a Tekton expert to use the reference pipelines. Reference pipelines are predefined with a basic structure that includes placeholders for custom scripts for steps such as builds, automated tests, and deployment. Users can declare their custom scripts for their own pipelines and set values for various environment properties for a specific pipeline.
{: note}

## Pipeline status types
{: #cd-devsecops-pipelines-statuses}

It's important to understand the error or failure conditions for a reference pipeline at certain points. Conceptually, two different types of status result from a task run in a compliance pipeline:

* **Compliance status**: The pass or fail state of a `some` check or a set of checks.
* **Pipeline status**: The success or failure state of a task execution itself.

If a test, scan, or check fails, it does not cause the pipeline itself to fail or stop; the task that is running the test is marked as green.
{: tip}

From a compliance point of view, the result of the unit test does not impact the deployment. You can deploy artifacts with failed checks, but the process keeps evidence about that activity. The compliance flow does not block you from releasing a fix when an outage occurs. For example, a green run for a unit test task that found failing tests means that `The task ran successfully and found the following issues`.

If a task fails with a red status, it occurs because the pipeline cannot, or should not, continue. Example conditions for failure include:

* Errors in a task or in the pipeline.
* Something happened that means it makes no sense to continue running the pipeline.

For example, if an artifact build fails in the continuous integration, it breaks the purpose of the continuous integration process itself.

To keep the final pipeline status in sync with the compliance results, a task at the end of the pipeline checks the compliance results and sets the pipeline run status to either `red` or `green`.

## Pull request pipeline
{: #cd-devsecops-pipelines-pr-pipeline}

The pull request pipeline runs pre-set compliance status checks on a pull request for the specified application (app) repository (repo). These status checks might prevent you from merging the pull request into the default active branch, usually `master`, if the checks are unsuccessful. Open or update a pull request against the default active branch to trigger the pull request pipeline run. Users can run their own setup for the pipeline and tests in custom stages. For more information about the pull request pipeline, see [Pull request pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-pr-pipeline).

## Continuous integration pipeline
{: #cd-devsecops-pipelines-ci-pipeline}

The continuous integration pipeline builds the deployable artifacts from the application (app) repositories (repos). Before it builds artifacts, the pipeline checks that the code is scanned and tested, in the same way that pull requests are processed. Built artifacts are also scanned for vulnerabilities and signed in the pipeline before they are marked ready for release and deployment in the [inventory](/docs/devsecops?topic=devsecops-cd-devsecops-inventory). Unlike the pull request pipeline, the continuous integration pipeline collects evidence and result artifacts on each stage of the build, such as testing, scanning, and signing. This data correlates to the built artifacts and can be tracked through the deployment process and change management. For more information about the continuous integration pipeline, see [Continuous Integration pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-ci-pipeline).

## Continuous deployment pipeline
{: #cd-devsecops-pipelines-cd-pipeline}

The continuous deployment pipeline generates all of the evidence and change request summary content. The pipeline deploys the build artifacts to a specific environment, such as staging or prod, and then collects, creates, and uploads all existing log files, evidence, and artifacts to the evidence locker. For more information about the continuous deployment pipeline, see [Continuous deployment pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-cd-pipeline).

## Continuous compliance pipeline
{: #cd-devsecops-pipelines-cc-pipeline}

The continuous compliance pipeline periodically scans the deployed artifacts and their source repositories for newer vulnerabilities since the artifacts were deployed in production. The pipeline also helps to track deviations with due date automatically and provides application awareness into [{{site.data.keyword.compliance_long}}](https://www.ibm.com/cloud/security-and-compliance-center){: external}.
For more information, see [Continuous compliance pipeline](/docs/devsecops?topic=devsecops-devsecops-cc-pipeline).


## Integration with {{site.data.keyword.compliance_short}}
{: #cd-devsecops-pipelines-scc}

With {{site.data.keyword.compliance_short}}, you can automate the evaluation of your security and compliance to detect vulnerabilities. By enabling the Security and Compliance Center integration by using DevSecOps templates, you can store evidence and monitor your deployments for compliance. For more information, see [Using the {{site.data.keyword.compliance_full}} with DevSecOps toolchains](/docs/devsecops?topic=devsecops-cd-devsecops-scc-toolchains).


## Inventory workflow
{: #cd-devsecops-pipelines-inventory-workflow}

The inventory contains several branches, including the default branch. These branches are used as source or target environments, like deployment stages, environments, or regions. For more information, see [Inventory](/docs/devsecops?topic=devsecops-cd-devsecops-inventory).


## Evidence
{: #cd-devsecops-pipelines-evidence}

Collecting evidence is an essential aspect of the DevSecOps reference architecture because compliance evidence creates the audit trail that auditors look for during a compliance audit. One of the goals of DevSecOps is automated evidence generation and storage in auditable evidence lockers. For more information, see [Evidence](/docs/devsecops?topic=devsecops-devsecops-evidence).

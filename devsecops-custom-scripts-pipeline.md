---

copyright:
  years: 2024
lastupdated: "2024-05-24"

keywords: DevSecOps, inventory model, inventory, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Understanding pipelines stages
{: #cd-devsecops-scripts-stages}

The following lists explain the stages in pull request (PR), Continuous Integration (CI), Continuous Deployment (CD), and Continuous Compliance (CC) pipelines.

## Pull request (PR) pipeline stages
{: #cd-devsecops-pr-pipeline-stages}

The PR pipeline runs compliance checks on a pull request for a chosen application repository and acts as a safeguard for merging into the main branch. The PR pipeline is triggered by opening or updating a pull request against a chosen branch.

For more information, see [Pull Request (PR) pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-pr-pipeline) documentation.

|PR pipeline stage        | Description |
|-------------------------|-------------|
| `code-pr-start`         | Clones the GitHub repository that is associated with the pull request |
| `code-setup`            | Set up your build and test environment to prepare the rest of the pipeline stages |
| `code-detect-secrets`   | Runs detect secrets scan on application code |
| `code-unit-tests`       | Executes a unit test for the code that is associated with the PR |
|`code-compliance-checks` | Code Risk Analyzer (CRA) or Mend Unified Agent scans for vulnerability detection on open source dependencies, and any other compliance checks on application repositories |
| `code-pr-finish`        |Consolidate pipeline status. If any of the compliance checks previously executed in the pipeline fail, this stage fails. This is the "fail-safe" behavior to ensure that compliance issues are remediated before a PR can be merged or a build can be deployed. |
{: caption="Pull request (PR) pipeline stages" caption-side="bottom"}
{: #pr-pipeline}

## Continuous Integration (CI) pipeline stages
{: #cd-devsecops-ci-pipeline-stages}

The CI pipeline builds deployable artifacts from application repositories. It scans, tests, and signs built artifacts while also collecting evidence on each stage to be released in the inventory and tracked through the deployment and change management.

For more information, see [Continuous Integration (CI) pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-ci-pipeline) documentation.

| CI pipeline stage         | Description |
|---------------------------|-------------|
| `code-pr-start`           | Set up the pipeline environment, including cloning the configuration and application repositories to the pipeline local file system |
| `code-setup`              | Set up your build and test environment to prepare the rest of the pipeline stages |
| `code-unit-tests`         | Executes unit tests and application tests on application code |
| `code-peer-review`        | Enforces the [Peer Review](/docs/devsecops?topic=devsecops-cd-devsecops-peer-review) of code changes before they are merged and promoted to production|
| `code-static-scan`        | Runs static scan on application code (the default is SonarQube but other tools can be used) |
| `code-compliance-checks`  | Runs [Code Risk Analyzer (CRA)](/docs/devsecops?topic=devsecops-cd-devsecops-cra-scans) scan  for vulnerability detection on open source dependencies, and any other compliance checks on application repositories |
| `build-artifact`          | Build the artifacts and images from the source code repository |
| `build-sign-artifact`     | Signs built artifacts and images |
| `build-scan-artifact`     | Scan the container image with [Vulnerability Advisor](/docs/devsecops?topic=devsecops-cd-devsecops-va-scans) |
| `deploy-dev`              | Deploys the built artifacts to the dev environment |
| `code-dynamic-scan`       | Runs the [OWASP-Zap API and UI dynamic scans](/docs/devsecops?topic=devsecops-cd-devsecops-zap-scans) on the application that is deployed to a dev environment |
| `deploy-acceptance-tests` | Runs acceptance and integration tests on the deployed built artifacts on the dev environment |
| `deploy-release`          | Add the built artifacts to the inventory that is used by the Continuous Delivery (CD) pipeline |
| `code-ci-finish`          | Collect, create, and upload the logs files, artifacts, and evidence to the evidence locker. If any of the compliance checks previously executed in the pipeline fail, this stage fails. This is the "fail-safe" behavior to ensure that compliance issues are remediated before a PR can be merged or a build can be deployed. |
{: caption="Continuous Integration (CI) pipeline" caption-side="bottom"}
{: #ci-pipeline}

## Continuous Deployment (CD) pipeline stages
{: #cd-devsecops-cd-pipeline-stages}

The CD pipeline compiles all the content for evidence and the change request summary. It deploys the build to an environment, such as staging or production, and uploads all of the evidence to the evidence locker.

For more information, see [Continuous Deployment (CD) pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-cd-pipeline) documentation.

| CD pipeline stage       | Description |
|-------------------------|-------------|
| `prod-start`            | Calculates the [deployment delta](/docs/devsecops?topic=devsecops-cd-devsecops-promote-branches) (services that are updated by the CD deployment) |
| `prod-setup`            | Set up the target environment to prepare the rest of the CD pipeline stages |
| `prod-verify-artifact`  | [Verifies signatures](/docs/devsecops?topic=devsecops-devsecops-image-verify) of images and artifacts that were built in the CI pipeline |
| `prod-change-request`   | Creation, approval of the [Change Request](/docs/devsecops?topic=devsecops-cd-devsecops-automate-changemgmt) that is associated to this deployment, and switch to implement state |
| `prod-deployment`       | Deploys the inventory delta to the target environment |
| `prod-acceptance-tests` | Runs acceptance and integration tests on the deployed built artifacts on the target environment |
| `prod-finish`           | Collects all evidence to the evidence locker, and close change request |
{: caption="Continuous Deployment (CD) pipeline" caption-side="bottom"}
{: #cd-pipeline}

### Continuous Compliance (CC) pipeline stages
{: #cd-devsecops-cc-pipeline-stages}

The CC pipeline performs periodic scans on deployed artifacts and their source repositories. It scans the latest deployed state in the inventory and creates, updates, or auto-closes incident issues in the incident repository.

For more information, see [Continuous Compliance (CC) pipeline](/docs/devsecops?topic=devsecops-devsecops-cc-pipeline) documentation.

| CC pipeline stage      | Description |
|------------------------|-------------|
| `cc-start`             | Clone repository, process inventory based on tag, and register repos and artifacts|
| `cc-setup`             | Set up the CC pipeline environment for the scans in later stages. This might include installing build dependencies or performing Maven builds|
| `cc-detect-secrets`    | Runs detect secrets scan on application code|
| `cc-static-scan`       | Runs static scan on application code (the default is SonarQube but other tools can be used)|
| `cc-dynamic-scan`      | Runs the [OWASP-Zap API and UI dynamic scans](/docs/devsecops?topic=devsecops-cd-devsecops-zap-scans) on the application that is deployed to the target environment|
| `cc-compliance-checks` | Runs [Code Risk Analyzer (CRA)](/docs/devsecops?topic=devsecops-cd-devsecops-cra-scans) scan  for vulnerability detection on open source dependencies, and any other compliance checks on application repositories|
| `cc-scan-artifact`     | Scan the container image with [Vulnerability Advisor](/docs/devsecops?topic=devsecops-cd-devsecops-va-scans)|
| `cc-finish`            | Collect, create, and upload the logs files, artifacts, and evidence to the evidence locker. If any of the compliance checks previously executed in the CC pipeline fails, this stage fails.|
{: caption="Continuous Compliance (CC) pipeline" caption-side="bottom"}
{: #cc-pipeline}

### Pipeline compliance checks
{: #cd-devsecops-pipeline-compliance-checks}

The following scans and checks are performed during the code-compliance-checks task within the PR, CI, and CC pipelines.

| Tool                                       | Scan or Check |
|--------------------------------------------|---------------|
| Detect secrets                             | Identifies visible secrets|
| Code Risk Analyzer (CRA)                   | Finds vulnerabilities for package dependencies, container base images, and OS packages|
| Code Risk Analyzer CIS                     | Checks configuration on Kubernetes deployment manifests|
| Code Risk Analyzer Bill of Materials (BOM) | Lists all dependencies included in packages and images|
| Mend Unified Agent (UA)                    | Scans open source components for vulnerabilities|
| Repository compliance                      | Checks settings of branch protection rules|
{: caption="Pipeline compliance checks" caption-side="bottom"}
{: #cc-pipeline}


### Stage descriptions
{: #cd-devsecops-stage-desc}

* `setup`: Run scripts to complete setup tasks that are done outside the pipeline run context. These tasks persist for the entire pipeline run. For example, setting up an external environment, setting up branch protection rules, performing health checks, checking environments, or initializing external services.
* `test`: Run unit tests, include all of your tests that run on your codebase, and pre-deploy time.
* `static-scan`: Run a static code analyzer tool.
* `peer-review`: Run scripts to check whether the pull request is reviewed and has the minimum number of required approvals.
* `dynamic-scan`: Run dynamic scan on the application.
* `containerize`: Create the artifacts that are required by your app, such as Docker images, RPMs, and Java Archive (JARS) files.
* `scan-artifact`: Scan the artifacts, for example, by using Container Registry Vulnerability Advisor for Docker images.
* `sign-artifact`: Sign the artifacts.
* `release`: Add artifacts to the inventory by marking them available for deployment.
* `deploy`: Deploy artifacts to an environment such as test and dev or staging and prod.
* `acceptance-test`: Run tests on deployed artifacts. You can also include your post-deployment tests in this stage.
* `verify-artifact`: Run scripts to check whether the artifacts that are signed in the CI pipeline have valid signatures.
* `finish`: This stage is a customizable stage and is always executed, irrespective of pipeline status.

---

copyright:
  years: 2022, 2024
lastupdated: "2024-03-28"

keywords: DevSecOps, IBM Cloud, maximum retry time, scans

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Pipeline parameters
{: #cd-devsecops-pipeline-parm}

Parameters are provided for the pull request, continuous integration, continuous deployment, continuous compliance, and promotion pipelines.
{: shortdesc}

You can add parameters to the pipelines on the pipeline UI and access them from the [custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-stages#cd-devsecops-pipelines-custom-customize).
{: tip}

Tables 1 to 5 list and describe the pull request, continuous integration, continuous deployment, continuous compliance, and promotion parameters for pipelines.

|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|`artifactory-dockerconfigjson` 		|SECRET 		|The base64-encoded Docker `config.json` file that stores credential information for artifactory.	 			|Optional			|
| `base-branch` |text | The target branch where the PR is merged. Typically, `master` is the default base branch. If a PR Git trigger is configured, which is typically the case, this parameter is populated from the trigger.| Required  |
| `base-repo` |text | The URL of the repo where the PR is merged. If a PR Git trigger is configured, which is typically the case, this parameter is populated from the PR trigger.| Required  |
| `base-repo-name` |text | The name of the repo where the PR is merged. If a PR Git trigger is configured, which is typically the case, this parameter is populated from the PR trigger.| Required  |
| `base-repo-owner` |text | The owner of the repo where the PR is merged. If a PR Git trigger is configured, which is typically the case, this parameter is populated from the PR trigger.| Required  |
|`baseimage-auth-email`		|text 		|The email credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.		|Optional			|
|`baseimage-auth-host`		|text		|The host credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.	|Optional			|
|`baseimage-auth-password`		|SECRET		|The password credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan. |Optional			|
|`baseimage-auth-user`		|text		|The user credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.			|Optional			|
| `cocoa-config-retry-status-code` | text   | The response status code for an API call that needs to be retried in the cocoa CLI. For example: `500,404`.  Default value: `500` | Optional |
| `cocoa-config-max-retry-attempts`| text   | The number of times that the cocoa CLI can retry the API call. For example: `5`. Default value: `3` | Optional |
| `cocoa-config-git-default-branch (supported in Q4, 2022)` | text   | The default Git branch for the cocoa CLI. For example: `main`. Default value: `master`   | Optional |
|`cra-custom-script-path`  | text   | Path to a custom script to be run before CRA scanning. This script is sourced to provide the option to set ENV variables in the context of the CRA BOM tool. | Optional |
|`cra-cveignore-path`  |text   |File path to the `cveignore`, relative to the application repository root. The default file path is `.cra/.cveignore` if value is not provided.   | Optional    |
|`cra-docker-build-context`     |text   |If this flag is specified, Code Risk Analyzer uses the directory in the path parameter as the Docker build context. The default value is `false`. |Optional |
|`cra-docker-buildflags`  |text  |Customize docker build command for build stage scanning. The parameter is empty by default.    |Optional    |
|`cra-dockerfile-pattern`             | text   | Ability to add Dockerfile with a different naming convention, such as `enterprise-linux.Dockerfile` | Optional |
|`cra-exclude-devdependencies`		| text		| Specifies whether to exclude dev dependencies from scanning (`true` or `false`). The default value is `false`.	| Optional			|
|`cra-gradle-exclude-configs`     | text   | Specifies which Gradle configurations to exclude dependencies in scanning. Example: `runtimeClasspath,testCompileClasspath`. The parameter is empty by default.   | Optional   |
|`cra-maven-exclude-scopes`       | text   | Specifies which maven scopes to exclude dependencies in scanning. Example: `test,compile`. The parameter is empty by default.  | Optional   |
|`cra-nodejs-create-package-lock`		| text		| Enable Code Risk Analyzer discovery to build the `package-lock.json` file for node.js repos. This parameter is set to false by default.	| Optional			|
|`cra-python-create-requirements-txt`		|text		|Deprecated. The new CRA tools don't use this parameter anymore. Enable Code Risk Analyzer discovery to build the `requirements.txt` file for Python repos. This parameter is set to false by default.	|Optional			|
|[`detect-secrets-baseline-filename`](#detect-secrets-baseline-filename) | text | The name of the baseline file in your app repository. | Optional |
|[`detect-secrets-exclusion-list`](#detect-secrets-exclusion-list) | text | A regex list of files to be excluded in the detect-secrets scan. | Optional |
|[`detect-secrets-image`](#detect-secrets-image) | text | Specifies an alternative detect-secrets image, including custom images or specific versions of the official image. | Optional |
|[`detect-secrets-verbose`](#detect-secrets-verbose) | text | Outputs the name of the file that is currently being scanned. | Optional |
|[`dind-image`](#dind-image)		|text		|Base image to run sidecars.	|Optional			|
|`git-token`		|SECRET		|The Git repo access token.	|Optional			|
|`github-token`		|SECRET		|The GitHub repo access token.	|Optional			|
|`grit-token`		|SECRET		|The {{site.data.keyword.gitrepos}} access token.	|Optional			|
| `head-branch` |text | The source branch where the PR is raised. If a PR Git trigger is configured, which is typically the case, this parameter is populated from the PR trigger.| Required  |
| `head-repo` |text | The URL of the repo where the PR is raised. If a PR Git trigger is configured, which is typically the case, this parameter is populated from the PR trigger.| Required  |
| `head-sha` |text | The HEAD commit on the `head-branch`. If a PR Git trigger is configured, which is typically the case, this parameter is populated from the PR trigger.| Required  |
|`iam_retry_count`		|text		|The number of retries to wait for fetching the IAM token.	|Optional			|
|`iam_retry_sleep`		|text		|The amount of wait time for fetching the IAM token.	|Optional			|
|`ibmcloud-api-key`		|SECRET		|The {{site.data.keyword.cloud}} API key that interacts with the `ibmcloud` CLI tool.	|Required			|
|`one-pipeline-dockerconfigjson`		|SECRET		|The base64-encoded Docker `config.json` file that pulls images from a private registry.	|Optional			|
|`pipeline-config`		|text		|The configuration file that customizes pipeline behavior.	|Optional			|
|`pipeline-config-branch`		|text		|The branch of the DevSecOps pipeline configuration.	|Optional			|
|`pipeline-config-repo`		|text		|The repo URL of the DevSecOps pipeline configuration location.	|Optional			|
|`pipeline-dockerconfigjson`		|SECRET		|The base64-encoded Docker `config.json` file that pulls images from a private registry.	|Optional			|
|[`pipeline-debug`](#pipeline-parm-pipeline-debug)		|select		|The pipeline debug mode switch.  |Optional			|
|`slack-notifications`		|text		|The switch that turns the Slack integration on or off.	|Optional			|	|[slack-notifications](#`pipeline-parm-slack-notifications`)		|text		|The switch that turns the Slack integration on or off |Optional		|
{: caption="Table 1. Pull request parameters" caption-side="bottom"}
{: #cd-pr-parameters}
{: tab-title="Pull request parameters"}
{: tab-group="IAM-simple"}
{: class="simple-tab-table"}


|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|`app-name` 		|text 		|The name of your application that is specified in the toolchain settings.			|Required			|
|`artifactory-dockerconfigjson`		|SECRET		|The base64-encoded Docker `config.json` file that stores credential information for artifactory.			|Optional			|
|`baseimage-auth-email`		|text		|The credentials for the application Dockerfile base image that is required for the Code Risk Analyzer scan.	|Optional			|
|`baseimage-auth-host`		|text		|The credentials for the application Dockerfile base image that is required for the Code Risk Analyzer scan. |Optional			|
|`baseimage-auth-password`		|SECRET		|The credentials for the application Dockerfile base image that is required for the Code Risk Analyzer scan.	|Optional			|
|`baseimage-auth-user`		|text		|The user credential for the base image of the application Dockerfile, required for the Code Risk Analyzer scan.			|Optional			|
|`batched-evidence-collection`|text|Set this flag to enable evidence collection in batch mode, which minimizes the network calls. Default `1`|Optional|
|[`branch`](#pipeline-parm-branch)		|text		|The Git repo branch of the repository that contains the source code of your application. Default value:	`master` |Optional			|
| `ciso-ibmcloud-api-key` | SECRET | Overrides `ibmcloud-api-key` for image signing and signature verification. | Optional |
| `cocoa-config-retry-status-code` | text   | The response status code for an API call that needs to be retried in the cocoa CLI. For example: `500,404`.  Default value: `500` | Optional |
| `cocoa-config-max-retry-attempts`| text   | The number of times that the cocoa CLI can retry the API call. For example: `5`. Default value: `3` | Optional |
| `cocoa-config-git-default-branch (supported in Q4, 2022)` | text   | The default Git branch for the cocoa CLI. For example: `main`. Default value: `master`   | Optional |
|[`cluster-name`](#pipeline-parm-cluster-name)		|text		|The name of the Docker build cluster.	|Required			|
|[`compliance-baseimage`](#pipeline-parm-compliance-baseimage)	|text		|The base image for running the built-in pipeline code.	|Optional
|`cos-api-key`		| SECRET		| The Cloud Object Storage API key.	| Optional			|
|`cos-bucket-name`		| text		| The name of the bucket in your Cloud Object Storage instance that is used as an evidence locker.	|Optional			|
|`cos-endpoint`		| text		| The endpoint that stores the evidence in the Cloud Object Storage instance that is used as an evidence locker. For more information, see [Endpoint Types](/docs/cloud-object-storage?topic=cloud-object-storage-endpoints#advanced-endpoint-types). | Optional			|
|`cr-ibmcloud-api-key`		|SECRET		| Overrides `ibmcloud-api-key` if provided, for pulling the image from Container Registry for the Sysdig scan.	|Optional			|
|`cr-resource-group`		|text		| The resource group that is used to access the Container Registry. |Optional			|
| `cra-custom-script-path`  | text   | Path to a custom script to be run before CRA scanning. This script is sourced to provide the option to set ENV variables in the context of the CRA BOM tool. | Optional |
|`cra-cveignore-path`     |text   |File path to the `cveignore`, relative to the application repository root. The default file path is `.cra/.cveignore` if value is not provided.   |Optional    |
|`cra-docker-build-context`     |text   |If this flag is specified, Code Risk Analyzer uses the directory in the path parameter as the Docker build context. The default value is `false`. |Optional |
|`cra-docker-buildflags`   |text   |Customize docker build command for build stage scanning. The parameter is empty by default.    |Optional    |
|`cra-dockerfile-pattern`             | text   | Ability to add Dockerfile with a different naming convention, such as `enterprise-linux.Dockerfile` | Optional |
|`cra-exclude-devdependencies`		| text		| Specifies whether to exclude dev dependencies from scanning (`true` or `false`). The default value is `false`.	| Optional			|
|`cra-gradle-exclude-configs`     | text   | Specifies which Gradle configurations to exclude dependencies in scanning. Example: `runtimeClasspath,testCompileClasspath`. The parameter is empty by default.   | Optional   |
| `cra-ibmcloud-api-key` | SECRET | Overwrites the `ibmcloud-api-key` that is used by the CRA tasks. | Optional |
|`cra-maven-exclude-scopes`       | text   | Specifies which maven scopes to exclude dependencies in scanning. Example: `test,compile`. The parameter is empty by default.  | Optional   |
|`cra-nodejs-create-package-lock`		| text		| Enable Code Risk Analyzer discovery to build the `package-lock.json` file for node.js repos. This parameter is set to false by default.	| Optional			|
|`cra-python-create-requirements-txt`		|text		|Deprecated. The new CRA tools don't use this parameter anymore. Enable Code Risk Analyzer discovery to build the `requirements.txt` file for Python repos. This parameter is set to false by default.	|Optional			|
|[`custom-exempt-label`](#pipeline-parm-custom-exempt-label)  |text   |Defines the custom label with which the incident issue is marked as exempted.	|Optional			|
|`custom-image-tag`		|text		|The custom tag for the image in a comma-separated list.	|Optional			|
|[`detect-secrets-baseline-filename`](#detect-secrets-baseline-filename) | text | The name of the baseline file in your app repository. | Optional |
|[`detect-secrets-exclusion-list`](#detect-secrets-exclusion-list) | text | A regex list of files to be excluded in the detect-secrets scan. | Optional |
|[`detect-secrets-image`](#detect-secrets-image) | text | Specifies an alternative detect-secrets image, including custom images or specific versions of the official image. | Optional |
|[`detect-secrets-verbose`](#detect-secrets-verbose) | text | Outputs the name of the file that is currently being scanned. | Optional |
|`dev-cluster-namespace`		|text		|The Kubernetes cluster namespace where the Docker engine is hosted and deployed.	|Required			|
|`dev-region`		|text		|The {{site.data.keyword.cloud_notm}} region that hosts the cluster.	|Required			|
|`dev-resource-group`		|text		|The cluster resource group.	|Required			|
|`doi-buildnumber`		|text		|The build number value to use as the `--buildnumber` flag in `ibmcloud doi` commands.	|Optional			|
|`doi-environment`		|text		|The {{site.data.keyword.DRA_short}} target environment.	|Optional			|
|`doi-ibmcloud-api-key`  |SECRET  | The {{site.data.keyword.cloud_notm}} API key that interacts with the DevOps Insights instance where DOI records are uploaded. |Optional |
|`doi-logicalappname`		|text		|The app name to use as the `--logicalappname` flag in `ibmcloud doi` commands	|Optional			|
|`doi-tags`		|text		|Comma-separated custom tags.	|Optional			|
|`doi-toolchain-id`		|text		|The {{site.data.keyword.DRA_short}} instance toolchain ID.	|Optional			|
|`evidence-repo`		|tool integration		|The evidence repo URL.	|Optional			|
|`git-token`		|SECRET		|The Git repo access token.	|Optional			|
|`github-token`		|SECRET		|The GitHub repo access token.	|Optional			|
|`gosec-additional-flags` | text | Extra flags to be appended to the start of the `gosec` command. |Optional			|
|`gosec-private-repository-url`   |text   | (deprecated) Your private repository base URL. For example, `https://github.ibm.com`. |Optional |
| `gosec-private-repository-host`    | text   | Your private repository host. For example, `github.ibm.com` | Optional |
|`gosec-private-repository-ssh-key`     |secret   |The SSH key for the private repository. |Optional |
| `gosec-private-repository-user` | text | User for the private repository with https access (default to `x-oauth-basic`) | Optional |
| `gosec-private-repository-token` | secret | The token that is used for https access to a private repository (default to the token configured for the first Git integration in the toolchain whose repository URL is hosted on `gosec-private-repository-host`'s value) | Optional |
|`gosec-proxy-virtual-repository-token`     |secret   |The virtual repository token for `gosec` proxy. |Optional |
|`gosec-proxy-virtual-repository-user`     |text   |The virtual repository user for `gosec` proxy. | Optional |
|`gosec-proxy-virtual-repository-url`     |text   |The virtual repository url for `gosec` proxy. |Optional |
|`gosec-scan-image`     |text   |Specifies an alternative `gosec` image, including custom images or specific versions of the official image. |Optional |
|`grit-token`		|SECRET		|The {{site.data.keyword.gitrepos}} access token.	|Optional			|
|`iam_retry_count`		|text		|The number of retries to wait for fetching the IAM token.	|Optional			|
|`iam_retry_sleep`		|text		|The amount of wait time for fetching the IAM token.	|Optional			|
|`ibmcloud-api-key`		|SECRET		|The {{site.data.keyword.cloud_notm}} API key that interacts with the `ibmcloud` CLI tool.	|Required			|
|`ibmcloud-enable-session-keep-alive` | select | Keep the IBM Cloud CLI session alive for long-running jobs in Code Risk Analyzer scan, if this value is set to 1. |Optional			|
|`icr-dockerconfigjson`		|SECRET		|The base64-encoded Docker `config.json` file that stores credential information for IBM Container Registry. This parameter is used by SLSA attestation.			|Optional			|
|`incident-assignee`		|text		|The assignee for the incident issues (GitHub or GitLab username). |Optional			|
|`incident-assignees`		|text		|The assignees for the incident issues (one or more usernames that are separated by a comma). This parameter can be used only with GitHub and GitLab Premium accounts. |Optional			|
|`incident-label`		|text		|The label for new incident issues.	|Optional			|
|`incident-labels`		|text		|The labels for new incident issues. `incident-labels` can be one or more labels that are separated by a comma.	|Optional			|
|`incident-repo`  	|tool integration		|The incident issues the repo URL.	|Optional			|
|`inventory-repo`		|tool integration		|The inventory repo URL.	|Optional			|
|[`dind-image`](#dind-image)		|text		|Base image to run sidecars.	|Optional			|
|`opt-in-gosec`		|text		|Enables the `gosec` static scan.	|Optional			|
|`one-pipeline-dockerconfigjson`		|SECRET		|The base64-encoded Docker `config.json` file that pulls images from a private registry.	|Optional			|
|`opt-in-dynamic-api-scan`		|text		|To enable the OWASP Zap API scan. 	|Optional			|
|`opt-in-dynamic-scan`		|text		|To enable the OWASP Zap scan.	 |Optional			|
|`opt-in-dynamic-ui-scan`		|text		|To enable the OWASP Zap UI scan.	 |Optional			|
|`opt-in-pr-collection`		|text		|Add any value to enable PR collection.	|Optional			|
|`opt-in-sonar`		|text		|Sonarqube scan integration.	|Optional			|
|`opt-in-sonar-hotspots`		|text		| Allows hotspot detection in Sonarqube scans.	|Optional			|
|`opt-out-ci-set-commit-status` 		|text		| If this parameter has any value, the commit status value of the source code repository is not set by the CI pipeline according to the CI pipeline execution's result (success or failure).	|Optional			|
|`opt-out-nonvulnerability-issue-collection`		|text		|Add any value to opt out of nonvulnerability related issue collection.	|Optional			|
|`peer-review-compliance`		|text		|Set the `peer-review-compliance` environment variable to 1 in your CI and CD Pipeline to activate the peer-review check. Similarly, in the CD pipeline, this configuration runs peer review validation for all pull requests linked to your current deployment. |Optional			|
|`peer-review-collection`		|text		|To retrieve a list of pull requests and their associated titles for your ongoing deployment, Set this value to 1	|Optional			|
|`pipeline-config`		|text		|The configuration file that customizes pipeline behavior.	|Required			|
|`pipeline-config-branch`		|text		|The branch of the DevSecOps pipeline configuration.	|Optional			|
|`pipeline-config-repo`		|text		|The repo URL of the DevSecOps pipeline configuration location.	|Optional	|
|[`pipeline-debug`](#pipeline-parm-pipeline-debug)		|select		|The pipeline debug mode switch.  |Optional			|
|`registry-namespace`		|text		|The Container Registry namespace for the image.	|Required			|
|`registry-region`		|text		|The {{site.data.keyword.cloud_notm}} region for the image registry.	|Required			|
| `repository` |text |The URL of your application's source code repository. | Optional |
|`repository-integration`		|text		|The integration name for the repo.	|Optional			|
|`sbom-validation-collect-evidence`   |text   | Enable evidence collection for sbom validation scan for cyclonedx sbom. If this value is set to 1, then the sbom validation collects evidence.  |Optional     |
|[`signing-key`](#pipeline-parm-signing-key)		|SECRET		|The base64-encoded private key for signing images by using `.pfx` file data.	|Required			|
|`skip-inventory-update-on-failure`	|text		|The switch that stops inventory update on a failed CI run.  |Optional			|
|[`slack-notifications`](#pipeline-parm-slack-notifications)		|text		|The switch that turns the Slack integration on or off |Optional		|
|`slsa-attestation`	|select		|The switch that enables SLSA attestations.  |Optional			|
| `slsa-attestation-public-key` | SECRET | The base64-encoded public key to verify SLSA attestation reports. | Optional |
|[`sonarqube`](#pipeline-parm-sonarqube)		|tool integration		|The Sonarqube tool integration.	|Optional			|
|[`sonarqube_standby`](#pipeline-parm-sonarqube)		|tool integration		|The standby Sonarqube tool integration that can be used as a fallback.	|Optional			|
|`sonarqube-config`		|text		|Runs a SonarQube scan in an isolated Docker-in-Docker container (default configuration) or in an existing development Kubernetes cluster (cluster configuration). Alternatively, you can bring your own SonarQube instance and configure the SonarQube tool integration (custom configuration). Options: `default`, `cluster`, or `custom`. Default is `default`. For more information, see ([Adding SonarQube to the continuous integration pipeline](/docs/devsecops?topic=devsecops-sonarqube#sonarqube-ci-pipeline)). | Required |
|[`sonarqube-namespace`](#pipeline-parm-sonarqube-namespace) 	|text		|The configured Kubernetes namespace to use with the SonarQube instance created by the pipeline.	|Optional			|
|`sonarqube-instance-image` 	|text		| The sonarqube instance image that is used to run as an isolated Docker-in-Docker container.	|Optional			|
|`sonarqube-scanner-image` 	|text		| The sonarqube scanner CLI image that is used for the default sonarqube scan command.	|Optional			|
|[`sonarqube-scan-command`](#pipeline-parm-sonarqube-scan-command) 	|text		|The sonarqube scan command that starts the sonarqube scan. `sonarqube-scan-command` must be set for Maven Java projects. For more information, see [`sonarqube-scan-command`](#pipeline-parm-sonarqube-scan-command) |Optional			|
|[`static-scan-retry-count`](#pipeline-parm-static-scan-retry-count)		|text		|The number of retries to wait for the Sonarqube instance to be established.	|Optional			|
|[`static-scan-retry-sleep`](#pipeline-parm-static-scan-retry-sleep)		|text		|The amount of wait time per retry iteration.	|Optional			|
|`subpipeline-webhook-token` | SECRET| The webhook secret of the `Subpipeline Webhook Trigger` for [triggering async pipelines](/docs/devsecops?topic=devsecops-devsecops-async-sub-pipelines). See also [Updating the async stage webhooks](/docs/devsecops?topic=devsecops-update-async-webhook). |Optional |
|`sysdig-api-token`		|text		|Sysdig API token value. The token is visible from the Sysdig instance's User Profile page. This value is needed for running the Sysdig scan.	|Required			|
|`sysdig-inline-scanner-image`		|text		|Sysdig inline scanner image to be used for the scan. The default value is `quay.io/sysdig/secure-inline-scan:2`	|Optional			|
|[`sysdig-scan`](#pipeline-param-sysdig-scan)	|select		|Enable Sysdig scan for images. If this value is set to 1, then Sysdig scan is enabled. 	|Required			|
|`sysdig-url`		|text		|The URL of the Sysdig instance to be used for the scan. The default value is `https://us-south.security-compliance-secure.cloud.ibm.com`	|Optional			|
|`va-scan-retry-count`		|text		|The number of retries to wait for the vulnerability report scan.	|Required			|
|`va-scan-retry-sleep`	|text		|The number of wait times per retry iteration.	|Optional			|
|`version`		|text		|The version of the app to deploy.	|Required			|
{: caption="Table 2. Continuous integration parameters" caption-side="bottom"}
{: #cd-ci-parameters}
{: tab-title="Continuous integration parameters"}
{: tab-group="IAM-simple"}
{: class="simple-tab-table"}

|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|`artifact-token`		|SECRET		|The token where artifacts are stored	|Required if artifact repo is in a different source provider.			|
|`artifactory-dockerconfigjson`		|SECRET		|The base64-encoded Docker `config.json` file that stores credential information for artifactory.			|Optional			|
|[`assignee`](#pipeline-parm-assignee)		|text		|The assignee of the change request.			|Optional			|
|[`backout-plan`](#pipeline-parm-backout-plan)	|text		|Plan of how the change will be rolled back if there is a failure.			|Optional			|
|`batched-evidence-collection`|text|Set this flag to enable evidence collection in batch mode, which minimizes the network calls. Default `1`|Optional|
| `ciso-ibmcloud-api-key` | SECRET | Overrides `ibmcloud-api-key` for image signing and signature verification. | Optional |
| `cocoa-config-retry-status-code` | text   | The response status code for an API call that needs to be retried in the cocoa CLI. For example: `500,404`.  Default value: `500` | Optional |
| `cocoa-config-max-retry-attempts`| text   | The number of times that the cocoa CLI can retry the API call. For example: `5`. Default value: `3` | Optional |
| `cocoa-config-git-default-branch (supported in Q4, 2022)` | text   | The default Git branch for the cocoa CLI. For example: `main`. Default value: `master`   | Optional |
|[`change-request-duration`](#pipeline-parm-change-request-duration)    | text   | The planned end time of the change. | Optional |
|`change-management-repo`  | text   | The URL of the change management repository.  | Optional |
|[`change-request-id`](#pipeline-parm-change-request-id)    |text   |The ID of an open change request. If this parameter is set to `notAvailable` by default, a change request is automatically created by the continuous deployment pipeline. |Optional			|
|[`cluster`](#pipeline-parm-cluster)	|text 		|The name of the Docker build cluster.		|Required			|
|[`cluster-region`](#pipeline-parm-cluster-region)		|text		|The {{site.data.keyword.cloud_notm}} region that hosts the cluster.	|Required			|
|[`compliance-baseimage`](#pipeline-parm-compliance-baseimage)	|text		|The base image for running the built-in pipeline code. |Optional			|
|`cos-api-key`		| SECRET		| The Cloud Object Storage API key.	| Optional			|
|`cos-bucket-name`		| text		| The name of the bucket in your Cloud Object Storage instance that is used as an evidence locker.	|Optional		|
|`cos-endpoint`		| text		| The endpoint that stores the evidence in the Cloud Object Storage instance that is used as an evidence locker. For more information, see [Endpoint Types](/docs/cloud-object-storage?topic=cloud-object-storage-endpoints#advanced-endpoint-types). | Optional			|
|`cr-pipeline-name`		|text		|The name of the pipeline that is creating the change request.	|Optional			|
|`cr-pipeline-version`		|text		|The version of the pipeline that is creating the change request.	|Optional			|
| `cra-ibmcloud-api-key` | SECRET | Overwrites the `ibmcloud-api-key` that is used by the CRA tasks. | Optional |
|[`customer-impact`](#pipeline-parm-customer-impact)		|text		|The impact of the change on the customer.	|Optional			|
|[`deployment-impact`](#pipeline-parm-deployment-impact)		|text		|The impact of the change on the deployment.	|Optional			|
|[`description`](#pipeline-parm-description)		|text		|The description of the change that gets appended to the Change Request Description.	|Optional			|
|[`dind-image`](#dind-image)		|text		|Base image to run sidecars.	|Optional			|
|`doi-buildnumber`		|text		|The build number value to use as the `--buildnumber` flag in `ibmcloud doi` commands.	|Optional			|
|`doi-environment`		|text		|The {{site.data.keyword.DRA_short}} target environment.	|Optional			|
|`doi-ibmcloud-api-key`  |SECRET  | The {{site.data.keyword.cloud_notm}} API key that interacts with the DevOps Insights instance where DOI records are uploaded. |Optional |
|`doi-logicalappname`		|text		|The app name to use as the `--logicalappname` flag in `ibmcloud doi` commands	|Optional			|
|`doi-tags`		|text		|Comma-separated custom tags.	|Optional			|
|`doi-toolchain-id`		|text		|The {{site.data.keyword.DRA_short}} instance toolchain ID.	|Optional			|
|`emergency-label`		|text		|Identifies the pull request as an emergency.	|Optional			|
|`evidence-repo`		|tool integration		|The evidence repo URL.	|Optional			|
|`force-redeploy`    |text   |Forces the deployment or redeployment of the app even if the last deployment does not contain a delta in the inventory. Set this parameter to `true` to force a deployment of the app as if it is the first deployment on the specified target environment. By default, this parameter is set to `false`. |Optional |
|`git-token`		|SECRET		|The Git repo access token.	|Optional			|
|`github-token`		|SECRET		|The GitHub repo access token.	|Optional			|
|`grit-token`		|SECRET		|The {{site.data.keyword.gitrepos}} access token.	|Optional			|
|`iam_retry_count`		|text		|The number of retries to wait for fetching the IAM token.	|Optional			|
|`iam_retry_sleep`		|text		|The amount of wait time for fetching the IAM token.	|Optional			|
|`ibmcloud-api-key`		|SECRET		|The {{site.data.keyword.cloud_notm}} API key that interacts with the `ibmcloud` CLI tool.	|Required			|
|[`impact`](#pipeline-parm-impact)		|text		|Extra notes on what this change implementation impacts.	|Optional			|
|`incident-repo`  	|tool integration		|The incident issues the repo URL.	|Optional			|
|`inventory-repo`		|tool integration		|The inventory repo URL.	|Optional			|
|`incident-assignee`		|text		|The assignee for the incident issues (GitHub or GitLab username). |Optional			|
|`incident-assignees`		|text		|One or more assignees for the incident issues (one or more usernames that are separated by a comma). This parameter can be used only with GitHub and GitLab Premium accounts. |Optional			|
|`incident-label`		|text		|The label for new incident issues.	|Optional			|
|`incident-labels`		|text		|The labels for new incident issues. `incident-labels` can be one or more labels that are separated by a comma.	|Optional			|
|`opt-in-pr-collection`		|text		|Add any value to enable PR collection.	|Optional			|
|`opt-out-nonvulnerability-issue-collection`		|text		|Add any value to opt out of nonvulnerability related issue collection.	|Optional			|
|`pipeline-config`		|text		|The configuration file that customizes pipeline behavior.	|Required			|
|`pipeline-config-branch`		|text		|The branch of the DevSecOps pipeline configuration.	|Optional			|
|`pipeline-config-repo`		|text		|The repo URL of the DevSecOps pipeline configuration location.	|Optional			|
|[`pipeline-debug`](#pipeline-parm-pipeline-debug)		|select		|The pipeline debug mode switch.	|Optional			|
|`pipeline-dockerconfigjson`		|SECRET		|The base64-encoded Docker `config.json` file that pulls images from a private registry.	|Optional	  	|<staging>  
|`pnp-ibmcloud-api-key` | secret | Overwrites the `ibmcloud-api-key` that is used by the ServiceNow V3 api services. | Required	  	|

### sysdig-scan
{: #pipeline-param-sysdig-scan}

This parameter is for the pipelines that use scan artifact stages to run the Sysdig scan by using `sysdig inline scanner` on `icr.io` images.

---

copyright: 
  years: 2022, 2023
lastupdated: "2023-09-20"

keywords: DevSecOps, IBM Cloud, maximum retry time, scans

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Pipeline parameters
{: #cd-devsecops-pipeline-parm}

Parameters are provided for the pull request, continuous integration, continuous deployment, continuous compliance, and promotion pipelines.
{: shortdesc}

You can add parameters to the pipelines on the pipeline UI and access them from the [custom scripts](/docs/devsecops?topic=devsecops-custom-scripts).
{: tip}

Tables 1 to 5 list and describe the pull request, continuous integration, continuous deployment, continuous compliance, and promotion parameters for pipelines.

|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|`artifactory-dockerconfigjson` 		|SECRET 		|The base64-encoded Docker `config.json` file that stores credential information for artifactory.	 			|Required			|
| `base-branch` |text | The target branch where the PR is merged. Typically, `master` is the default base branch. If a PR Git trigger is configured, which is typically the case, this parameter is populated from the trigger.| Required  |
| `base-repo` |text | The URL of the repo where the PR is merged. If a PR Git trigger is configured, which is typically the case, this parameter is populated from the PR trigger.| Required  |
| `base-repo-name` |text | The name of the repo where the PR is merged. If a PR Git trigger is configured, which is typically the case, this parameter is populated from the PR trigger.| Required  |
| `base-repo-owner` |text | The owner of the repo where the PR is merged. If a PR Git trigger is configured, which is typically the case, this parameter is populated from the PR trigger.| Required  |
|`baseimage-auth-email`		|text 		|The email credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.		|Optional			|
|`baseimage-auth-host`		|text		|The host credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.	|Optional			|
|`baseimage-auth-password`		|SECRET		|The password credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan. |Optional			|
|`baseimage-auth-user`		|text		|The user credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.			|Optional			|
| `cocoa-config-retry-status-code` | text   | The response status code for an API call that needs to be retried in the cocoa CLI. For example: `500,404`.  Default value: `500` | Optional |
| `cocoa-config-max-retry-attempts`| text   | The number of times that the cocoa CLI can retry the API call before giving up. For example: `5`. Default value: `3` | Optional |
| `cocoa-config-git-default-branch (supported in Q4, 2022)` | text   | The default native Git branch for the cocoa CLI. For example: `main`. Default value: `master`   | Optional |
|`cra-custom-script-path`  | text   | Path to a custom script to be run before CRA scanning. This script is sourced to provide the option to set ENV variables in the context of the CRA BOM tool. | Optional |
|`cra-cveignore-path`  |text   |File path to the cveignore, relative to the application repository root. Default file path is `.cra/.cveignore` if value is not provided.   | Optional    |
|`cra-docker-build-context`     |text   |If this flag is specified, Code Risk Analyzer uses the directory in the path parameter as the Docker build context. The default value is `false`. |Optional |
|`cra-docker-buildflags`  |text  |Customize docker build command for build stage scanning. The parameter is empty by default.    |Optional    |
|`cra-dockerfile-pattern`             | text   | Ability to add Dockerfiles with a different naming convention, such as `enterprise-linux.Dockerfile` | Optional |
|`cra-exclude-devdependencies`		| text		| Specifies whether to exclude dev dependencies from scanning (`true` or `false`). The default value is `false`.	| Optional			|
|`cra-gradle-exclude-configs`     | text   | Specifies which Gradle configurations to exclude dependencies in scanning. Example: `runtimeClasspath,testCompileClasspath`. The parameter is empty by default.   | Optional   |
|`cra-maven-exclude-scopes`       | text   | Specifies which maven scopes to exclude dependencies in scanning. Example: `test,compile`. The parameter is empty by default.  | Optional   |
|`cra-nodejs-create-package-lock`		| text		| Enable Code Risk Analyzer discovery to build the `package-lock.json` file for node.js repos. This parameter is set to false by default.	| Optional			|
|`cra-python-create-requirements-txt`		|text		|Deprecated. The new CRA tools don't use this parameter anymore. Enable Code Risk Analyzer discovery to build the `requirements.txt` file for Python repos. This parameter is set to false by default.	|Optional			|
|[`detect-secrets-baseline-filename`](#detect-secrets-baseline-filename) | text | The name of the baseline file in your app repository. | Optional |
|[`detect-secrets-exclusion-list`](#detect-secrets-exclusion-list) | text | A regex list of files to be excluded in the detect-secrets scan. | Optional |
|[`detect-secrets-image`](#detect-secrets-image) | text | Specifies an alternative detect-secrets image, including custom images or specific versions of the official image. | Optional |
|[`detect-secrets-verbose`](#detect-secrets-verbose) | text | Outputs the name of the file that is currently being scanned. | Optional |
|`dind-image`		|text		|Base image to run sidecars.	|Optional			|
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
|`artifactory-dockerconfigjson`		|SECRET		|The base64-encoded Docker `config.json` file that stores credential information for artifactory.			|Required			|
|`baseimage-auth-email`		|text		|The credentials for the application Dockerfile base image that is required by the Code Risk Analyzer scan.	|Optional			|	
|`baseimage-auth-host`		|text		|The credentials for the application Dockerfile base image that is required by the Code Risk Analyzer scan. |Optional			|
|`baseimage-auth-password`		|SECRET		|The credentials for the application Dockerfile base image that is required by the Code Risk Analyzer scan.	|Optional			|
|`baseimage-auth-user`		|text		|The user credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.			|Optional			|
|`batched-evidence-collection`|text|Set this flag to enable evidence collection in batched mode, which minimizes the network calls. Default `0`|Optional|
|[`branch`](#pipeline-parm-branch)		|text		|The Git repo branch of the repository that contains the source code of your application. Default value:	`master` |Optional			|
| `ciso-ibmcloud-api-key` | SECRET | Overrides `ibmcloud-api-key` for image signing and signature verification. | Optional | 
| `cocoa-config-retry-status-code` | text   | The response status code for an API call that needs to be retried in the cocoa CLI. For example: `500,404`.  Default value: `500` | Optional |
| `cocoa-config-max-retry-attempts`| text   | The number of times that the cocoa CLI can retry the API call before giving up. For example: `5`. Default value: `3` | Optional |
| `cocoa-config-git-default-branch (supported in Q4, 2022)` | text   | The default native Git branch for the cocoa CLI. For example: `main`. Default value: `master`   | Optional |
|[`cluster-name`](#pipeline-parm-cluster-name)		|text		|The name of the Docker build cluster.	|Required			|
|[`compliance-baseimage`](#pipeline-parm-compliance-baseimage)	|text		|The base image for running the built-in pipeline code.	|Optional	
|`cos-api-key`		| SECRET		| The Cloud Object Storage API key.	| Optional			|
|`cos-bucket-name`		| text		| The name of the bucket in your Cloud Object Storage instance that is used as an evidence locker.	|Optional			|
|`cos-endpoint`		| text		| The endpoint that stores the evidence in the Cloud Object Storage instance that is used as an evidence locker. For more information, see [Endpoint Types](/docs/cloud-object-storage?topic=cloud-object-storage-endpoints#advanced-endpoint-types). | Optional			|
|`cr-ibmcloud-api-key`		|SECRET		| Overrides `ibmcloud-api-key` if provided, for pulling the image from container registry for the Sysdig scan.	|Optional			|
| `cra-custom-script-path`  | text   | Path to a custom script to be run before CRA scanning. This script is sourced to provide the option to set ENV variables in the context of the CRA BOM tool. | Optional |
|`cra-cveignore-path`     |text   |File path to the cveignore, relative to the application repository root. Default file path is `.cra/.cveignore` if value is not provided.   |Optional    |
|`cra-docker-build-context`     |text   |If this flag is specified, Code Risk Analyzer uses the directory in the path parameter as the Docker build context. The default value is `false`. |Optional |
|`cra-docker-buildflags`   |text   |Customize docker build command for build stage scanning. The parameter is empty by default.    |Optional    |
|`cra-dockerfile-pattern`             | text   | Ability to add Dockerfiles with a different naming convention, such as `enterprise-linux.Dockerfile` | Optional |
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
|`grit-token`		|SECRET		|The {{site.data.keyword.gitrepos}} access token.	|Optional			|
|`iam_retry_count`		|text		|The number of retries to wait for fetching the IAM token.	|Optional			|
|`iam_retry_sleep`		|text		|The amount of wait time for fetching the IAM token.	|Optional			|
|`ibmcloud-api-key`		|SECRET		|The {{site.data.keyword.cloud_notm}} API key that interacts with the `ibmcloud` CLI tool.	|Required			|
|`ibmcloud-enable-session-keep-alive` | select | Keep the IBMCloud CLI session alive for long running jobs in Code Risk Analyzer scan, if this value is set to 1. |Optional			|
|`incident-assignee`		|text		|The assignee for the incident issues (GitHub or GitLab username). |Optional			|
|`incident-assignees`		|text		|The assignees for the incident issues (one or more usernames that are separated by a comma). Note that this parameter can be used only with GitHub and GitLab Premium accounts. |Optional			|
|`incident-label`		|text		|The label for new incident issues.	|Optional			|
|`incident-labels`		|text		|The labels for new incident issues. `incident-labels` can be one or more labels that are separated by a comma.	|Optional			|
|`incident-repo`  	|tool integration		|The incident issues repo URL.	|Optional			|
|`inventory-repo`		|tool integration		|The inventory repo URL.	|Optional			|
|`dind-image`		|text		|Base image to run sidecars.	|Optional			|
|`one-pipeline-dockerconfigjson`		|SECRET		|The base64-encoded Docker `config.json` file that pulls images from a private registry.	|Optional			|
|`opt-in-dynamic-api-scan`		|text		|To enable the OWASP Zap API scan. 	|Optional			|
|`opt-in-dynamic-scan`		|text		|To enable the OWASP Zap scan.	 |Optional			|
|`opt-in-dynamic-ui-scan`		|text		|To enable the OWASP Zap UI scan.	 |Optional			|
|`opt-in-pr-collection`		|text		|Add any value to enable PR collection.	|Optional			|
|`opt-in-sonar`		|text		|The Sonarqube scan integration.	|Optional			|
|`opt-in-sonar-hotspots`		|text		|Allows hotspot detection in sonarqube scans.	|Optional			|
|`opt-out-ci-set-commit-status` 		|text		| If this parameter has any value, the commit status value of the source code repository is not set by the CI pipeline according to the CI pipeline execution's result (success or failure).	|Optional			|
|`opt-out-nonvulnerability-issue-collection`		|text		|Add any value to opt out of nonvulnerability related issue collection.	|Optional			|
|`pipeline-config`		|text		|The configuration file that customizes pipeline behavior.	|Required			|
|`pipeline-config-branch`		|text		|The branch of the DevSecOps pipeline configuration.	|Optional			|
|`pipeline-config-repo`		|text		|The repo URL of the DevSecOps pipeline configuration location.	|Optional	|
|[`pipeline-debug`](#pipeline-parm-pipeline-debug)		|select		|The pipeline debug mode switch.  |Optional			|
|`registry-namespace`		|text		|The container registry namespace for the image.	|Required			|
|`registry-region`		|text		|The {{site.data.keyword.cloud_notm}} region for the image registry.	|Required			|
| `repository` |text |The URL of your application's source code repository. | Optional |
|`repository-integration`		|text		|The integration name for the repo.	|Optional			|
|`sbom-validation-collect-evidence`   |text   | Enable evidence collection for sbom validation scan for cyclonedx sbom. If this value is set to 1, then the sbom validation collects evidence.  |Optional     |
|[`signing-key`](#pipeline-parm-signing-key)		|SECRET		|The base64-encoded private key for signing images by using `.pfx` file data.	|Required			|
|`skip-inventory-update-on-failure`	|text		|The switch that stops inventory update on a failed CI run.  |Optional			|
|[`slack-notifications`](#pipeline-parm-slack-notifications)		|text		|The switch that turns the Slack integration on or off |Optional		|
|[`sonarqube`](#pipeline-parm-sonarqube)		|tool integration		|The Sonarqube tool integration.	|Optional			|
|`sonarqube-config`		|text		|Runs a SonarQube scan in an isolated Docker-in-Docker container (default configuration) or in an existing development Kubernetes cluster (cluster configuration). Alternatively, you can bring your own SonarQube instance and configure the SonarQube tool integration (custom configuration). Options: `default`, `cluster`, or `custom`. Default is `default`. For more information, see ([Adding SonarQube to the continuous integration pipeline](/docs/devsecops?topic=devsecops-sonarqube#sonarqube-ci-pipeline)). | Required |
|[`sonarqube-namespace`](#pipeline-parm-sonarqube-namespace) 	|text		|The configured Kubernetes namespace to use with the SonarQube instance that is created by the pipeline.	|Optional			|
|[`sonarqube-scan-command`](#pipeline-parm-sonarqube-scan-command) 	|text		|The sonarqube scan command that starts the sonarqube scan. `sonarqube-scan-command` must be set for Maven Java projects. For more information, see [`sonarqube-scan-command`](#pipeline-parm-sonarqube-scan-command) |Optional			|
|[`static-scan-retry-count`](#pipeline-parm-static-scan-retry-count)		|text		|The number of retries to wait for the Sonarqube instance to be established.	|Optional			|
|[`static-scan-retry-sleep`](#pipeline-parm-static-scan-retry-sleep)		|text		|The amount of wait time per retry iteration.	|Optional			|
|`subpipeline-webhook-token` | SECRET| The webhook secret of the `Subpipeline Webhook Trigger` for [triggering async pipelines](/docs/devsecops?topic=devsecops-devsecops-async-sub-pipelines). See also [Updating the async stage webhooks](/docs/devsecops?topic=devsecops-update-async-webhook). |Optional |
|`sysdig-api-token`		|text		|Sysdig API token value. The token is visible from the Sysdig instance's User Profile page. This value is needed for running the Sysdig scan.	|Required			|
|`sysdig-url`		|text		|The URL of the Sysdig instance to be used for the scan. Default value is `https://us-south.security-compliance-secure.cloud.ibm.com`	|Optional			|
|`sysdig-inline-scanner-image`		|text		|Sysdig inline scanner image to be used for the scan. Default value is `quay.io/sysdig/secure-inline-scan:2`	|Optional			|
|`va-scan-retry-count`		|text		|The number of retries to wait for the vulnerability report scan.	|Required			|
|`va-scan-retry-sleep`	|text		|The number of wait times per retry iteration.	|Optional			|
|`version`		|text		|The version of the app to deploy.	|Required			|
|[`sysdig-scan`](#pipeline-param-sysdig-scan)	|select		|Enable Sysdig scan for images. If this value is set to 1, then Sysdig scan is enabled. 	|Required			|
{: caption="Table 2. Continuous integration parameters" caption-side="bottom"}
{: #cd-ci-parameters}
{: tab-title="Continuous integration parameters"}
{: tab-group="IAM-simple"}
{: class="simple-tab-table"}

|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|`artifact-token`		|SECRET		|The token where artifacts are stored	|Required if artifact repo is in different source provider.			|
|`artifactory-dockerconfigjson`		|SECRET		|The base64-encoded Docker `config.json` file that stores credential information for artifactory.			|Required			|
|[`assignee`](#pipeline-parm-assignee)		|text		|The assignee of the change request.			|Optional			|
|[`backout-plan`](#pipeline-parm-backout-plan)	|text		|Plan of how the change will be rolled back in case of a failure.			|Optional			|
|`batched-evidence-collection`|text|Set this flag to enable evidence collection in batched mode, which minimizes the network calls. Default `0`|Optional|
| `ciso-ibmcloud-api-key` | SECRET | Overrides `ibmcloud-api-key` for image signing and signature verification. | Optional |
| `cocoa-config-retry-status-code` | text   | The response status code for an API call that needs to be retried in the cocoa CLI. For example: `500,404`.  Default value: `500` | Optional |
| `cocoa-config-max-retry-attempts`| text   | The number of times that the cocoa CLI can retry the API call before giving up. For example: `5`. Default value: `3` | Optional |
| `cocoa-config-git-default-branch (supported in Q4, 2022)` | text   | The default native Git branch for the cocoa CLI. For example: `main`. Default value: `master`   | Optional |
|[`change-request-duration`](#pipeline-parm-change-request-duration)    | text   | The planned end time of the change. | Optional |
|`change-management-repo`  | text   | The URL of the change management repository.  | Optional |
|[`change-request-id`](#pipeline-parm-change-request-id)    |text   |The ID of an open change request. If this parameter is set to `notAvailable` by default, a change request is automatically created by the continuous deployment pipeline. |Optional			|
|[`cluster`](#pipeline-parm-cluster)	|text 		|The name of the Docker build cluster.		|Required			|
|[`cluster-region`](#pipeline-parm-cluster-region)		|text		|The {{site.data.keyword.cloud_notm}} region that hosts the cluster.	|Required			|
|[`compliance-baseimage`](#pipeline-parm-compliance-baseimage)	|text		|The baseimage for running the built-in pipeline code. |Optional			|
|`cos-api-key`		| SECRET		| The Cloud Object Storage API key.	| Optional			|
|`cos-bucket-name`		| text		| The name of the bucket in your Cloud Object Storage instance that is used as an evidence locker.	|Optional		|
|`cos-endpoint`		| text		| The endpoint that stores the evidence in the Cloud Object Storage instance that is used as an evidence locker. For more information, see [Endpoint Types](/docs/cloud-object-storage?topic=cloud-object-storage-endpoints#advanced-endpoint-types). | Optional			|
| `cra-ibmcloud-api-key` | SECRET | Overwrites the `ibmcloud-api-key` that is used by the CRA tasks. | Optional |
|[`customer-impact`](#pipeline-parm-customer-impact)		|text		|The impact of the change on the customer.	|Optional			|
|[`deployment-impact`](#pipeline-parm-deployment-impact)		|text		|The impact of the change on the deployment.	|Optional			|
|[`description`](#pipeline-parm-description)		|text		|The description of the change that will be appended to the Change Request Description.	|Optional			|
|`dind-image`		|text		|Base image to run sidecars.	|Optional			|
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
|[`impact`](#pipeline-parm-impact)		|text		|Additional notes on what this change implementation impacts.	|Optional			|
|`incident-repo`  	|tool integration		|The incident issues repo URL.	|Optional			|
|`inventory-repo`		|tool integration		|The inventory repo URL.	|Optional			|
|`incident-assignee`		|text		|The assignee for the incident issues (GitHub or GitLab username). |Optional			|
|`incident-assignees`		|text		|The one or more assignees for the incident issues (one or more usernames that are separated by a comma). Note that this parameter can be used only with GitHub and GitLab Premium accounts. |Optional			|
|`incident-label`		|text		|The label for new incident issues.	|Optional			|
|`incident-labels`		|text		|The labels for new incident issues. `incident-labels` can be one or more labels that are separated by a comma.	|Optional			|
|`opt-in-pr-collection`		|text		|Add any value to enable PR collection.	|Optional			|
|`opt-out-nonvulnerability-issue-collection`		|text		|Add any value to opt out of nonvulnerability related issue collection.	|Optional			|
|`pipeline-config`		|text		|The configuration file that customizes pipeline behavior.	|Required			|
|`pipeline-config-branch`		|text		|The branch of the DevSecOps pipeline configuration.	|Optional			|
|`pipeline-config-repo`		|text		|The repo URL of the DevSecOps pipeline configuration location.	|Optional			|
|[`pipeline-debug`](#pipeline-parm-pipeline-debug)		|select		|The pipeline debug mode switch.	|Optional			|
|`pipeline-dockerconfigjson`		|SECRET		|The base64-encoded Docker `config.json` file that pulls images from a private registry.	|Optional	  	|
|[`priority`](#pipeline-parm-priority)		|text		|The priority of the change request.	|Optional			|
|[`purpose`](#pipeline-parm-purpose)		|text		|The reason why the change is needed.	|Optional			|
|`region`		|text		|The target region where the app is deployed.	|Optional			|
|[`purpose`](#pipeline-parm-purpose)		|text		|The reason why the change is needed.	|Optional			|
|[`source-environment`](#pipeline-parm-source-environment)	|text		|The source environment that the app is promoted from.	|Required			|
|[`summary-retry-attempts`](#pipeline-parm-summary-retry-attempts)		|text		|Set the maximum number of retries for the evidence summary upload. The default is `5`.  |Optional			|
|[`summary-max-retry-time`](#pipeline-parm-summary-max-retry-time)		|text		|Set the maximum backoff time for the evidence summary upload. The actual backoff time is generated exponentially with each retry until it reaches the maximum backoff time set with this parameter. The default is `32`.  |Optional			|
|[`target-environment`](#pipeline-parm-target-environment)		|text		|The target environment that the app is deployed.	|Required			|
|[`target-environment-purpose`](#pipeline-parm-target-environment-purpose)		|text		|The context of the environment where the app is deployed. Valid values: `pre_prod`, `production`	|Required |
|[`target-environment-detail`](#pipeline-parm-target-environment-detail)		|text		|Description of the target environment where the app is deployed. |Required |	
|`version`		|text		|The version of the app to deploy.	|Required	  	|
{: caption="Table 3. Continuous deployment parameters" caption-side="bottom"}
{: #cd-parameters}
{: tab-title="Continuous deployment parameters"}
{: tab-group="IAM-simple"}
{: class="simple-tab-table"}

|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|`app-url` 		|text 		|The URL of your application that is deployed on the target. Recommended to use staging application url.			|Required			|
|`artifactory-dockerconfigjson`		|SECRET		|The base64-encoded Docker `config.json` file that stores credential information for artifactory.			|Required			|
|`baseimage-auth-email`		|text		|The credentials for the application Dockerfile base image that is required by the Code Risk Analyzer scan.	|Optional			|
|`baseimage-auth-host`		|text		|The credentials for the application Dockerfile base image that is required by the Code Risk Analyzer scan. |Optional			|
|`baseimage-auth-password`		|SECRET		|The credentials for the application Dockerfile base image that is required by the Code Risk Analyzer scan.	|Optional			|	
|`baseimage-auth-user`		|text		|The user credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.			|Optional			|
|`batched-evidence-collection`|text|Set this flag to enable evidence collection in batched mode, which minimizes the network calls. Default `0`|Optional|
|`branch`		|text		|The Git repo branch of the repository that contains the source code of your application. Default value: `master`. |Optional			|
| `cocoa-config-retry-status-code` | text   | The response status code for an API call that needs to be retried  in the cocoa CLI. For example: `500,404`.  Default value: `500` | Optional |
| `cocoa-config-max-retry-attempts`| text   | The number of times that the cocoa CLI can retry the API call before giving up. For example: `5`. Default value: `3` | Optional |
| `cocoa-config-git-default-branch (supported in Q4, 2022)` | text   | The default native Git branch for the cocoa CLI. For example: `main`. Default value: `master`   | Optional |
| `cocoa-display-elapsed-time` | text   | Set this to a non-empty value to print the elapsed time in seconds for cocoa CLI command execution. Example: `Elapsed time: 5.32 seconds`.   | Optional |
|[`compliance-baseimage`](#pipeline-parm-compliance-baseimage)		|text		|The baseimage for running the built-in pipeline code. |Optional			|
|`cos-api-key`		| SECRET		| The Cloud Object Storage API key.	| Optional			|
|`cos-bucket-name`		| text		| The name of the bucket in your Cloud Object Storage instance that is used as an evidence locker.	|Optional			|
|`cos-endpoint`		| text		| The endpoint that stores the evidence in the Cloud Object Storage instance that is used as an evidence locker. For more information, see [Endpoint Types](/docs/cloud-object-storage?topic=cloud-object-storage-endpoints#advanced-endpoint-types). | Optional			|
| `cra-custom-script-path`  | text   | Path to a custom script to be run before CRA scanning. This script is sourced to provide the option to set ENV variables in the context of the CRA BOM tool. | Optional |
|`cra-cveignore-path`     |text   |File path to the cveignore, relative to the application repository root. Default file path is `.cra/.cveignore` if value is not provided.   |Optional    |
|`cra-docker-build-context`     |text   |If this flag is specified, Code Risk Analyzer uses the directory in the path parameter as the Docker build context. The default value is `false`. |Optional |
|`cra-docker-buildflags`   |text   |Customize docker build command for build stage scanning. The parameter is empty by default.    |Optional    |
|`cra-dockerfile-pattern`             | text   | Ability to add Dockerfiles with a different naming convention, such as `enterprise-linux.Dockerfile` | Optional |
|`cra-exclude-devdependencies`		| text		| Specifies whether to exclude dev dependencies from scanning (`true` or `false`). The default value is `false`.	| Optional			|
|`cra-gradle-exclude-configs`     | text   | Specifies which Gradle configurations to exclude dependencies in scanning. Example: `runtimeClasspath,testCompileClasspath`. The parameter is empty by default.   | Optional   |
| `cra-generate-cyclonedx-format`      | text   | If set to `1`, CRA also generates the BOM in `cyclonedx` format (defaults to `0`). | Optional |
| `cra-ibmcloud-api-key` | SECRET | Overwrites the `ibmcloud-api-key` that is used by the CRA tasks. | Optional |
|`cra-maven-exclude-scopes`       | text   | Specifies which maven scopes to exclude dependencies in scanning. Example: `test,compile`. The parameter is empty by default.  | Optional   |
|`cra-nodejs-create-package-lock`		| text		| Enable Code Risk Analyzer discovery to build the `package-lock.json` file for node.js repos. This parameter is set to false by default.	| Optional			|
|`cra-python-create-requirements-txt`		|text		|Deprecated. The new CRA tools don't use this parameter anymore. Enable Code Risk Analyzer discovery to build the `requirements.txt` file for Python repos. This parameter is set to false by default.	|Optional			|
|[`custom-exempt-label`](#pipeline-parm-custom-exempt-label)  |text   |Defines the custom label with which the incident issue has been marked as exempted.    |Optional    |
|[`detect-secrets-baseline-filename`](#detect-secrets-baseline-filename) | text | The name of the baseline file in your app repository. | Optional |
|[`detect-secrets-exclusion-list`](#detect-secrets-exclusion-list) | text | A regex list of files to be excluded in the detect-secrets scan. | Optional |
|[`detect-secrets-image`](#detect-secrets-image) | text | Specifies an alternative detect-secrets image, including custom images or specific versions of the official image. | Optional |
|[`detect-secrets-verbose`](#detect-secrets-verbose) | text | Outputs the name of the file that is currently being scanned. | Optional |
|`dev-region`		|text		|The {{site.data.keyword.cloud_notm}} region that hosts the cluster. Required for default static and dynamic scans. |Required			|
|`dev-resource-group`		|text		|The cluster resource group.	|Required			|
|`dind-image`		|text		|Base image to run sidecars.	|Optional			|
|`doi-environment`		|text		|The {{site.data.keyword.DRA_short}} target environment.	|Optional			|
|`doi-ibmcloud-api-key`  |SECRET  | The {{site.data.keyword.cloud_notm}} API key that interacts with the DevOps Insights instance where DOI records are uploaded. |Optional  |
|`doi-tags`		|text		|Comma-separated custom tags.	|Optional			|
|`doi-toolchain-id`		|text		|The {{site.data.keyword.DRA_short}} instance toolchain ID.	|Optional			|
|`environment-tag`     |text   |Tag name that represents the target environment in the inventory. Example: `prod_latest`    |Required           |
|`evidence-repo`		|tool integration		|The evidence repo URL.	|Optional			|
|`git-token`		|SECRET		|The Git repo access token.	|Optional			|
|`github-token`		|SECRET		|The GitHub repo access token.	|Optional			|
|`grit-token`		|SECRET		|The {{site.data.keyword.gitrepos}} access token.	|Optional			|
|`ibmcloud-api-key`		|SECRET		|The {{site.data.keyword.cloud_notm}} API key that interacts with the `ibmcloud` CLI tool.	|Required			|
|`ibmcloud-enable-session-keep-alive` | select | Keep the IBMCloud CLI session alive for long running jobs in Code Risk Analyzer scan, if this value is set to 1. |Optional			|
|`incident-assignee`		|text		|The assignee for the incident issues (GitHub or GitLab username). |Optional			|
|`incident-assignees`		|text		|The one or more assignees for the incident issues (one or more usernames that are separated by a comma). Note that this parameter can be used only with GitHub and GitLab Premium accounts. |Optional			|
|`incident-label`		|text		|The label for new incident issues.	|Optional			|
|`incident-labels`		|text		|The labels for new incident issues. `incident-labels` can be one or more labels that are separated by a comma.	|Optional			|
|`incident-repo`  	|tool integration		|The incident issues repo URL.	|Optional			|
|`inventory-exclude`    | text | Comma-separated list of the inventory entries that are excluded for scanning and testing in the CC pipeline. Entries can also be specified by using glob patterns.  |Optional			|
|`inventory-include`    | text | Comma-separated list of the inventory entries that are included for scanning and testing in the CC pipeline. Entries can also be specified by using glob patterns. If not set, all entries are scanned and tested.  |Optional			|
|`inventory-repo`		|tool integration		|The inventory repo URL.	|Optional			|
|`opt-in-auto-close` |text		|Enables auto-closing of issues coming from vulnerabilities, once the vulnerability is no longer detected by the CC pipeline run.	|Optional			|
|`opt-in-dynamic-api-scan`		|text		|To enable the OWASP Zap API scan. 	|Optional			|
|`opt-in-dynamic-scan`		|text		|To enable the OWASP Zap scan.	 |Optional			|
|`opt-in-dynamic-ui-scan`		|text	|To enable the OWASP Zap UI scan.	 |Optional			|
|`opt-in-sonar`		|text		|The Sonarqube scan integration.	|Optional			|
|`opt-in-sonar-hotspots`		|text		|Allows hotspot detection in sonarqube scans.	|Optional			|
|`opt-out-nonvulnerability-issue-collection`		|text		|Add any value to opt out of nonvulnerability related issue collection.	|Optional			|
|`pipeline-config`		|text		|The configuration file that customizes pipeline behavior.	|Required			|
|`pipeline-config-branch`		|text		|The branch of the DevSecOps pipeline configuration.	|Optional			|
|`pipeline-config-repo`		|text		|The repo URL of the DevSecOps pipeline configuration location.	|Optional			|
|[`pipeline-debug`](#pipeline-parm-pipeline-debug)		|select		|The pipeline debug mode switch.  |Optional			|
|`pipeline-dockerconfigjson`		|SECRET		|The base64-encoded Docker `config.json` file that pulls images from a private registry.	|Optional	  	|
|`region-prefix`  |text  |Region name as prefix for the `latest` tag for the target environment. Example: `us-south`   |Optional  |
|`repo-url` 		|text 		|The URL of your application repository.			|Required, if same inventory is used to store multiple application artifacts.			|
|`repository-integration`		|text		|The integration name for the repo.	|Optional			|
|[`slack-notifications`](#pipeline-parm-slack-notifications)		|text		|The switch that turns the Slack integration on or off |Optional		|
|`sonarqube`		|tool integration		|The Sonarqube tool integration.	|Optional			|
|`sonarqube-config`		|text		|Runs a SonarQube scan in an isolated Docker-in-Docker container (default configuration) or in an existing development Kubernetes cluster (cluster configuration). Alternatively, you can bring your own SonarQube instance and configure the SonarQube tool integration (custom configuration). Options: `default`, `cluster`, or `custom`. Default is `default`. For more information, see ([Adding SonarQube to the continuous integration pipeline](/docs/devsecops?topic=devsecops-sonarqube#sonarqube-ci-pipeline)).| Required |
|`opt-in-cra-auto-remediation`		|text		|Specifies if {{site.data.keyword.cloud_notm}} `cra auto remediation` is run (`true` or `false`).	|Optional			|
|`opt-in-cra-auto-remediation-enabled-repos`		|text		|Specifies the list of comma separated repository names that are to be turned on for {{site.data.keyword.cloud_notm}} `cra auto remediation`. This parameter is considered only if `opt-in-cra-auto-remediation` is set to `true`	|Optional			|
|`opt-in-cra-auto-remediation-force`		|text		|Forces {{site.data.keyword.cloud_notm}} `cra auto remediation` to update the packages even if the major version is different to the current vulnerable package version (`true` or `false`). This parameter is considered only if `opt-in-cra-auto-remediation` is set to `true`	|Optional			|
|`sbom-validation-collect-evidence`   |text   | Enable evidence collection for sbom validation scan for cyclonedx sbom. If this value is set to 1, then the sbom validation collects evidence.   |Optional     |
|[`sysdig-scan`](#pipeline-param-sysdig-scan)	|select		|Enable Sysdig scan for images. If this value is set to 1, then the Sysdig scan is enabled.	|Required			|
|`sysdig-api-token`		|text		|Sysdig API token value. The token is visible from the Sysdig instance's User Profile page. This value is needed for running Sysdig scan.	|Required			|
|`sysdig-url`		|text		|The URL of the Sysdig instance to be used for the scan. Default value is `https://secure.sysdig.com`	|Optional			|
|`sysdig-inline-scanner-image`		|text		|Sysdig inline scanner image to be used for the scan. Default value is `quay.io/sysdig/secure-inline-scan:2`	|Optional			|
|`cr-ibmcloud-api-key`		|SECRET		| Overrides `ibmcloud-api-key` if provided, for pulling the image from container registry for the Sysdig scan.	|Optional			|
{: caption="Table 4. Continuous compliance parameters" caption-side="bottom"}
{: #pipelines-cc-parameters}
{: tab-title="Continuous compliance parameters"}
{: tab-group="IAM-simple"}
{: class="simple-tab-table"}

|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|`dind-image`		|text		|Base image to run sidecars.	|Optional			|
{: caption="Table 5. Promotion parameters" caption-side="bottom"}
{: #pipelines-promotion-parameters}
{: tab-title="Promotion parameters"}
{: tab-group="IAM-simple"}
{: class="simple-tab-table"}

## Specifications
{: #pipeline-parm-specs}

### assignee
{: #pipeline-parm-assignee}

This parameter is for the promotion pull request. This value holds the assignee of the change request. This should be a functional id or an email who will be assigned to the change request in the change management system.

### backout-plan
{: #pipeline-parm-backout-plan}

This parameter is for the promotion pull request. Plan of how the change will be rolled back in case of a failure. By default it is empty.

### branch
{: #pipeline-parm-branch}

This is an optional parameter for the CI pipeline. It determines which branch we pull the code from the app repository. By default, it's `master`. 

### change-request-duration
{: #pipeline-parm-change-request-duration}

This is an optional parameter for the CD pipeline. The value of it will be the created change request's maintenance duration. By default it's `30 min`.

### change-request-id
{: #pipeline-parm-change-request-id}

This is an optional parameter for the CD pipeline. By providing a change request ID, the pipeline will skip the creation of a new change request and will use the provided change request. If this parameter is set to `notAvailable` or left empty, a change request is automatically created by the continuous deployment pipeline. By default it's `notAvailable`.

### cluster
{: #pipeline-parm-cluster}

This parameter holds the name of the cluster in the CD pipeline, which is used for the Docker deploy. This parameter is required for the example app.

### cluster-name
{: #pipeline-parm-cluster-name}

This parameter holds the name of the cluster in the CI pipeline, which is used for the Docker deploy. This parameter is required.

### cluster-region
{: #pipeline-parm-cluster-region}

This is a parameter for the CD pipeline. The {{site.data.keyword.cloud_notm}} region that hosts the cluster.	This is a required parameter for the example app.

### compliance-baseimage
{: #pipeline-parm-compliance-baseimage}

This parameter holds the version of the compliance-baseimage. If you want to use something other than our latest image, you can use this parameter.

Example:
`icr.io/continuous-delivery/toolchains/devsecops/baseimage:some-other-tag`

### custom-exempt-label
{: #pipeline-parm-custom-exempt-label}

This is an optional parameter for the CI and CC pipelines. If you marked an incident issue permanently exempted with a custom label, then this parameter should hold the value of the custom label.

### customer-impact
{: #pipeline-parm-customer-impact}

This parameter is for the promotion pull request, it records the impact of the change request on the customer. By default the parameter is the pipe separated string `'Critical | High | Moderate | Low | No_Impact'`. Edit the default string to select one of the options.

### deployment-impact
{: #pipeline-parm-deployment-impact}

This parameter is for the promotion pull request, it records the impact of the change request on the deployment. By default the parameter is the pipe separated string `'Small | Large'`. Edit the default string to select one of the options.

### description
{: #pipeline-parm-description}

This parameter is for the promotion pull request. This parameter contains the description of the change, that will be appended to the Change Request Description. By default it is empty.


### detect-secrets-baseline-filename
{: #detect-secrets-baseline-filename}

This parameter specifies a custom file name for the baseline file that is used by detect-secrets. By default, detect-secrets looks for a file that is named `.secrets.baseline` in the repository root directory. However, if you name your baseline file differently, you can provide its file name by using this parameter.

### detect-secrets-exclusion-list
{: #detect-secrets-exclusion-list}

This parameter is an environment property that overrides the default exclusion list when a run is done without an existing baseline file. This parameter identifies files to ignore so that issues are not created that are linked to them.

### detect-secrets-image
{: #detect-secrets-image}

This parameter can be used to specify a different detect-secrets image to use, such as a custom image or a specific version of the official detect-secrets image.

### detect-secrets-verbose
{: #detect-secrets-verbose}

This parameter, when set to 1, logs the name of the current file being scanned.

### impact
{: #pipeline-parm-impact}

This parameter is for the promotion pull request. Additional notes on what this Change Implementation will impact. By default it is empty.

### pipeline-debug
{: #pipeline-parm-pipeline-debug}

If this is set to 1, the pipeline will run in debug mode, and the logs will show more information. By default, it is set to 0.

### priority
{: #pipeline-parm-priority}

This parameter is for the promotion pull request. The priority of the change request. By default it is `'Critical | High | Moderate | Low | Plan'`. You can change it to one of these values.

### purpose
{: #pipeline-parm-purpose}

This parameter is for the promotion pull request. The reason why the change is needed. By default it is empty.

### signing-key
{: #pipeline-parm-signing-key}

This parameter is for the CI pipeline. It is a secret type parameter that holds the base64-encoded private key for signing images by using `.pfx` file data.
 
### slack-notifications
{: #pipeline-parm-slack-notifications}

Switch to turn on/off the Slack Integration. This is turned off by default, so the value is `0`. Set to `1` to turn it on, set to `0` to turn it off.

### sonarqube
{: #pipeline-parm-sonarqube}

This parameter is for all the pipelines that use static scan stages. This is an integration type parameter. If you are using your own sonarqube integration, select that integration as value. By default, this parameter is empty.

### sonarqube-namespace
{: #pipeline-parm-sonarqube-namespace}

This parameter is for all the pipelines that use static scan stages. This is the configured Kubernetes namespace to use with the SonarQube instance that is created by the pipeline, you can change it to any namespace that you want to use.

### sonarqube-scan-command
{: #pipeline-parm-sonarqube-scan-command}

This parameter is for the pipelines that use static scan stages. `sonarqube-scan-command` specifies the command to start the sonarqube scan. If this parameter is not set, the default is `docker run --network host -v "$SONAR_DIR":/usr/sonar_scan -v "$WORKSPACE/$path":/usr/src "$sonarqube_scanner_image" sonar-scanner -Dsonar.working.directory=/usr/sonar_scan`. The default parameter uses the Docker `sonar-scanner` image to perform the scan.

For a Maven Java project, set this parameter to `mvn -Dmaven.repo.local="${WORKSPACE}/.m2" -Dsonar.login="$(cat /tmp/sonarqube-token)" -Dsonar.host.url="$SONAR_HOST_URL" -Dsonar.projectKey="$SONAR_PROJECT_KEY" -Dsonar.projectName="$SONAR_PROJECT_KEY" -Dsonar.working.directory="$SONAR_DIR" sonar:sonar` to use the Maven computed classpath for sonar scan.

### source-environment
{: #pipeline-parm-source-environment}

The source environment that the app is promoted from, which is the source inventory branch of the promotion. The default value is `master`. This value can be overwritten with the usage of a webhook trigger by adding the `source-environment` property to the payload.

### static-scan-retry-count
{: #pipeline-parm-static-scan-retry-count}

This parameter is for any static scan that you want to run in a pipeline. These scans use API calls, which can take a short time to work. Therefore, retry these calls if needed. You can change the times of retries with this parameter. By default, this parameter is set to 30.

### static-scan-retry-sleep
{: #pipeline-parm-static-scan-retry-sleep}

This parameter is for any static scan that you want to run in a pipeline. These scans use API calls, which can take a short time to work. Therefore, retry these calls if needed. You can change the amount of seconds to wait with this parameter. By default, this parameter is set to 10.

### summary-retry-attempts
{: #pipeline-parm-summary-retry-attempts}

Use a retry method to upload the evidence summary to make sure it is successful. You can change the times of retries with this parameter. By default, this parameter is set to 5.


### summary-max-retry-time
{: #pipeline-parm-summary-max-retry-time}

Use a retry method to upload the evidence summary to make sure it is successful. You can change the amount of seconds to wait with this parameter. By default, this parameter is set to 32.

### target-environment
{: #pipeline-parm-target-environment}

The target environment that the app is deployed to, which is the target inventory branch of the promotion. The default value is `prod`. This value can be overwritten with the usage of a webhook trigger by adding the `target-environment` property to the payload.

### target-environment-purpose
{: #pipeline-parm-target-environment-purpose}

This parameter is for the CD pipeline. `target-environment-purpose` determines the context of the target environment and how it is used. Valid values are `pre_prod` and `production`. Mark anything that is not a `production` related change as `pre_prod`.

### target-environment-detail
{: #pipeline-parm-target-environment-detail}

This parameter is for the CD pipeline, it describes the target environment.



### sysdig-scan
{: #pipeline-param-sysdig-scan}

This parameter is for the pipelines that use scan artifact stages to run the Sysdig scan by using `sysdig inline scanner` on `icr.io` images.


---

copyright:
  years: 2021, 2022
lastupdated: "2022-03-23"

keywords: DevSecOps, scans, compliance

subcollection: devsecops

---

{:shortdesc: .shortdesc}
{:table: .aria-labeledby="caption"}
{:external: target="_blank" .external}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:download: .download}
{:help: data-hd-content-type='help'}
{:support: data-reuse='support'}

# Pipeline parameters
{: #cd-devsecops-pipeline-parm}

Parameters are provided out of the box for the pull request, continuous integration, and continuous delivery pipelines.
{: shortdesc}

You can add parameters to the pipelines on the pipeline UI and access them from the [custom scripts](/docs/devsecops?topic=devsecops-custom-scripts).
{: tip}

## Pull request parameters
{: #cd-pr-parameters}

The following table lists and describes the pull request parameters for pipelines:

|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|artifactory-dockerconfigjson 		|SECRET 		|The base64-encoded Docker `config.json` file that stores credential information for artifactory.	 			|Optional			|
|baseimage-auth-user		|text		|The user credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.			|Optional			|
|baseimage-auth-email		|text 		|The e-mail credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.		|Optional			|
|baseimage-auth-host		|text		|The host credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.	|Optional			|
|baseimage-auth-password		|SECRET		|The password credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan. |Optional			|
| cra-custom-script-path  | text   | Path to a custom script to be run before CRA scanning. This script is sourced to provide the option to set ENV variables in the context of the CRA BOM tool. | Optional |
|cra-docker-build-context     |text   |If this flag is specified, Code Risk Analyzer uses the directory in the path parameter as the Docker build context. The default value is `false`. |Optional |
|cra-dockerfile-pattern             | text   | Ability to add Dockerfiles with a different naming convention, such as `enterprise-linux.Dockerfile` | optional |
|cra-exclude-devdependencies		|text		|Specifies whether to exclude dev dependencies from scanning (`true` or `false`). The default value is `false`.	|Optional			|
|git-token		|SECRET		|The Git repo access token.	|Optional			|
|github-token		|SECRET		|The GitHub repo access token.	|Optional			|
|grit-token		|SECRET		|The Git Repo and Issue Tracking access token.	|Optional			|
|ibmcloud-api-key		|SECRET		|The {{site.data.keyword.cloud}} API key that interacts with the `ibmcloud` CLI tool.	|Required			|
|pipeline-config		|text		|The configuration file that customizes pipeline behavior.	|Optional			|
|pipeline-config-branch		|text		|The branch of the DevSecOps pipeline configuration.	|Optional			|
|pipeline-config-repo		|text		|The repo URL of the DevSecOps pipeline configuration location.	|Optional			|
|pipeline-dockerconfigjson		|SECRET		|The base64-encoded Docker `config.json` file that pulls images from a private registry.	|Optional			|
|onepipeline-dockerconfigjson		|SECRET		|Deprecated. The base64-encoded Docker `config.json` file that pulls images from a private registry.	|Optional			|
|[pipeline-debug](#pipeline-debug)		|select		|The pipeline debug mode switch.  |Optional			|
|slack-notifications		|text		|The switch that turns the Slack integration on or off.	|Optional			|	|[slack-notifications](#pipeline-parm-slack-notifications)		|text		|The switch that turns the Slack integration on or off |Optional		|
{: caption="Table 1. Pull request parameters" caption-side="top"}

## Continuous integration parameters
{: #cd-ci-parameters}

The following table lists and describes the continuous integration parameters for pipelines:

|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|app-name 		|text 		|The name of your application that is specified in the toolchain settings.			|Required			|
|artifactory-dockerconfigjson		|SECRET		|The base64-encoded Docker `config.json` file that stores credential information for artifactory.			|Optional			|
|baseimage-auth-user		|text		|The user credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.			|Optional			|
|baseimage-auth-email		|text		|The credentials for the application Dockerfile base image that is required by the Code Risk Analyzer scan.	|Optional			|	|baseimage-auth-email		|text 		|The e-mail credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.		|Optional			|
|baseimage-auth-host		|text		|The credentials for the application Dockerfile base image that is required by the Code Risk Analyzer scan. |Optional			|	|baseimage-auth-host		|text		|The host credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.	|Optional			|
|baseimage-auth-password		|SECRET		|The credentials for the application Dockerfile base image that is required by the Code Risk Analyzer scan.	|Optional			|	|baseimage-auth-password		|SECRET		|The password credential for the base image of the application Dockerfile, required by the Code Risk Analyzer scan. |Optional			|
|branch		|text		|The Git repo branch.	|Optional			|
|cluster-name		|text		|The name of the Docker build cluster.	|Required			|
|[compliance-baseimage](#pipeline-parm-compliance-baseimage)	|text		|The baseimage for running the built-in pipeline code.	|Optional	
|cos-api-key		|text		|The Cloud Object Storage API key.	|Optional			|
|cos-bucket-name		|text		|The name of the bucket in your Cloud Object Storage instance that is used as an evidence locker.	|Optional			|
|cos-endpoint		|text		|The endpoint of your Cloud Object Storage instance that is used as an evidence locker.   |Optional			|
|custom-image-tag		|text		|The custom tag for the image in a comma-separated list.	|Optional			|
| cra-custom-script-path  | text   | Path to a custom script to be run before CRA scanning. This script is sourced to provide the option to set ENV variables in the context of the CRA BOM tool. | Optional |
|cra-docker-build-context     |text   |If this flag is specified, Code Risk Analyzer uses the directory in the path parameter as the Docker build context. The default value is `false`. |Optional |
|cra-dockerfile-pattern             | text   | Ability to add Dockerfiles with a different naming convention, such as `enterprise-linux.Dockerfile` | optional |
|cra-exclude-devdependencies		|text		|Specifies whether to exclude dev dependencies from scanning (`true` or `false`). The default value is `false`.	|Optional			|
|dev-cluster-namespace		|text		|The Kubernetes cluster namespace where the Docker engine is hosted and deployed.	|Required			|
|dev-region		|text		|The {{site.data.keyword.cloud}} region that hosts the cluster.	|Required			|
|dev-resource-group		|text		|The cluster resource group.	|Required			|
|doi-buildnumber		|text		|The build number value to use as the `--buildnumber` flag in `ibmcloud doi` commands.	|Optional			|
|doi-environment		|text		|The {{site.data.keyword.DRA_short}} target environment.	|Optional			|
|doi-logicalappname		|text		|The app name to use as the `--logicalappname` flag in `ibmcloud doi` commands	|Optional			|
|doi-toolchain-id		|text		|The {{site.data.keyword.DRA_short}} instance toolchain ID.	|Optional			|
|git-token		|SECRET		|The Git repo access token.	|Optional			|
|github-token		|SECRET		|The GitHub repo access token.	|Optional			|
|grit-token		|SECRET		|The Git Repo and Issue Tracking access token.	|Optional			|
|ibmcloud-api-key		|SECRET		|The {{site.data.keyword.cloud}} API key that interacts with the `ibmcloud` CLI tool.	|Required			|
|incident-assignee		|text		|The assignee for the incident issues (GitHub or GitLab user name). |Optional			|
|incident-label		|text		|The label for new incident issues.	|Optional			|
|pipeline-config		|text		|The configuration file that customizes pipeline behavior.	|Required			|
|pipeline-config-branch		|text		|The branch of the DevSecOps pipeline configuration.	|Optional			|
|pipeline-config-repo		|text		|The repo URL of the DevSecOps pipeline configuration location.	|Optional			|
|onepipeline-dockerconfigjson		|SECRET		|Deprecated. The base64-encoded Docker `config.json` file that pulls images from a private registry.	|Optional			|
|registry-namespace		|text		|The container registry namespace for the image.	|Required			|
|registry-region		|text		|The {{site.data.keyword.cloud}} region for the image registry.	|Required			|
|repository-integration		|text		|The integration name for the repo.	|Optional			|
|signing-key		|SECRET		|The base64-encoded private key for signing images by using `.pfx` file data.	|Required			|
|[pipeline-debug](#pipeline-debug)		|select		|The pipeline debug mode switch.  |Optional			|
|[slack-notifications](#pipeline-parm-slack-notifications)		|text		|The switch that turns the Slack integration on or off |Optional		|
|sonarqube		|tool integration		|The Sonarqube tool integration.	|Optional			|
|sonarqube-namespace 	|text		|The configured Kubernetes namespace to use with the SonarQube instance that is created by the pipeline.	|Optional			|
|static-scan-retry-count		|text		|The number of retries to wait for the Sonarqube instance to be established.	|Optional			|
|static-scan-retry-sleep		|text		|The amount of wait time per retry iteration.	|Optional			|
|va-scan-retry-count		|text		|The number of retries to wait for the vulnerability report scan.	|Required			|
|va-scan-retry-sleep	|text		|The number of wait times per retry iteration.	|Optional			|
|version		|text		|The version of the app to deploy.	|Required			|
|inventory-repo		|tool integration		|The inventory repo URL.	|Optional			|
|evidence-repo		|tool integration		|The evidence repo URL.	|Optional			|
|incident-repo  	|tool integration		|The incident issues repo URL.	|Optional			|
{: caption="Table 2. Continuous integration parameters" caption-side="top"}

## Continuous delivery parameters
{: #cd-parameters}

The following table lists and describes the continuous delivery parameters for pipelines:

|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|artifactory-dockerconfigjson		|SECRET		|The base64-encoded Docker `config.json` file that stores credential information for artifactory.			|Optional			|
|[assignee](#pipeline-parm-assignee)		|text		|The assignee of the change request.			|Optional			|
|[backout-plan](#pipeline-parm-backout-plan)	|text		|Plan of how the change will be rolled back in case of a failure.			|Optional			|
|change-request-id    |text   |The ID of an open change request. If this parameter is set to `notAvailable` by default, a change request is automatically created by the continuous delivery pipeline. |Optional			|
|cluster	|text 		|The name of the Docker build cluster.		|Required			|
|cluster-region		|text		|The {{site.data.keyword.cloud}} region that hosts the cluster.	|Required			|
|[compliance-baseimage](#pipeline-parm-compliance-baseimage)	|text		|The baseimage for running the built-in pipeline code. |Optional			|
|cos-api-key		|text		|The Cloud Object Storage API key.	|Optional			|
|cos-bucket-name		|text		|The name of the bucket in your Cloud Object Storage instance that is used as an evidence locker.	|Optional			|
|cos-endpoint		|text		|The endpoint of your Cloud Object Storage instance that is used as an evidence locker.   |Optional			|
|[description](#pipeline-parm-description)		|text		|The description of the change that will be appended to the Change Request Description.	|Optional			|
|doi-buildnumber		|text		|The build number value to use as the `--buildnumber` flag in `ibmcloud doi` commands.	|Optional			|
|doi-environment		|text		|The {{site.data.keyword.DRA_short}} target environment.	|Optional			|
|doi-logicalappname		|text		|The app name to use as the `--logicalappname` flag in `ibmcloud doi` commands	|Optional			|
|doi-toolchain-id		|text		|The {{site.data.keyword.DRA_short}} instance toolchain ID.	|Optional			|
|emergency-label		|text		|Identifies the pull request as an emergency.	|Optional			|
|force-redeploy    |text   |Forces the deployment or redeployment of the app even if the last deployment does not contain a delta in the inventory. Set this parameter to `true` to force a deployment of the app as if it is the first deployment on the specified target environment. By default, this parameter is set to `false`. |Optional |
|git-token		|SECRET		|The Git repo access token.	|Optional			|
|github-token		|SECRET		|The GitHub repo access token.	|Optional			|
|grit-token		|SECRET		|The Git Repo and Issue Tracking access token.	|Optional			|
|artifact-token		|SECRET		|The token where artifacts are stored	|Required if artifact repo is in different source provider.			|
|ibmcloud-api-key		|SECRET		|The {{site.data.keyword.cloud}} API key that interacts with the `ibmcloud` CLI tool.	|Required			|
|[impact](#pipeline-parm-impact)		|text		|Additional notes on what this change implementation impacts.	|Optional			|
|pipeline-config		|text		|The configuration file that customizes pipeline behavior.	|Required			|
|pipeline-config-branch		|text		|The branch of the DevSecOps pipeline configuration.	|Optional			|
|pipeline-config-repo		|text		|The repo URL of the DevSecOps pipeline configuration location.	|Optional			|
|[pipeline-debug](#pipeline-debug)		|select		|The pipeline debug mode switch.	|Optional			|
|pipeline-dockerconfigjson		|SECRET		|The base64-encoded Docker `config.json` file that pulls images from a private registry.	|Optional	  	|
|[priority](#pipeline-parm-priority)		|text		|The priority of the change request.	|Optional			|
|[purpose](#pipeline-parm-purpose)		|text		|The reason why the change is needed.	|Optional			|
|region		|text		|The target region where the app is deployed.	|Optional			|
|[slack-notifications](#pipeline-parm-slack-notifications)	|text		|The switch that turns the Slack integration on or off.  |Optional			|
|[source-environment](#pipeline-parm-source-environment)	|text		|The source environment that the app is promoted from.	|Required			| 
|summary-retry-attempts		|text		|Set the maximum number of retries for the evidence summary upload. The default is `5`.  |Optional			|
|summary-max-retry-time		|text		|Set the maximum backoff time for the evidence summary upload. The actual backoff time will be generated as a random number between 1 and the maximum backoff time. The default is `5`.  |Optional			|
|[target-environment](#pipeline-parm-target-environment)		|text		|The target environment that the app is deployed to.	|Required			|
|version		|text		|The version of the app to deploy.	|Required	  	|
|inventory-repo		|tool integration		|The inventory repo URL.	|Optional			|
|evidence-repo		|tool integration		|The evidence repo URL.	|Optional			|
|incident-repo  	|tool integration		|The incident issues repo URL.	|Optional			|
{: caption="Table 3. Continuous delivery parameters" caption-side="top"}

## Specifications
{: #pipeline-parm-specs}

### assignee
{: #pipeline-parm-assignee}

This parameter is for to the promotion pull request. This value holds the assignee of the change request. This should be a functional id or an email who will be assigned to the change request in the change management system.

### backout-plan
{: #pipeline-parm-backout-plan}

This parameter is for to the promotion pull request. Plan of how the change will be rolled back in case of a failure. By default it is empty.

### compliance-baseimage
{: #pipeline-parm-compliance-baseimage}

This parameter holds the version of the compliance-baseimage. If you want to use something other than our latest image, you can use this parameter.

Example: 
`wcp-compliance-automation-team-docker-local.artifactory.swg-devops.com/compliance-baseimage:some-other-tag`

### description
{: #pipeline-parm-description}

This parameter is for to the promotion pull request. This parameter contains the description of the change, that will be appended to the Change Request Description. By default it is empty.

### impact
{: #pipeline-parm-impact}

This parameter is for to the promotion pull request. Additional notes on what this Change Implementation will impact. By default it is empty.

### pipeline-debug
{: #pipeline-parm-pipeline-debug}

If this is set to 1, the pipeline will run in debug mode, and the logs will show more information. By default, it is set to 0.

### priority
{: #pipeline-parm-priority}

This parameter is for to the promotion pull request. The priority of the change request. By default it is `'Critical | High | Moderate | Low | Plan'`. You can change it to one of these values.

### purpose
{: #pipeline-parm-purpose}

This parameter is for to the promotion pull request. The reason of why the change is needed. By default it is empty.

### slack-notifications
{: #pipeline-parm-slack-notifications}

Switch to turn on/off the Slack Integration. This is turned off by default, so the value is `0`. Set to `1` to turn it on, set to `0` to turn it off.

### source-environment
{: #pipeline-parm-source-environment}

The source environment that the app is promoted from, which is the source inventory branch of the promotion. The default value is `master`.

### target-environment
{: #pipeline-parm-target-environment}

The target environment that the app is deployed to, which is the target inventory branch of the promotion. The default value is `prod`.

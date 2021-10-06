---

copyright:
  years: 2021
lastupdated: "2021-09-15"

keywords: DevSecOps

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

Parameters are provided, out of the box, for pull request, continuous integration, and continuous delivery pipelines.
{: shortdesc}

You can add parameters to the pipelines on the pipeline UI, and access them from the [custom scripts](/docs/devsecops?topic=devsecops-custom-scripts).
{: tip}

## Pull request parameters
{: #cd-pr-parameters}

The following table lists and describes the pull request parameters for pipelines:

|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|artifactory-dockerconfigjson 		|SECRET 		|The base64-encoded Docker `config.json` file that stores credential information for artifactory.	 			|Optional			|
|baseimage-auth-user		|text		|The credentials for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.			|Optional			|
|baseimage-auth-email		|text 		|The credentials for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.		|Optional			|
|baseimage-auth-host		|text		|The credentials for the base image of the application Dockerfile, required by the Code Risk Analyzer scan.	|Optional			|
|baseimage-auth-password		|SECRET		|The credentials for the base image of the application Dockerfile, required by the Code Risk Analyzer scan. |Optional			|
|cra-exclude-devdependencies		|text		|Specifies whether to exclude dev dependencies from scanning (`true` or `false`). The  default value is `false`.	|Optional			|
|cra-exclude-scopes		|text		|Specifies which scopes to use to exclude dependencies from scanning. This parameter value is  empty by default. Valid values include test and compile.	|Optional			|
|git-token		|SECRET		|The Git repository (repo) access token.	|Optional			|
|ibmcloud-api-key		|SECRET		|The {{site.data.keyword.cloud}} API key that interacts with the `ibmcloud` CLI tool.	|Required			|
|pipeline-config		|text		|The configuration file that customizes pipeline behavior.	|Optional			|
|pipeline-config-branch		|text		|The branch of the DevSecOps pipeline configuration.	|Optional			|
|pipeline-config-repo		|text		|The repo URL of the DevSecOps pipeline configuration location.	|Optional			|
|pipeline-dockerconfigjson		|SECRET		|The base64-encoded Docker `config.json` file that pulls images from a private registry.	|Optional			|
|onepipeline-dockerconfigjson		|SECRET		|Deprecated. The base64-encoded Docker `config.json` file that pulls images from a private registry.	|Optional			|
|pipeline-debug		|select		|The pipeline debug mode switch.	|Optional			|
|slack-notifications		|text		|The switch that turns the Slack integration on or off.	|Optional			|
|cra-exclude-scopes		|text		|Specifies which scopes to use to exclude dependencies from scanning. Valid values include test and compile.	|Optional			|
|cra-nodejs-create-package-lock		|text		|Enable Code Risk Analyzer discovery to build the `package-lock.json` file for node.js repos. This parameter is set to false by default.	|Optional			|
|ibmcloud-api-key		|text		|Enable Code Risk Analyzer discovery to build the `requirements.txt` file for python repos. This parameter is set  to false by default.	|Optional			|
{: caption="Table 1. Pull request parameters" caption-side="top"}

## Continuous integration parameters
{: #cd-ci-parameters}

The following table lists and describes the continuous integration parameters for pipelines:

|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|app-name 		|text 		|The name of your application that is specified in the toolchain settings.			|Required			|
|artifactory-dockerconfigjson		|SECRET		|The base64-encoded Docker `config.json` file that stores credential information for artifactory.			|Optional			|
|baseimage-auth-user		|text 		|The credentials for the application Dockerfile base image that is required by the Code Risk Analyzer scan.		|Optional			|
|baseimage-auth-email		|text		|The credentials for the application Dockerfile base image that is required by the Code Risk Analyzer scan.	|Optional			|
|baseimage-auth-host		|text		|The credentials for the application Dockerfile base image that is required by the Code Risk Analyzer scan. |Optional			|
|baseimage-auth-password		|SECRET		|The credentials for the application Dockerfile base image that is required by the Code Risk Analyzer scan.	|Optional			|
|branch		|text		|The Git repo branch.	|Optional			|
|cluster-name		|text		|The name of the Docker build cluster.	|Required			|
|compliance-baseimage		|text		|The baseimage for running most of the built-in pipeline code.	|Optional			|
|cos-api-key		|text		|The Cloud Object Storage API key.	|Optional			|
|cos-bucket-name		|text		|The name of the bucket in your Cloud Object Storage instance that is used as an evidence locker.	|Optional			|
|cos-endpoint		|text		|The endpoint of your Cloud Object Storage instance that is used as an evidence locker.   |Optional			|
|custom-image-tag		|text		|The custom tag for the image in a comma-separated list.	|Optional			|
|cra-exclude-devdependencies		|text		|Specifies whether to exclude dev dependencies from scanning (`true` or `false`). The  default value is `false`.	|Optional			|
|cra-exclude-scopes		|text		|Specifies which scopes to use to exclude dependencies from scanning. This parameter value is  empty by default. Valid values include test and compile.	|Optional			|
|dev-cluster-namespace		|text		|The Kubernetes cluster namespace where the Docker engine is hosted and deployed.	|Required			|
|dev-region		|text		|The {{site.data.keyword.cloud}} region that hosts the cluster.	|Required			|
|dev-resource-group		|text		|The cluster resource group.	|Required			|
|doi-buildnumber		|text		|The build number value to use as the `--buildnumber` flag in `ibmcloud doi` commands.	|Optional			|
|doi-environment		|text		|The {{site.data.keyword.DRA_short}} target environment.	|Optional			|
|doi-logicalappname		|text		|The app name to use as the `--logicalappname` flag in `ibmcloud doi` commands	|Optional			|
|doi-toolchain-id		|text		|The {{site.data.keyword.DRA_short}} instance toolchain ID.	|Optional			|
|git-token		|SECRET		|The Git repo access token.	|Optional			|
|ibmcloud-api-key		|SECRET		|The {{site.data.keyword.cloud}} API key that interacts with the `ibmcloud` CLI tool.	|Required			|
|incident-label		|text		|The label for new incident issues.	|Optional			|
|pipeline-config		|text		|The configuration file that customizes pipeline behavior.	|Required			|
|pipeline-config-branch		|text		|The branch of the DevSecOps pipeline configuration.	|Optional			|
|pipeline-config-repo		|text		|The repo URL of the DevSecOps pipeline configuration location.	|Optional			|
|pipeline-dockerconfigjson		|SECRET		|The base64-encoded Docker `config.json` file that pulls images from a private registry.	|Optional			|
|pipeline-dockerconfigjson		|SECRET		|Deprecated. The base64-encoded Docker `config.json` file that pulls images from a private registry.	|Optional			|
|pipeline-debug		|select		|The pipeline debug mode switch.	|Optional			|
|registry-namespace		|text		|The container registry namespace for the image.	|Required			|
|registry-region		|text		|The {{site.data.keyword.cloud}} region for the image registry.	|Required			|
|repository-integration		|text		|The integration name for the repo.	|Optional			|
|slack-notifications		|text		|The switch that turns the Slack integration on or off.	|Optional			|
|sonarqube		|tool integration		|The Sonarqube tool integration.	|Optional			|
|static-scan-retry-count		|text		|The number of retries to wait for the Sonarqube instance to be established.	|Optional			|
|static-scan-retry-sleep		|text		|The amount of wait time per retry iteration.	|Optional			|
|vault-secret		|SECRET		|The base64-encoded private key for signing images by using `.pfx` file data.	|Required			|
|va-scan-retry-count		|text		|The number of retries to wait for the vulnerability report scan.	|Required			|
|va-scan-retry-sleep	|text		|The number of wait times per retry iteration.	|Optional			|
|version		|text		|The version of the app to deploy.	|Required			|
|inventory-repo		|tool integration		|The inventory repo URL.	|Optional			|
|evidence-repo		|tool integration		|The evidence repo URL.	|Optional			|
|incident-repo  	|tool integration		|The incident issues repo URL.	|Optional			|
|cra-exclude-scopes		|text		|Specifies which scopes to use to exclude dependencies from scanning. Valid values include test and compile.	|Optional			|
|cra-nodejs-create-package-lock		|text		|Enable Code Risk Analyzer discovery to build the `package-lock.json` file for node.js repos. This parameter is set to false by default.	|Optional			|
|cra-python-create-requirements-txt		|text		|Enable Code Risk Analyzer discovery to build the `requirements.txt` file for Python repos. This parameter is set to false by default.	|Optional			|
{: caption="Table 2. Continuous integration parameters" caption-side="top"}

## Continuous delivery parameters
{: #cd-parameters}

The following table lists and describes the continuous delivery parameters for pipelines:

|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|artifactory-dockerconfigjson		|SECRET		|The base64-encoded Docker `config.json` file that stores credential information for artifactory.			|Optional			|
|change-request-id    |text   |The ID of an open change request. If this parameter is set to `notAvailable` by default, a change request is automatically created by the continuous delivery pipeline. |Optional			|
|cluster	|text 		|The name of the Docker build cluster.		|Required			|
|cluster-region		|text		|The {{site.data.keyword.cloud}} region that hosts the cluster.	|Required			|
|compliance-baseimage		|text		|The baseimage for running most of the built-in pipeline code. |Optional			|
|cos-api-key		|text		|The Cloud Object Storage API key.	|Optional			|
|cos-bucket-name		|text		|The name of the bucket in your Cloud Object Storage instance that is used as an evidence locker.	|Optional			|
|cos-endpoint		|text		|The endpoint of your Cloud Object Storage instance that is used as an evidence locker.   |Optional			|
|doi-buildnumber		|text		|The build number value to use as the `--buildnumber` flag in `ibmcloud doi` commands.	|Optional			|
|doi-environment		|text		|The {{site.data.keyword.DRA_short}} target environment.	|Optional			|
|doi-logicalappname		|text		|The app name to use as the `--logicalappname` flag in `ibmcloud doi` commands	|Optional			|
|doi-toolchain-id		|text		|The {{site.data.keyword.DRA_short}} instance toolchain ID.	|Optional			|
|emergency-label		|text		|Identifies the pull request as an emergency.	|Optional			|
|force-redeploy    |text   |Forces the deployment or redeployment of the app even if the last deployment does not contain a delta in the inventory. Set this parameter to `true` to force a deployment of the app as if it is the first deployment on the specified target environment. By default, this parameter is set to `false`. |Optional |
|git-token		|SECRET		|The Git repo access token.	|Optional			|
|ibmcloud-api-key		|SECRET		|The {{site.data.keyword.cloud}} API key that interacts with the `ibmcloud` CLI tool.	|Required			|
|pipeline-config		|text		|The configuration file that customizes pipeline behavior.	|Required			|
|pipeline-config-branch		|text		|The branch of the DevSecOps pipeline configuration.	|Optional			|
|pipeline-config-repo		|text		|The repo URL of the DevSecOps pipeline configuration location.	|Optional			|
|pipeline-dockerconfigjson		|SECRET		|The base64-encoded Docker `config.json` file that pulls images from a private registry.	|Optional	  	|
|pipeline-debug		|select		|The pipeline debug mode switch.	|Optional			|
|region		|text		|The target region where the app is deployed.	|Optional			|
|slack-notifications		|text		|The switch that turns the Slack integration on or off.  |Optional			|
|source-environment		|text		|The source environment that the app is promoted from.	|Required			|
|target-environment		|text		|The target environment that the app is deployed to.	|Required			|
|version		|text		|The version of the app to deploy.	|Required	  	|
|inventory-repo		|tool integration		|The inventory repo URL.	|Optional			|
|evidence-repo		|tool integration		|The evidence repo URL.	|Optional			|
|incident-repo  	|tool integration		|The incident issues repo URL.	|Optional			|
{: caption="Table 3. Continuous delivery parameters" caption-side="top"}

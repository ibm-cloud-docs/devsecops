---

copyright:
  years: 2024, 2025
lastupdated: "2025-01-21"

keywords: DevSecOps, polyglot, inferred devsecops, spots

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Creating a pipeline with Inferred DevSecOps configuration
{: #devsecops-inferred-pipeline-configuration}


Once you've added your own application or micro-service to the DevSecOps CI toolchain, you can use the Inferred DevSecOps Pipeline Configuration feature to get started quickly. This feature:

-  Infers the content of the `.pipeline-config.yaml`  DevSecOps pipeline configuration file for you
-  Identifies the scripts that are needed to build, test, and deploy your code
-  Provides the code for these scripts, so you can focus on your application

By using this feature, you can easily onboard your micro-services or applications to DevSecOps pipelines and streamline your DevSecOps adoption.
{: shortdesc}

No additional steps are necessary to configure the Inferred DevSecOps Pipeline Configuration, as it's already integrated into the DevSecOps.
{: note}

## Prerequisites
{: #devsecops-inferred-pipeline-configuration-prereq}

1.  Set up your DevSecOps toolchains and integrate [your application's source code repository](/docs/devsecops?topic=devsecops-tutorial-ci-toolchain#tutorial-ci-toolchain-application).
   Do not use the default sample app repository. Instead, onboard your own application's repository.
   {: #note}

1.  Review the [basics of DevSecOps pipeline customization](/docs/devsecops?topic=devsecops-cd-devsecops-basics-pipelines-customization) to know more about the different templates that are available, support options, and other important information to get you started with DevSecOps.

## Getting started
{: #devsecops-inferred-pipeline-configuration-getstart}

To begin, configure your toolchains to use your own source code repository. Then, **run** your first [DevSecOps CI pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-ci-pipeline). This feature is enabled by default, so no additional setup is required. It dynamically infers the DevSecOps pipeline configuration and scripts that are needed to build, test, and deploy your application or service.

To disable this feature, set a `pipeline-config` value that matches an existing file in your repository.
{: #note}


### Spots with Inferred DevSecOps Pipeline Configuration
{: #devsecops-inferred-pipeline-configuration-how-to-use-spots}

The Inferred DevSecOps Pipeline Configuration feature uses source introspection and language definition to identify `spots` in your source repository. A `spot` is a location in your code that requires a specific action to be performed.

Each spot has the following properties:

| Property | Description |
| -------------- | -------------- |
|  Source | Identifies a location in the source code repository as the context of the spot. |
| Processes | One or more processes that indicate the type of operation to be performed. |
| Tools | An array that is associated to a process that lists the tools to be started to perform the action. Each tool can have its own set of properties. |
| Environment setup | References a script file (or script commands) that will be started to set up the environment for the process operation (tool invocations) to be performed. |
{: caption="Spot Properties" caption-side="bottom"}

### Identified spots
{: #devsecops-inferred-pipeline-configuration-spots}

The Inferred DevSecOps Pipeline Configuration feature currently identifies the following types of `spots`:
`code` spots, `deployment` spots, `acceptance-test` spots, `dynamic-scan` spots and `release`spots.

#### Code spots
{: #devsecops-inferred-pipeline-code-spots}

Code spots are related to a supported source code language, including:

-  [Node.js](https://nodejs.org) (with npm, Yarn or Gradle)
-  [Java](https://www.ibm.com/topics/java) (with Maven or Gradle)
-  [Golang](https://go.dev)
-  [Python](https://www.python.org)
-  [Dockerfile](https://docs.docker.com/reference/dockerfile)
-  [Terraform configuration language](https://developer.hashicorp.com/terraform/language)

The `code` spots handle the following processes:
-  `building`: Defines the tools that perform the build of the given source code.
-  `unit-testing`: Locates the tools that perform unit testing of the build outcome.

#### Deployment spots
{: #devsecops-inferred-pipeline-deployment-spots}

The `deployment` spots locate a deployment vehicle, including deployment resources and tools. `deployment` spots have a deploying process that lists the deployment tools. The deployment vehicles currently supported are:

-  Kubernetes resources definitions like `Pod`, `ReplicaSet`, `ReplicationController`, `Deployment`, `Daemonset`, `StatefulSet`, `Job`, `Cronjob`, `NetworkPolicy`, `Ingress`, `Service`, `Route` - [kubectl](https://kubernetes.io/docs/reference/kubectl) as tool

-  [Websphere Liberty Application Custom Resource](https://www.ibm.com/docs/en/was-liberty/core?topic=resources-webspherelibertyapplication-custom-resource) - [kubectl](https://kubernetes.io/docs/reference/kubectl) as tool

-  [Open Liberty Application Custom Resource](https://openliberty.io/docs/latest/open-liberty-operator.html#_what_is_the_open_liberty_operator) - [kubectl](https://kubernetes.io/docs/reference/kubectl) as tool

-  [Helm chart](https://helm.sh) - helm as tool

-  [Terraform configuration](https://developer.hashicorp.com/terraform/language) - [IBM Cloud Schematics](/docs/schematics) or [Terraform CLI](https://developer.hashicorp.com/terraform/cli) as tool

-  [Wazi DeploymentMethod Custom Resource](https://www.ibm.com/docs/en/developer-for-zos/17.0?topic=files-deployment-method) - [Wazi Deploy](https://www.ibm.com/docs/en/developer-for-zos/17.0?topic=reference-syntax-wazi-deploy-core-commands) as tool

#### Acceptance-test spots
{: #devsecops-inferred-pipeline-accept-spots}

The `acceptance-test` spots locate an acceptance-test suite to run. `acceptance-test` spots have an `acceptance-testing` process that identifies the tools to run the acceptance-test suite.

#### Dynamic-scan spots
{: #devsecops-inferred-pipeline-dynamic-spots}

 The `dynamic-scan` spots identify locations for a dynamic scan. Dynamic-scan spots have a `scanning` process to list the scan tools invoked during dynamic scan.

 The [OWASP ZAP scan](/docs/devsecops?topic=devsecops-cd-devsecops-zap-scans) is the only supported tool to star a sub-webhook trigger.

#### Release spots
{: #devsecops-inferred-pipeline-dynamic-spots}

The `release` spots locate the release process. Release spots have a `releasing` process that lists the tools to run during the release stage.
Currently, the supported tools for the releasing process are:
-  [semantic-release](https://semantic-release.gitbook.io/semantic-release)
-  [maven](https://maven.apache.org/index.html) with [`maven deploy`](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html) phase.

### Sample polyglot-spots.json content
{: #devsecops-pipeline-configuration-ison}

The Inferred DevSecOps Pipeline Configuration feature extracts spots and generates the following JSON content to trigger specific actions and tools during CI pipeline stages.

```JSON
{
  "code": [
    {
      "source": "Dockerfile",
      "language": "Dockerfile",
      "building": {
        "tools": [
          {
            "tool": "docker"
          }
        ]
      }
    },
    {
      "source": "package.json",
      "language": "NodeJS",
      "building": {
        "tools": [
          {
            "tool": "npm"
          }
        ]
      },
      "unit-testing": {
        "tools": [
          {
            "tool": "npm",
            "command": "test"
          }
        ]
      }
    }
  ],
  "acceptance-test": [
    {
      "source": "package.json",
      "acceptance-testing": {
        "tools": [
          {
            "tool": "npm",
            "command": "run acceptance-test"
          }
        ]
      }
    }
  ],
  "deployment": [
    {
      "source": "deployment_iks.yml",
      "deploying": {
        "tools": [
          {
            "tool": "kubectl"
          }
        ],
        "environment-setup": ".env.deploy.sh"
      }
    },
    {
      "source": "deployment_os.yml",
      "deploying": {
        "tools": [
          {
            "tool": "kubectl"
          }
        ],
        "environment-setup": ".env.deploy.sh"
      }
    }
  ],
  "dynamic-scan": [
    {
      "source": "definitions/definitions1.json",
      "scanning": {
        "tools": [
          {
            "tool": "trigger-async-zap",
            "kind": "api"
          }
        ],
        "environment-setup": "scripts/zap/zap-custom-scripts/.env.dynamic-scan.sh"
      }
    },
    {
      "source": "scripts/zap/uiscripts/run.sh",
      "scanning": {
        "tools": [
          {
            "tool": "trigger-async-zap",
            "kind": "ui"
          }
        ],
        "environment-setup": "scripts/zap/zap-custom-scripts/.env.dynamic-scan.sh"
      }
    }
  ],
  "release": []
}
```

## Advanced configuration
{: #devsecops-inferred-pipeline-configuration-advanced-config}

### Files injection
{: #devsecops-inferred-pipeline-configuration-config-file-injection}

The Inferred DevSecOps Pipeline Configuration feature uses the contents of  `polyglot-spots.json` and `.pipeline-config.yaml` files to customize the DevSecOps pipeline process execution.

During the `finish` stage of a CI pipeline, both `polyglot-spots.json` and `.pipeline-config.yaml` (corresponding to the static pipeline configuration of the CI pipeline execution) are added to a branch named `inferred-devsecops` (default) in the application source code repository.

The pipeline configuration with format version `2` (usable with compliance-pipelines branch `v11` for instance) can be also generated if the property `create-inferred-pipeline-configuration-v2` is set to `true` (Default to `false`). An additional pipeline-configuration file like `.pipeline-config-v2.yaml` is then added to branch named `inferred-devsecops` (default) in the application source code repository.

#### Configuring the injection branch
{: #devsecops-pipeline-configuration-file-injection-config-branch}

You can configure the name of the branch for injecting DevSecOps inferred files by using the `inferred-devsecops-branch` pipeline property. The default value is `inferred-devsecops`.

Use the `push-inferred-pipeline-configuration-files` pipeline property (`push-polyglot-files` property is deprecated in favor of `push-inferred-pipeline-configuration-files` property) to enable or disable the creation and update of the `inferred-devsecops` branch:

| Value | Description |
| -------------- | -------------- |
|  `true` (default) | Configuration files are added and pushed to the `inferred-devsecops` branch of the source application source code repository. |
| `false` | Configuration files are not added to the inferred-devsecops branch. |
{: caption="Enable or disable inferred-devsecops branch" caption-side="bottom"}

### Configuring Spots Extraction
{: #devsecops-pipeline-configuration-spot-config}

Configure the extraction of spots by using the following pipeline environment properties:

#### Ignoring Spots
{: #devsecops-pipeline-configuration-spot-ignore}

You can ignore specific spots during extraction by using regular expressions. The following configuration options are available:

* `ignore-code-spot-pattern`: Ignores code spots that match the specified regular expression.
* `ignore-deployment-spot-pattern`: Ignores deployment spots that match the specified regular expression.
* `ignore-dynamic-scan-spot-pattern`: Ignores dynamic-scan spots that match the specified regular expression.
* `ignore-acceptance-test-spot-pattern`: Ignores acceptance-test spots that match the specified regular expression.
* `ignore-release-spot-pattern`: Ignores release spots that match the specified regular expression.

#### Code Engine Configuration
{: #devsecops-pipeline-configuration-spot-cec}

If you are using IBM Cloud Code Engine for deployment, specify the Code Engine project and configure the build process with the following spots:
* `code-engine-project`: Specifies the Code Engine project.
* `code-engine-build-use-native-docker`: (Default: `false`) Indicates whether to use Docker CLI instead of `ibmcloud code-engine buildrun` command.

#### Golang Configuration
{: #devsecops-pipeline-configuration-spot-go}

 To configure the spot extraction process for Golang, use the following spots:

* `go-ignore-main`: (Default: `false`) Indicates whether code spot extraction to not focus on main package and main function detection for the main source argument.
* `go-output`: Specifies the executable output file from the go build command.

#### Gradle Configuration
{: #devsecops-pipeline-configuration-spot-gradle}

 To configure the Gradle tasks for setup, unit testing, build artifact, and acceptance testing, use the following spots:

* `gradle-setup-tasks`: (Default: `assemble`) Comma-separated list of Gradle tasks for setup stage.
* `gradle-unit-testing-tasks`: (Default: `test`) Comma-separated list of Gradle tasks for unit-test stage.
* `gradle-build-artifact-tasks`: (Default: `build`) Comma-separated list of Gradle tasks for build-artifact stage.
* `gradle-acceptance-testing-tasks`: Comma-separated list of Gradle tasks for acceptance-test stage.

#### NPM Configuration
{: #devsecops-pipeline-configuration-spot-NPM}

You can configure the NPM unit testing and acceptance testing script detection.
* `hint-npm-unit-testing-script`: (Default: `test`) Hints for NPM unit testing script detection.
* `hint-npm-acceptance-testing-script`: (Default: `acceptance-test`) Hints for NPM acceptance testing script detection.

#### Python Configuration
{: #devsecops-pipeline-configuration-python}

To configure the Python Poetry version, use the following spots:

* `hint-python-poetry-version`: (Default: `1.8.2`) Hints for Python Poetry version.

#### Terraform Configuration
{: #devsecops-pipeline-configuration-spot-terraform}

To configure the Terraform deployment process, use the following spots:

* `terraform-deployment`: (Default: `false`) Disables Schematics as a deployment vehicle in favor of Terraform and Cloud Object Storage for state storage.

#### Artifact Upload
{: #devsecops-pipeline-configuration-spot-artifact-upload}

To configure the artifact upload process, use the following spots:

* `artifact-upload-to-devsecops-cos`: (Default: `false`) Enables artifact upload to a Cloud Object Storage bucket by using DevSecOps CLI artifact upload for nonimage saved artifacts.


### Environment set up Files
{: #devsecops-pipeline-configuration-hooks-environment-setup-files}

Each source code repository requires specific setup or customization for a given stage. The Inferred DevSecOps Pipeline Configuration feature provides a way to specify an environment-setup property that can be defined as a bash script. This script is sourced before running the corresponding action for a process.

During spots extraction, the feature uses a hint based on the file name to determine the environment setup files. The following files are supported:

| File name | Associated Stage | Description |
| -------------- | -------------- | -------------- |
| `.env.build.sh` | Build | Environment setup for build process. It can be overridden by an environment setup file for a scoped tool (like docker, maven, npm...) such as `.env.docker-build.sh`, `.env.maven-build.sh`, ... |
| `.env.docker-build.sh` | Build (Docker) | Environment setup for build process with Docker source |
| `.env.go-build.sh` | Build (Go) | Environment setup for build process with Go source |
| `.env.maven-build.sh`| Build (Maven)| Environment setup for build process with Maven source |
| `.env.npm-build.sh`	 | Build  (npm) | Environment setup for build process with npm source |
| `.env.yarn-build.sh` | Build (Yarn) | Environment setup for build process with Yarn source |
| `.env.test.sh` | Unit Testing| Environment setup for unit testing process |
| `.env.deploy.sh` | Deployment | Environment setup for deployment process |
| `.env.acceptance-test.sh`	 | Acceptance Testing | Environment setup for acceptance testing process |
| `.env.dynamic-scan.sh` | Dynamic Scanning | Environment setup for dynamic scanning process |
| `.env.release.sh` | Release | Environment setup for release process |
{: caption="Environment Setup Files" caption-side="bottom"}

For an example of how to use this script, see the  [Hello Compliance App](https://us-south.git.cloud.ibm.com/open-toolchain/hello-compliance-app/-/blob/master/.env.deploy.sh?ref_type=heads) repository on IBM Cloud.

### Environment Context Injection
{: #devsecops-pipeline-configuration-environment-context-injection}

The Inferred DevSecOps Pipeline Configuration feature incorporates environment variables from pipeline and trigger properties into various project contexts. The project contexts are as follows:

-  Pipeline execution stages
-  Helm deployments
-  Code Engine deployments

This feature allows you to inject or set context, such as environment variables, from pipeline and trigger properties based on normalized property names.

Some tools handle properties with normalized names that are injected into specific contexts, such as:

-  docker build arguments and/or docker build secrets
-  complementary `values.yaml` files for Helm deployments
-  `configmap` or `secret` for Code Engine deployments

By using normalized property names, you can inject environment variables and other context into your pipeline and deployments.

#### Environment Variables Injection in Pipeline Execution Stages
{: #devsecops-pipeline-configuration-environment-variable-injection}

The Inferred DevSecOps Pipeline Configuration feature provides an `export-properties`  utility to export pipeline and trigger properties as environment variables during stage execution. This utility is invoked in every customized stage:

```bash
export-properties "GLOBAL" && export-properties "${STAGE^^}"
```

##### Global Environment Variables
{: #devsecops-pipeline-configuration-environment-variable-injection-global}

The command `export-properties "GLOBAL"` exports pipeline and trigger properties with normalized names with `ENV_GLOBAL_<XXX>` as environment variables such as `XXX` in every pipeline stage execution context.

###### Example of Global Environment Variable
{: #devsecops-pipeline-configuration-environment-variable-injection-global-ex}

| Property name | Property valuer | Resulting environment variable |
| -------------- | -------------- | -------------- |
| `ENV_GLOBAL_my_var`| 	`my_value` | `my_var=my_value` |
{: caption="Example of Global Environment Variables " caption-side="bottom"}

##### Stage-Specific Environment Variables
{: #devsecops-pipeline-configuration-environment-variable-injection-stage}

The command `export-properties "${STAGE^^}"` will export pipeline or trigger properties relevant for the current executed stage with normalized name `ENV_<stage in upper case>_<XXX>` as environment variables in the given executed stage.

###### Example of Stage-Specific Environment Variables
{: #devsecops-pipeline-configuration-environment-variable-injection-stage-ex}

| Property name | Property valuer | Resulting environment variable |
| -------------- | -------------- | -------------- |
| `ENV_SETUP_CGO_ENABLED`| 	`true` | `CGO_ENABLED=true` |
{: caption="Example of Global Environment Variables " caption-side="bottom"}

In the CI pipeline, the `code-setup - run-stage` step has the `CGO_ENABLED` environment variable set to the proper value.

Refer to the [stage descriptions](/docs/devsecops?topic=devsecops-cd-devsecops-scripts-stages#cd-devsecops-stage-desc) for a list of stages and their descriptions.

A typical use case for this feature is to inject environment variables before running unit tests to provide configuration. In this case, the normalized name of the properties would be `ENV_TEST_<a_var>` with `<a_var>` being the name of the exported environment variable to be available for the test` stage execution.

###### Example
{: #devsecops-pipeline-typical-ex}

| Property name | Property valuer | Resulting environment variable |
| -------------- | -------------- | -------------- |
| `ENV_TEST_MY_VAR`| 	`my_value` | `MY_VAR=my_value` |
{: caption="Example of Stage-Specific Environment Variables " caption-side="bottom"}

Use this feature to simplify your pipeline configuration and improve consistency across your deployments.


#### Tool Execution and Configuration
{: #devsecops-pipeline-configuration-tool-execution}

Some tools in the Inferred DevSecOps Pipeline Configuration feature use pipeline and trigger properties to infer complementary configuration.

##### Docker
{: #devsecops-pipeline-configuration-tool-docker}

* **Build arguments**: The docker build command is completed with --build-arg parameters based on pipeline and trigger properties with a normalized name like `DOCKER_BUILD_ARG_`.
    * Example: Adding a property that is named `DOCKER_BUILD_ARG_my_arg` injects the parameter `--build-arg="my_arg="`into the docker build command.
* Build secrets: The docker build command is completed with --secret parameters based on pipeline and trigger properties with a normalized name like `DOCKER_BUILD_SECRET_`.
    * For example, adding a property that is named DOCKER_BUILD_SECRET_my_secret injects the parameter --secret id=my_secret,env= into the docker build command.

To learn more, refer to [docker build arguments](https://docs.docker.com/build/guide/build-args/) and [docker build secret](https://docs.docker.com/build/building/secrets/)

##### Helm
{: #devsecops-pipeline-configuration-tool-helm}

* **Deploying processing**: Additional values can be injected into the Helm deploy process based on normalized pipeline and trigger properties.
    * If a property has a name like `HELM_VALUE_,` the complementary values file managed by the Helm processing tool adds an entry `a_value_property` with the value of the pipeline or trigger property.
    * The complementary values file is used as an argument of the last `-f | --values` parameter for helm command.

To learn more, refer to  [Complementary values content](https://helm.sh/docs/chart_best_practices/values/#consider-how-users-will-use-your-values).

 ##### Terraform
{: #devsecops-pipeline-configuration-tool-Terraform}

* **Deploying process** The Terraform tool relies on Terraform helper functions provided by [compliance-commons terraform](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-commons/-/blob/master/terraform/terraform-utilities.sh).
    * For more information on configuration properties for context injection, see [Configuring Terraform input variables](/docs/devsecops?topic=devsecops-cd-devsecops-iac-ci-pipeline#devsecops-iac-ci-terra-var).

##### Schematics
{: #devsecops-pipeline-configuration-tool-Schematics}

* **Deploying process**: The Schematics tool relies on Schematics helper functions provided by [compliance-commons schematics](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-commons/-/blob/master/schematics/schematics-utilities.sh).
    * For more information on configuration properties for context injection, refer to [Configuring Schematics workspace declared variables](/docs/devsecops?topic=devsecops-cd-devsecops-iac-ci-pipeline#devsecops-iac-ci-terra-var).

##### Code Engine
{: #devsecops-pipeline-configuration-tool-Code-Engine}

* **Deploying process**: Additional configuration for the application can be created by defining complementary configmap or secret associated with the application.
    * For pipeline and trigger properties with a normalized name such as `CE_ENV_<XXXX>`, an entry in a complementary configmap or secret (associated with the Code Engine application or job) will be created with key `<XXXX>` and its value set based on the value of the corresponding pipeline or trigger property.

To learn more, refer to [code-engine configmap(s) to configure applications or jobs](/docs/codeengine?topic=codeengine-configmap) and [code-engine secret to configure applications or jobs](/docs/codeengine?topic=codeengine-secret)

## DevSecOps common scripts library
{: #devsecops-common-script-library}

Inferred DevSecOps Pipeline Configuration uses scripts/functions in certain stages from the scripts in the common library, which offers a set of reusable scripts that can help you if you want to started with customization.

For more information about the common scripts library, including scripts, tools, usage, and parameters, see [Common scripts library](/docs/devsecops?topic=devsecops-common-scripts).

## FAQ
{: #devsecops-pipeline-configuration-faq}

### Branch protection
{: #devsecops-pipeline-configuration-faq-branch}

#### Enable Branch Protection by Default
{: #devsecops-pipeline-configuration-faq-branch-default}

DevSecOps PR and CI pipelines enable branch protection on the source code repository by default. This verification occurs during the code-setup stage.

#### Disable Branch Protection
{: #devsecops-pipeline-configuration-faq-branch-disable}

To disable branch protection, set the `setup-branch-protection` property to `false`.

#### Customize Branch Protection Status Checks
{: #devsecops-pipeline-configuration-faq-branch-customize}

To customize the [prefix for the branch protection status checks](/docs/devsecops?topic=devsecops-devsecops-config-github#setting-customized-prefix-for-compliance-checks) , set the `branch-protection-status-check-prefix` property. The default prefix is `tekton`.



### Poetry & Private repositories
{: #devsecops-pipeline-configuration-poetry}

#### Configure Poetry for Private Repositories
{: #devsecops-pipeline-configuration-poetry-repo}

When using Poetry (`pyproject.toml` is identified as a code spot) and an alternative source or repository is defined to fetch dependencies, such as:

```toml
[[tool.poetry.source]]
name = "local"
url = "https://na-public.artifactory.swg-devops.com/artifactory/api/pypi/ip-devops-team-pypi-virtual/simple"
secondary = true
```

#### Authenticate with Private Repositories in IBM Cloud
{: #devsecops-pipeline-configuration-poetry-repo-autheticate}

It is required to provide credentials for this `local` source repository. [Poetry documentation on configuration for credentials](https://python-poetry.org/docs/repositories/#configuring-credentials) indicates that the environment variables to provide http user and password should be `POETRY_HTTP_BASIC_LOCAL_USERNAME` and `POETRY_HTTP_BASIC_LOCAL_PASSWORD`.

Use the [environment variable injection](#environment-variables-injection-in-pipeline-execution-stages) feature, and add the following pipeline environment properties:
- `ENV_GLOBAL_POETRY_HTTP_BASIC_LOCAL_USERNAME` (text) with the appropriate value
- `ENV_GLOBAL_POETRY_HTTP_BASIC_LOCAL_PASSWORD` (secured) with the appropriate secured value

### Maven, pom.xml, settings.xml, and Environment Resolution
{: #devsecops-pipeline-configuration-maven-pom-env}


#### Configure Maven for Custom Settings Files in IBM Cloud
{: #devsecops-pipeline-configuration-configure-maven}

If your Maven project defines a specific settings file with a custom file name, such as `ci-settings.xml`, define a pipeline environment property [maven-user-settings-file-path](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-commons/-/blob/master/doc/setup__maven.md#properties---retrieved-using-get_env) with the value set to `ci_settings.xml` at the PR, CI pipeline, or trigger level properties.

In addition, if there is some `env.<VARIABLE>` to be resolved like:

```bash
    <server>
      <username>${env.MAVEN_USERNAME}</username>
      <password>${env.MAVEN_PASSWORD}</password>
      <id>central</id>
    </server>
```
Use the [environment variable injection](#environment-variable-injection-in-pipeline-execution-stages) feature to provide these variables by adding 2 pipeline properties (in the PR and CI pipeline):
- `ENV_GLOBAL_MAVEN_USERNAME` (text) with the value to use for maven username
- `ENV_GLOBAL_MAVEN_PASSWORD` (secured) with the value to use for maven password

### Force Static Linking for Go Builds
{: #devsecops-pipeline-configuration-force-go}

#### Enable Static Linking for Go Builds
{: #devsecops-pipeline-enable-static}

By default, `go build` produces a dynamically linked binary. To use it in a Docker container, enable static linking by setting `CGO_ENABLED=0` during the build.

#### Configure the Environment Variable for Go Build
{: #devsecops-pipeline-enable-configure}

To enable static linking, use the [environment variable injection](#environment-variable-injection-in-pipeline-execution-stages) feature to add the following pipeline environment property in the CI pipeline:

-  `ENV_SETUP_CGO_ENABLED` with the value set to `0`


## Getting support
{: #get-support-devsecops}

As you customize your DevSecOps pipelines, get help directly from the {{site.data.keyword.cloud_notm}} development teams by [joining us on Slack](https://join.slack.com/t/ibm-devops-services/shared_invite/zt-1znyhz8ld-5Gdy~biKLe233Chrvgdzxw).

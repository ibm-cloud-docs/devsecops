---

copyright:
  years: 2024
lastupdated: "2024-11-07"

keywords: DevSecOps

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Inferred DevSecOps Pipeline Configuration 
{: #devsecops-inferred-pipeline-configuration}

After adding your own application / micro-service to the DevSecOps CI toolchain, you're unsure where to start from:
- What should be the content of the `.pipeline-config.yaml` DevSecOps pipeline configuration file?
- Which scripts should be used to build, test and deploy my code?
- What code to put in these scripts?

The Inferred DevSecOps Pipeline Configuration feature eases the adoption of DevSecOps offering by seamlessly inferring DevSecOps pipeline configuration and scripts to self-adapt to most of the applications and provides a quick and easy way to on-board your own micro-service(s) or application(s) to DevSecOps PR/CI/CD pipelines.
{: shortdesc}

**Note:** this feature is part of the DevSecOps offering and does not require any additional configuration.
{: #note}

## Prereqs
- You created your DevSecOps CI/CD/CC toolchains and on-boarded [your application source code repository](https://cloud.ibm.com/docs/devsecops?topic=devsecops-tutorial-ci-toolchain#tutorial-ci-toolchain-application) (not the default sample app repository).
- You reviewed  the [basics of DevSecOps pipeline customization](/docs/devsecops?topic=devsecops-cd-devsecops-basics-pipelines-customization). There, you learned about the different templates that are available, support options, and other important information to get you started with DevSecOps.

## Get started now
You may want to get started immediately and run your first DevSecOps pipeline **without** any further configuration.

You may later read this page to learn more about this feature, how it works, and its advanced usage.

### How it works
The Inferred DevSecOps Pipeline Configuration feature provides default PR/CI/CD DevSecOps configuration and scripts based on:
1) Source code introspection and language definition in the source repository.
2) DevSecOps processes and customization best practices.
3) Default stages to execute code build, artifact build, deployment, release according to some configuration environment/properties.

### Auto configure your DevSecOps pipelines
- If you configured your CI/CD/CC toolchains to use your own source code repository, no further action is required (this feature is enabled by default). 
- Run the PR/CI/CD/CC pipeline.
- The Inferred DevSecOps Pipeline Configuration feature dynamically infers DevSecOps pipeline configuration and associated scripts to build, test and deploy your application / service.

Note: setting a value for the `pipeline-config` parameter at pipeline or trigger level corresponding to an existing file in the source code repository will disable this feature.

## Basic usage
### spots
Source introspection and language definition enables the Inferred DevSecOps Pipeline Configuration feature to identify `spots` in the source repository. Each spot has:
- a `source` property identifying a location in the source code repository as the context of the spot.
- one or more processes that indicate the type of operation to be performed.
- each process has a `tools` property as an array that lists the tools to be invoked to perform the action (each tool can have its own set of properties).
- each process or tool can have an `environment-setup` property to reference a script file (or script commands) that will be invoked to setup the environment for the process operation (tool invocations) to be performed.

## Advanced configuration
### Files injection
The Inferred DevSecOps Pipeline Configuration feature relies on `polyglot-spots.json` and `pipeline-config.yaml` files content to customize the DevSecOps pipeline(s) process(es) execution.

During the finish stage of a ci-pipeline, both `polyglot-spots.json` and `pipeline-config.yaml` (corresponding to the static configuration of the pipeline execution) files are automatically dumped and added to a branch named `inferred-devsecops` (default) in the application source code repository.

The name of the branch for the injection of DevSecOps inferred files can be configured by using the `inferred-devsecops-branch` pipeline property (Default:`inferred-devsecops`).

The creation and update of the `inferred-devsecops` branch can be enabled or disabled using the pipeline property `push-polyglot-files`:
- `true` (default): configuration files will be added and pushed to the `inferred-devsecops` branch of the source application source code repository.
- `false`: configuration files will not be added to the `inferred-devsecops` branch.

### Spots
The Inferred DevSecOps Pipeline Configuration feature currently handles the following `spots`:
   - `code`: spots related to a supported source code language (currently: `NodeJS/npm`, `NodeJS/yarn`, `NodeJS/gradle`, `java/maven`, `java/gradle`, `go`, `Python`, `Docker`, `Terraform configuration language`.
    code spots handle the following processes:
     - `building`: this process defines the tools performing the build of the given source/code spot
     - `unit-testing`: this process defines the tools performing the unit-testing of the building process outcome
   - `deployment`: spots locating a deployment vehicle (deployment resources and tools). Currently supported: `Kubernetes resources/kubectl`, `Kubernetes Liberty Application CRDs/kubectl-liberty-app`, `Helm chart/helm`, `Terraform configuration/schematics|terraform`, `WAZI CRD/DeploymentMethod`.
    deployment spots have a `deploying` process listing the tools used to perform the deployment.
   - `acceptance-test`: spots locating an acceptance-test suite to be executed.
     acceptance-test spots have an `acceptance-testing` process identifying the tools to run the acceptance-test suite.
   - `dynamic-scan`: spots locating a dynamic-scan to be performed.
    dynamic-scan spots have a `scanning` process listing the `tools` to perform the scan. Currently supported is the [owasp-zap scan](https://cloud.ibm.com/docs/devsecops?topic=devsecops-cd-devsecops-zap-scans) tool started in a subwebhook trigger
   - `release`: spots locating release process.
    release spots have a `releasing` process listing the `tools` to be executed during release stage. Currently supported is the `semantic-release` tool.

### Extraction of spots
Configure the extraction of `spots` by using the following pipeline environment properties:
- `ignore-code-spot-pattern`: regular expression to specify ignore code spot during spots extraction.
Example: `^.+deployment_os\.yml$` or `^(?!helm/agra/).+`
- `ignore-deployment-spot-pattern`: regular expression to specify ignore deployment spot during spots extraction.
- `ignore-dynamic-scan-spot-pattern`: regular expression to specify ignore dynamic-scan spot during spots extraction.
- `ignore-acceptance-test-spot-pattern`: regular expression to specify ignore acceptance-test spot during spots extraction.
- `ignore-release-spot-pattern`:  regular expression to specify ignore release spot during spots extraction.
Example: `^.+releaserc-gitlab\.json$`
- `code-engine-project` : specify code-engine-project if using [IBM Cloud Code Engine](https://cloud.ibm.com/docs/codeengine) for deployment. Extraction of deployment spots for IBM Cloud Code Engine will be done using the code spots found.
- `code-engine-build-use-native-docker`: (Default:`false`) indicates that the build process should use Docker CLI and not `ibmcloud code-engine buildrun` command (only valid if code-engine-project has been set)
- `go-ignore-main`: (Default:`false`): indicate that the go code spot extraction should not focus on main package and main function detection for the main source argument.
- `go-output`: executable output file from the `go build` command. 
- `hint-npm-unit-testing-script`: (Default:`test`) hint for npm unit testing script detection. The value should refer to corresponding key in the `scripts` section of the package.json
- `hint-npm-acceptance-testing-script`: (Default: `acceptance-test`) hint for npm acceptance testing script detection. The value should refer to corresponding key in the `scripts` section of the package.json
- `hint-python-poetry-version`: (Default: `1.8.2`) hint for python poetry version
- `terraform-deployment`: (Default: `false`) disable schematics as deployment vehicle in favor of terraform and COS for [state storage](https://cloud.ibm.com/docs/devsecops?topic=devsecops-cd-devsecops-iac-ci-pipeline#devsecops-iac-ci-pipeline-terra-cli)
- `artifact-upload-to-devsecops-cos` : (Default: `false`) enable artifact upload to a COS bucket using DevSecOps CLI [artifact upload](https://cloud.ibm.com/docs/devsecops?topic=devsecops-cd-devsecops-cli#artifact-upload) for non-image saved artifact (using [save_artifact](https://cloud.ibm.com/docs/devsecops?topic=devsecops-devsecops-pipelinectl#save_artifact)) having a corresponding saved file (using [save_file](https://cloud.ibm.com/docs/devsecops?topic=devsecops-devsecops-pipelinectl#save_file)) and an `alias` property.
- `gradle-setup-tasks`: (Default: `assemble`) comma separated list of gradle tasks to be performed during `setup` stage.
- `gradle-unit-testing-tasks`: (Default: `test`). comma separated list of gradle tasks to be performed during `unit-test` stage.
- `gradle-build-artifact-tasks`: (Default: `build`). comma separated list of gradle tasks to be performed during `build-artifact` stage.
- `gradle-acceptance-testing-tasks`: comma separated list of gradle tasks to be performed during `acceptance-test` stage.

### Sample spots.json content
```
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

### Hooks
Each source code repository may require some specific setup/customization for a given stage.

The Inferred DevSecOps Pipeline Configuration feature allows to specify an `environment-setup` property that can be be defined as a bash script that will be sourced before executing corresponding stage for a given process.

During pipeline execution, the feature uses a hint based on the filename to determine the environment setup files:
- `.env.build.sh` file is associated as environment-setup for process build in code spots. It can be overriden by an environment setup file for a scoped tool (like docker, maven, npm...) such as `.env.docker-build.sh`, `.env.maven-build.sh`, ...
  - `.env.docker-build.sh` file is associated as environment-setup for process build and docker source in code spots
  - `.env.maven-build.sh` file is associated as environment-setup for process build and maven/pom.xml source in code spots
  - `.env.npm-build.sh` file is associated as environment-setup for process build and npm tool/package.json source in code spots
  - `.env.yarn-build.sh` file is associated as environment-setup for process build and yarn tool/package.json source in code spots
  - `.env.go-build.sh` file is associated as environment-setup for process build and go.mod/go language source in code spots
- `.env.test.sh` file is associated as environment-setup for process unit-testing in code spots
- `.env.deploy.sh` file is associated as environment-setup for process in deployment spots
- `.env.acceptance-test.sh` file is associated as environment-setup for process in acceptance-test spots
- `.env.dynamic-scan.sh` file is associated as environment-setup for process in dynamic-scan spots
- `.env.release.sh` file is associated as environment-setup for process in release spots

See a sample usage [here](https://us-south.git.cloud.ibm.com/open-toolchain/hello-compliance-app/-/blob/master/.env.deploy.sh?ref_type=heads).

### Environment Context injection
The Inferred DevSecOps Pipeline Configuration feature offers a way to inject/set context (such as environment variables) from pipeline and/or trigger properties's values according to normalized property name.
Some tool(s) also handle properties with normalized name(s) that are injected in a specific context (such as in complementary `values.yaml` file for `helm` deployment or as `configmap` or `secret` for a `code-engine` deployment, ...)

### Environment variables injection in pipeline execution stages
The Inferred DevSecOps Pipeline Configuration feature provides an `export-properties` utility to export (pipeline/trigger) properties as environment variables during a stage execution.
This utility is invoked in every customized stages:
```
export-properties "GLOBAL" && export-properties "${STAGE^^}"
```

- The first command exports pipeline/trigger propertie(s) with normalized name `ENV_GLOBAL_<XXX>` as environment variable(s) like `XXX` in every pipeline's stage(s) execution context.

Example: add a pipeline or trigger property named `ENV_GLOBAL_my_var`. It will be injected as an environment variable `my_var` in every stage(s) of the DevSecOps pipeline execution that are customized by the Inferred DevSecOps Pipeline Configuration feature.


- The second command will export pipeline/trigger propertie(s) relevant for the current executed stage with normalized name `ENV_<stage in upper case>_<XXX>` as environment variable(s) in the given executed stage.
example: dynamic-scan should be ENV_DYNAMIC_SCAN_<a_var>?

Example: the value of a pipeline/trigger property named `ENV_SETUP_CGO_ENABLED` will be injected in an environment variable `CGO_ENABLED` for stage `setup` (note: refer to the [stage descriptions](https://cloud.ibm.com/docs/devsecops?topic=devsecops-cd-devsecops-scripts-stages#cd-devsecops-stage-desc) that lists stages and their descriptions). In the CI pipeline, the `code-setup - run-stage` step will have the `CGO_ENABLED` environment variable set to the proper value.

A typical usage of such property is to inject environment variable(s) before executing unit-test - in order to provide some configuration. In that case, the normalized name of the properties would be `ENV_TEST_<a_var>` with `<a_var>` being the name of the exported environment variable to be available for `test` stage execution. 

### Tool execution/configuration/context injection
Some supported `tools` make use of pipeline/trigger properties name lookup (based on a normalized name) to infer complementary configuration.
#### docker (building processing)
[docker build arguments](https://docs.docker.com/build/guide/build-args/) and [docker build secret](https://docs.docker.com/build/building/secrets/)

`docker build` invoked by the docker tool during build process is completed with `--build-arg` parameter(s) that are injected based on pipeline/trigger propertie(s) with a normalized property name like `DOCKER_BUILD_ARG_<a_docker_build_arg>`.
Example: adding a property named `DOCKER_BUILD_ARG_my_arg`, the `docker build` command will use a parameter `--build-arg="my_arg=<the value of DOCKER_BUILD_ARG_my_arg property>"`

In addition, `docker build` invoked by the docker tool during build process is completed with `--secret` parameter(s) that are injected based on pipeline/trigger propertie(s) with a normalized property name like `DOCKER_BUILD_SECRET_<a_docker_build_secret>`.
Example: adding a property named `DOCKER_BUILD_SECRET_my_secret`, the `docker build` command will have a parameter `--secret id=my_secret,env=<reference to environment variable containing the secret value>`

#### helm (deploying processing)
helm install/upgrade command and [complementary values content](https://helm.sh/docs/chart_best_practices/values/#consider-how-users-will-use-your-values)

During the deploy processing using helm, additional values content can be injected based on normalized pipeline/trigger properties. If a property has a name like `HELM_VALUE_<a_value_property>` then the complementary values file managed by the helm processing tool will add an entry `a_value_property` whith the value of the pipeline/trigger property.
The complementary values file is used as an argument of the last `-f | --values` [parameter](https://helm.sh/docs/chart_template_guide/values_files/).

#### terraform (deploying process)
[terraform input variables](https://developer.hashicorp.com/terraform/language/values/variables#environment-variables) from environment variables.

The terraform `tool` used by the Inferred DevSecOps Pipeline Configuration feature relies on terraform helper functions provided by [compliance-commons terraform](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-commons/-/blob/master/terraform/terraform-utilities.sh).
As such, the same configuration properties for context injection can be used - as described [here](https://cloud.ibm.com/docs/devsecops?topic=devsecops-cd-devsecops-iac-ci-pipeline#devsecops-iac-ci-terra-var).

#### schematics (deploying process)
[schematics workspace declared variables](https://cloud.ibm.com/docs/schematics?topic=schematics-create-tf-config#declare-variable)

The schematics `tool` used by the Inferred DevSecOps Pipeline Configuration feature relies on schematics helper functions provided by [compliance-commons schematics](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-commons/-/blob/master/schematics/schematics-utilities.sh).
As such, the same configuration properties for context injection can be used - as described [here](https://cloud.ibm.com/docs/devsecops?topic=devsecops-cd-devsecops-iac-ci-pipeline#devsecops-iac-ci-terra-var).

#### code-engine (deploying process)
[code-engine configmap(s) to configure applications or jobs](https://cloud.ibm.com/docs/codeengine?topic=codeengine-configmap) and [code-engine secret(s) to configure applications or jobs](https://cloud.ibm.com/docs/codeengine?topic=codeengine-secret)

During the deploy processing using code-engine, additional configuration for the application or job can be created by defining some complementary configmap or secret associated to the application/job.
For pipeline/trigger properties with a normalized name like `CE_ENV_<XXXX>`, an entry in a complementary configmap or secret (associated with the code-engine application or job) will be created with key `<XXXX>` and its value set based on the value of corresponding the pipeline/trigger property.

## The DevSecOps Commons Scripts Library
In DevSecOps, some stages run default scripts if they are not specified in the `.pipeline-config.yaml` DevSecOps pipeline configuration file. These default scripts are part of a commons library, which provides a set of reusable scripts that can be used as a starting point for customization.

Learn more about the [common scripts library](/docs/devsecops?topic=devsecops-common-scripts), scripts, tools, usage and parameters.

## FAQ
### Branch protection
By default, DevSecOps PR and CI pipelines are configured to ensure branch protection is set on the source code repository.
This verification is performed during the code-setup stage. This can be disabled by setting the property `setup-branch-protection` to `false`.

In addition, the [prefix for the branch protection status checks](https://cloud.ibm.com/docs/devsecops?topic=devsecops-devsecops-config-github#setting-customized-prefix-for-compliance-checks) can be set using the property `branch-protection-status-check-prefix` (Default:`tekton`).

### Poetry & Private repositories
When **Poetry** is involved (i.e. `pyproject.toml` is an identified code spot) and an alternative source/repository to fetch dependencies is defined like:
```
[[tool.poetry.source]]
name = "local"
url = "https://na-public.artifactory.swg-devops.com/artifactory/api/pypi/ip-devops-team-pypi-virtual/simple"
secondary = true
```
It is required to provide credentials for this `local` source repository. [Poetry documentation on configuration for credentials](https://python-poetry.org/docs/repositories/#configuring-credentials) indicates that the environment variables to provide http user and password should be `POETRY_HTTP_BASIC_LOCAL_USERNAME` and `POETRY_HTTP_BASIC_LOCAL_PASSWORD`.

Use the [environment variable injection](#environment-variables-injection-in-pipeline-execution-stages) feature, and add the following pipeline environment properties:
- `ENV_GLOBAL_POETRY_HTTP_BASIC_LOCAL_USERNAME` (text) with the appropriate value
- `ENV_GLOBAL_POETRY_HTTP_BASIC_LOCAL_PASSWORD` (secured) with the appropriate secured value

### Maven, pom.xml, settings.xml and env.<VARIABLE> resolution
If the maven project defines a specific settings file with non default file name like `ci-settings.xml`, define a pipeline environment property [maven-user-settings-file-path](./doc/setup_maven.md#properties---retrieved-using-get_env) with value set to `ci_settings.xml` at the PR, CI pipeline or trigger level properties.
In addition, if there is some `env.<VARIABLE>` to be resolved like:
```
    <server>
      <username>${env.MAVEN_USERNAME}</username>
      <password>${env.MAVEN_PASSWORD}</password>
      <id>central</id>
    </server>
```
Use the [environment variable injection](#environment-variable-injection-in-pipeline-execution-stages) feature to provide these variables by adding 2 pipeline properties (in the PR and CI pipeline):
- `ENV_GLOBAL_MAVEN_USERNAME` (text) with the value to use for maven username
- `ENV_GLOBAL_MAVEN_PASSWORD` (secured) with the value to use for maven password

### Go build: binary not found in a Docker container/image
By Default:`go build` is not statically linking the binary so if the go output/binary has to be used in a Docker container/image, it may result in a `not found` error message.
The solution is to force static linking by setting `CGO_ENABLED=0` when `go build` is performed in the setup stage.

To do so, use the [environment variable injection](#environment-variable-injection-in-pipeline-execution-stages) feature by adding the following pipeline environment property in the CI pipeline:
- `ENV_SETUP_CGO_ENABLED` and set the value to `0`

## Getting support
{: #get-support-devsecops}

As you customize your DevSecOps pipelines, get help directly from the {{site.data.keyword.cloud_notm}} development teams by [joining us on Slack](https://join.slack.com/t/ibm-devops-services/shared_invite/zt-1znyhz8ld-5Gdy~biKLe233Chrvgdzxw).

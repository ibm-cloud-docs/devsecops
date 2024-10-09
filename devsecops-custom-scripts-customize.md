---

copyright:
  years: 2024
lastupdated: "2024-05-24"

keywords: DevSecOps, inventory model, inventory, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Customizing pipelines by using custom scripts
{: #cd-devsecops-pipelines-custom-customize}

Custom scripts are customizations added in the pipeline.  Adopters, teams, and users provide scripts to run custom tasks to ensure continuous integration and continuous deployment of strategies.
{: shortdesc}

Custom scripts control the pipeline stages. You can use a configuration file (`pipeline-config.yaml`) to configure the behavior of stages, script content, and the base image that runs the scripts. The scripts and configuration for pipeline stages are loaded from a Git repository (repo) that can either be the application (app) repo (similar to `.travis.yml` or `Jenkinsfile`) or a custom repo.

When any of the custom scripts are started, the complete URL of the custom script file, including the file name and the commit hash, is printed at the beginning of the pipeline logs as follows: `The custom script can be viewed using the following link: 'https://<source repo url>/<organization name>/<repository name>/blob/<commit hash>/.pipeline-config.yaml'`. This positioning improves traceability.

For more information, refer [Customizing DevSecOps pipelines for beginners](/docs/devsecops?topic=devsecops-cd-devsecops-basics-pipelines-customization).

## Configuration in `pipeline-config.yaml`
{: #cd-devsecops-scripts-config}

 Use a `.pipeline-config.yaml` configuration file to extend pipeline behavior.

## File location
{: #cd-devsecops-scripts-location}

For pull request and continuous integration pipelines, store the `.pipeline-config.yaml` configuration file in an app repository in the same manner as the `.travis.yml`, or `Jenkinsfile` files. For continuous deployment pipelines, store this file in a dedicated repository.

For all pipelines, you can customize the location and source of the `.pipeline-config.yaml` file with the following pipeline UI parameters:

* `pipeline-config` to set the path of the configuration file. The default value is `.pipeline-config.yaml`.
* `pipeline-config-repo` to set the repository to pull the configuration and scripts from. The default value is the continuous integration app repository.
* `pipeline-config-branch` to use as the branch for the configuration in the config repository. The default value is the continuous integration app repository branch, and the `master` branch in continuous deployment.

## Configuration parameters
{: #cd-devsecops-scripts-configparm}

The configuration in the `.pipeline-config.yaml` file supports the following parameters:

These settings are not pipeline parameters, they must be part of your `.pipeline-config.yaml` file.
{: important}

* `image`: You can use any Docker image that is accessible by the pipeline worker as a base image for the task.

* `script`: The script to run in the stage. Because this field is used as a script file, ensure that the contents act like a script file in the base image that you provided. You can include other scripts next to this configuration file, and refer to them from this entry point. For example:

```bash
test:
  image: ibmcom/pipeline-base-image:2.7
  script: |
    #!/bin/sh
    scripts/lint.sh
    scripts/unit-test.sh
```

* `dind`: Specify whether to enable `docker-in-docker` for the script context. The default setting is `false`.

* `abort_on_failure`: By default, the pipeline stops when the script fails. Setting this to `false` marks the stage in a warning (amber state) (it passed with warning), allows the pipeline to continue, and the failed job is referenced in the evidence.

* `image_pull_policy`: Set the pod `ImagePullPolicy` setting that is provided in the image for the base image. Possible values are the same as valid values for Kubernetes: `Always` or `IfNotPresent`. The default value is `IfNotPresent`.

* `configmap`: Pull key-value pairs from a provided configmap that is accessible by `PipelineRun`. The following values are supported:

    * `config-name`: The direct configmap name syntax. The stage runner tries to access and mount the configmap named `config-name`.
    * `$prop-name`: The indirect configmap name syntax. The configmap name is looked up from the environment-properties configmap, which contains every environment value that is set on the pipeline UI. For example, if there is a prop `my-config` entry in the environment properties, then `my-config` is used for `$prop`.

* `secret`: Pull key-value pairs from a provided configmap that is accessible by `PipelineRun`. The following values are supported:

    * `secret-name`: The direct Secret name syntax. The stage runner tries to access and mount the Secret named `secret-name`.

    * `$prop-name`: The indirect Secret name syntax. The Secret name is located in the environment-properties configmap that contains every environment value that is set on the pipeline UI. For example, if the environment-properties configmap contains the `my-secret` entry property, `my-secret` is used for `$prop`.

* `runAfter`: Run the current stage after the stage that specified in this property completes. Set the value with the stage name that you used in `.pipeline-config.yaml`. Use this property sparingly and confirm that the stage that is specified by this property exists in the pipeline. If the stage does not exist, the pipeline can go into a deadlock.

* `skip`: If set to `true`, bypass the current stage, if possible, in v10 pipelines. Not all stages can be bypassed. The following table lists the stages that can be skipped during the pipeline run.

| Pipeline                | Stages |
|-------------------------|--------|
| PR pipeline             |`code-unit-tests`, `code-compliance-tests`, and `code-pr-finish`|
| CI pipeline             |`code-unit-tests`, `code-static-scan`, `code-compliance-checks`, `build-scan-artifact`, `code-dynamic-scan`, `deploy-acceptance-tests`, `deploy-release`, and `code-ci-finish`|
| CD pipeline             |`prod-verify-artifact`, `prod-acceptance-tests`, and `prod-finish`|
| CC pipeline             |`cc-static-scan`, `cc-dynamic-scan`, `cc-compliance-checks`, `cc-scan-artifact`, and `cc-finish`|
| App-preview PR pipeline |`code-unit-tests`, `code-static-scan`, `code-compliance-checks`, `build-scan-artifact`, `deploy-acceptance-tests`, and `app-preview-pr-finish` |
| Dev-mode CI pipeline    |`code-unit-tests`, `code-static-scan`, `deploy-release`, and `code-ci-finish`|
| Dev-mode CD pipeline    |`prod-acceptance-tests` and `prod-finish`|
{: caption="Stages that can be skipped in pipeline runs" caption-side="top"}

### Example configuration
{: #cd-devsecops-scripts-sample-config}

```bash
version: '1' # fixed, this value is used to track schema changes

# `setup` runs right after the app repo is cloned
setup:

  # the pipeline will break in case setup fails (default is true)
  abort_on_failure: true

  # any docker image can be used which is accessible by the private worker
  image: ibmcom/pipeline-base-image:2.7

  # you can reference configmaps, each key in the configmap is going to be available as `/config/{key}`
  configmap: my-config

    # `$prop` is the indirect config map syntax, the concrete configmap is looked
    # up from the `environment-properties` configmap
    #
    # Eg. if there's a `prop: my-config` entry in the environment properties,
    # then `my-config` is going to be used for `$prop`
  configmap: $prop

  # the mechanism described works for secrets as well!
  secret: $my-secrets

  # the script is executed inside the checked out app repo
  script: |
    #!/bin/sh
    ...

# `test` runs after `setup`, but before building the docker image
test:
  image: ibmcom/pipeline-base-image:2.7
  script: |
    #!/bin/sh
    ...

# `static-scan` runs after `test`, but before building the docker image
static-scan:
  image: ibmcom/pipeline-base-image:2.12
  script: |
    #!/bin/sh
    ...


# `deploy` runs after building the docker image
deploy:
  image: ibmcom/pipeline-base-image:2.7

  # the script has access to the built docker image, which is available at `/config/image`
  script: |
    #!/bin/sh

    cat /config/image

# `dynamic-scan` runs after `deploy`, but before the acceptance test run
dynamic-scan:
  image: ibmcom/pipeline-base-image:2.12
  script: |
    #!/bin/sh
    ...

# `acceptance-test` runs after `deploy`
acceptance-test:
  image: ibmcom/pipeline-base-image:2.7
  script: |
    #!/bin/sh
    ...
```

To view the `.pipeline-config.yaml` configuration file that is used by default for the example app in the reference pipeline template, see [configuration for pull requests and continuous integration](https://us-south.git.cloud.ibm.com/open-toolchain/hello-compliance-app/-/blob/master/.pipeline-config.yaml){: external} and [configuration for continuous deployment](https://us-south.git.cloud.ibm.com/open-toolchain/hello-compliance-deployment/-/blob/master/.pipeline-config.yaml){: external}.

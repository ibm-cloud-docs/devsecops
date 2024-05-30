---

copyright:
  years: 2021, 2023
lastupdated: "2024-05-28"

keywords: DevSecOps

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Using OnePipeline Preprocessor
{: #cd-devsecops-onepipeline-preprocessor}

[OnePipeline]{: tag-blue} [Internal]{: tag-red}

The preprocessor is an extension of [OnePipeline](https://github.com/ibm-cloud-docs/devsecops/blob/master/devsecops-pipelines.md). It is designed to preprocess OnePipeline before a `pipelineRun` starts and dynamically generate Tekton pipeline.

## Preprocessor features:
{: #cd-devsecops-onepipeline-preprocessor-features}

* Enables configuration driven CI/CD
* Dynamically compose Tekton pipelines based on configuration in the `.pipeline-config.yaml`
* Define and execute multiple instances of stages in parallel, for example, `build-artifact`
* Skip a stage from a pipeline and exit with `success` or `failure` instead of an ambiguous state of `completed`
* Change the execution order of the stages using `runAfter`
* Choose the runtime configuration (CPU and memory) of a `pod` executing the stage using `runtimeClassName`
* Add a new stage in the pipeline without worrying about the Tekton specifications
* Avoid do-nothing-sidecar when not required
* Allows execution of more parallel stages

 For example, the CI pipeline has `code-compliance-checks`, `code-unit-tests`, `code-detect-secrets`, and `code-peer-review` running in parallel before `build-artifact` and `code-static-scan` are initiated. With the preprocessor, all six stages are executed simultaneously resulting in overall pipeline execution time improvement

## Benefits
{: #cd-devsecops-onepipeline-preprocessor-benefits}

The preprocessor was designed and built to improve the user experience and additionally provides common benefits.

### Flexible and Extensible OnePipeline
{: #cd-devsecops-onepipeline-preprocessor-flexible}

The preprocessor allows us to consider the pipeline as a template to avoid duplication and reduce complexity. It is now possible to create a generic pipeline which can be used for multiple projects/use cases.

### Optimal Resource Utilization
{: #cd-devsecops-onepipeline-preprocessor-opu}

The dynamic nature of the preprocessor positions us to reduce resource utilization by avoiding sidecar creations when not required and by avoiding dummy/no-op stages. Also, no extra runtime resources are required to process and relay the runtime configuration from one stage to the next.

For example, no sidecar created when `dind` is set to `false` in the `.pipeline-config.yaml`:

![Preprocessor image](images/dind.png?raw=true)


### Improved Performance
{: #cd-devsecops-onepipeline-preprocessor-improved-performance}

The preprocessor removes the limitation of running only four stages in parallel in the CI pipeline. As long as the stages are independent of each other, they can be executed in parallel. This in turn results in overall pipeline execution time improvement.

Additionally, a long-running task can be split into [multiple parallel stages](#multiple-parallel-instances-of-the-same-stage).

![Preprocessor image](images/ci-pipeline.png?raw=true)

The CI pipeline benefits from running more stages in parallel with the preprocessor by reduction in the depth of the tree and execution time improvement.

![Preprocessor image](images/ci-pipeline-with-preprocessor.png?raw=true)

## Enabling the OnePipeline Preprocessor
{: #cd-devsecops-onepipeline-preprocessor-enable}

### Prerequisites
{: #cd-devsecops-onepipeline-preprocessor-prereq}

When using IBM managed workers or taas-managed-workers no prerequisites are required.

When using private worker ensure that the following prerequisites are in place:

The worker nodes that are used in the pipelines need to be at Tekton v 0.56 or later and worker agent 0.17.3 or later

* Update the definitions section of the toolchain to include `v10-preprocessor` branch from the [compliance-pipelines](https://github.ibm.com/one-pipeline/compliance-pipelines/tree/v10-preprocessor)
* Update the `.pipeline-config.yaml` to include the following one line specification:

```
preprocessor_version: latest
```

Please see [Pipeline Pro Tips Blog - Optimize your pipelines with the preprocessor](https://w3.ibm.com/w3publisher/secure-pipelines-service/pipeline-pro-tips-blog/22eaecd0-de52-11ee-a726-b3cf4ef83545) for further instructions



## Confirm the Preprocessor is Enabled
{: #cd-devsecops-onepipeline-preprocessor-confirm}

There are two ways to confirm that the `pipelineRun` has the preprocessor on:

* The `prepare-next-stage` step is removed from every stage

![Preprocessor image](images/preprocessor-enabled-1.png?raw=true)

* The `Agent Log` section in the `Show Context` tab of the toolchain has a log whether the preprocessor is enabled or not:

![Preprocessor image](images/preprocessor-enabled-2.png?raw=true)


## Preprocessor Feature Implementation
{: #cd-devsecops-onepipeline-preprocessor-pfi}

The preprocessor dynamically generates Tekton pipelines based on configuration in the `.pipeline-config.yaml` and supports a variety of options.

### Multiple Parallel Instances of the Same Stage
{: #cd-devsecops-onepipeline-preprocessor-multiple-parallel}

The preprocessor introduced a capability to execute multiple instances of the same stage using unique `image`, `script`, and other configuration for each instance. It implements [Tekton Matrix](https://github.com/tektoncd/pipeline/blob/main/docs/matrix.md) to fan out a `task` in a `pipeline`. This feature can be enabled by specifying a list of configuration for any stage:

```yaml
containerize:
  - dind: true
    image: icr.io/continuous-delivery/pipeline/pipeline-base-image:2.38
    script: |
      #!/usr/bin/env bash

      source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/build_setup_1.sh
      source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/build_1.sh

  - dind: true
    image: icr.io/continuous-delivery/pipeline/pipeline-base-image:2.38
    script: |
      #!/usr/bin/env bash

      source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/build_setup_2.sh
      source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/build_2.sh
```

This configuration in the `containerize` section of the `.pipeline-config.yaml` creates two instances of the `build-artifact` stage in the CI pipeline. Both `build-artifact` instances run in parallel.

The default maximum count of instances for a given stage is 256. A `.pipeline-config.yaml` with more than the maximum count of instances results in a validation error and the `pipeline` is not executed. It is advised to limit running multiple parallel instances of the same stage to as low as possible. This configuration can potentially create a bottleneck accessing the common `workspace` and as a result can cause failures in the `pipelineRun`. { :note}


The preprocessor evaluates the list of configuration specified in the `.pipeline-config.yaml` and dynamically generates Tekton pipelines `matrix` specifications:

```yaml
      - name: build-artifact
        params:
          - name: stage
            value: containerize
        matrix:
          include:
            - name: matrix instance - 0
              params:
                - name: script
                  value: script-0
                - name: dind
                  value: 'true'
                - name: image
                  value: icr.io/continuous-delivery/pipeline/pipeline-base-image:2.38
            - name: matrix instance - 1
              params:
                - name: dind
                  value: 'true'
                - name: image
                  value: icr.io/continuous-delivery/pipeline/pipeline-base-image:2.38
                - name: script
                  value: script-1
```

The dashboard shows two instances of `build-artifact`:

![Preprocessor image](images/multiple-build-artifact.png?raw=true)

It is possible to choose different values of `image`, `script`, etc. except `dind` and `skip`. Choosing different values of `dind` and `skip` are not applicable in this context. They both are task/stage specific configuration and the runtime configuration has to be consistent for all instances of the stage.

This feature can also be useful to split any long-running stage into multiple independent running stages in parallel. For example, `code-compliance-checks` runs multiple scans in a single 1PL stage. This feature can be leveraged to execute multiple instances of the same stage but with individual scans like (`cra`, `checkov`, `tfsec`, `mend`). Currently, these scans run sequentially as part of a single stage causing longer running time for the compliance checks.


### Skip One or More Stages
{: #cd-devsecops-onepipeline-preprocessor-skip-one}


The implementation of skipping a stage in OnePipeline is improved with the preprocessor. The preprocessor follows the same syntax of skipping a stage with `skip` set to `true` in the `.pipeline-config.yaml`. As opposed to OnePipeline, skipping a stage with the preprocessor neither depends on the `whenExpressions` in the pipeline definition nor requires any other configuration including (`image` and `script`).

The preprocessor removes the stage from the pipeline when opted to skip it. It also removes any references of the skipped stage from the pipeline such that it results in a clean and concise pipeline. The `pipelineRun` status is now either `succeeded` or `failed` instead of returning an intermediate state of `completed`.

The dashboard does not show the stages that were opted to skip. For example, the following configuration in the `.pipeline-config.yaml` results in the pipeline without `code-dynamic-scan`:

```yaml
  dynamic-scan:
    skip: true
```

![Preprocessor image](images/skip-comparison.png?raw=true)

Also with this functionality, the CI pipeline exits with `succeeded` even when multiple stages were skipped in the [.pipeline-config.yaml](samples/03-ci-pipeline-skip-stages.yaml).

![Preprocessor image](images/skip-2.png?raw=true)

### Change an Execution Order
{: #cd-devsecops-onepipeline-preprocessor-change-execution}

It is sometimes necessary to change the default execution order of the pipeline based on the use case. For example, the CI pipeline is designed such that `code-compliance-checks` and `code-unit-tests` run in parallel. It is possible to change this order such that compliance checks starts after the unit testing is done using `runAfter`:

```yaml
compliance-checks:
  runAfter: test
```

With the preprocessor, `build-artifact` and `code-unit-tests` run in parallel. To change this order to initiate containerization after the unit testing, specify the stage name in the `runAfter` under `containerize` in the `.pipeline-config.yaml`:

```yaml
containerize:
  runAfter: test
```

The preprocessor changes the pipeline definition before deploying it to the cluster such that the `runAfter` clause of `build-artifact` is updated:

```yaml
      - name: build-artifact
        runAfter:
          - code-ci-start
          - code-setup
          - code-unit-tests
```

### Runtime Configuration of a Stage
{: #cd-devsecops-onepipeline-preprocessor-runtime-config}

When a pipeline is executed using the IBM Managed/TaaS/SPS Pipeline Worker Pool (PWP), a VM with a specific default memory is allocated. A [feature](https://github.ibm.com/cloud-docs/ContinuousDelivery/blob/source/tekton_deploy_var.md#managed-worker-virtual-machine-vm-sizing) was introduced to choose less or extra memory respectively for lighter execution or for intensive tasks through `runtimeClassName` as a label in task definition or an environment property `TASK_RUNTIME_CLASS` in a toolchain.

The preprocessor has further simplified the specification such that the users can choose and specify the runtime profile for each stage in the `.pipeline-config.yaml`. To indicate which VM profile to apply to a specific task within a Tekton pipeline, add the `runtimeClassName` to the stage with one of the following VM values. If none is provided, the default VM profile is used.

```yaml
tiny: 128Mi
small: 2Gi
medium: 4Gi (default)
large: 8Gi
```

For example:

```yaml
setup:
  runtimeClassName: tiny

containerize:
  runtimeClassName: large
```

### Add One or More New Stages in the Pipeline
{: #cd-devsecops-onepipeline-preprocessor-add-one}

It is now possible to introduce one or more new stages in OnePipeline if needed. The specifications to introduce a new stage are simplified with the preprocessor.

1. Update the pipeline definition to include a new stage. Determine the position where you want to add the new stage in the pipeline. For example, the following specification introduces `custom-stage` after `code-ci-start` and `code-setup` are executed.

```yaml
apiVersion: tekton.dev/v1beta1
kind: Pipeline
metadata:
  name: ci-pipeline
spec:
  # params and workspaces specification
  tasks:
    # task specifications
    - name: custom-stage
      taskRef:
        name: run-stage
      runAfter:
        - code-ci-start
        - code-setup
      workspaces:
        - name: app
          workspace: artifacts
      params:
        - name: stage
          value: custom-stage
        - name: image
          value: ""
        - name: script
          value: ""
        - name: configmap
          value: ""
        - name: secret
          value: ""
        - name: abort-on-failure
          value: ""
        - name: dind
          value: ""
        - name: image-pull-policy
          value: ""
        - name: run-after
          value: ""
        - name: compliance-baseimage
          value: $(params.compliance-baseimage)
        - name: pipeline-debug
          value: $(params.pipeline-debug)
        - name: pipeline-name
          value: $(params.pipeline-name)
        - name: dind-image
          value: $(params.dind-image)
```

**Note:** No need to initialize `params` to a result of a parent task e.g. `$(tasks.code-pr-start.results.image)`, `$(tasks.codesetup.results.image[0])`, `$(tasks.code-setup.results.image[2])`, etc.

1. Add the configuration for this new stage in the `.pipeline-config.yaml`:

```yaml
custom-stage:
  image: icr.io/continuous-delivery/toolchains/devsecops/csso-image-sign:8.0.0@sha256:4fa72947d3b97c029b035f5c0b458184808f294a417b847b4935015d3c0744d3
  script: |
    #!/usr/bin/env bash
    if [[ "$PIPELINE_DEBUG" == 1 ]]; then
      trap env EXIT
      env
      set -x
    fi
    cd "$WORKSPACE/$(load_repo app-repo path)" || exit 1
    source $WORKSPACE/$ONE_PIPELINE_CONFIG_DIRECTORY_NAME/scripts/run-custom-stage.sh
```

The preprocessor initializes the `params` of this new stage before a run is initiated:

![Preprocessor image](images/add-new-stage.png?raw=true)

The preprocessor also supports an additional requirement of introducing a new `param` in the newly introduced stage if needed. For example, a new param `custom-param` is introduced such that the `custom-stage` can utilize that further in the processing.

* The pipeline definition

```yaml
apiVersion: tekton.dev/v1beta1
kind: Pipeline
metadata:
  name: ci-pipeline
spec:
  # params and workspaces specification
  tasks:
    # task specifications
    - name: custom-stage
      taskRef:
        name: run-stage
      runAfter:
        - code-ci-start
        - code-setup
      workspaces:
        - name: app
          workspace: artifacts
      params:
        - name: stage
          value: custom-stage
        - name: custom-param
          value: ""
```

* Initialize `custom-param` in the `.pipeline-config.yaml`

```yaml
custom-stage:
  custom-param: Hello World!
  image: icr.io/continuous-delivery/pipeline/pipeline-base-ubi:3.25@sha256:dd2000b525336acd00e9b95b74f15696797764725adbb25fa720e9431d558cbf
```
* The toolchain reflects this new `param`

![Preprocessor image](images/add-new-param-in-custom-stage.png?raw=true)


## Parameters in `.pipeline-config.yaml` and the Defaults
{: #cd-devsecops-onepipeline-preprocessor-parameters-one}

Here is the list of parameters that can be present in the `.pipeline-config.yaml` with their default values.

The default values are first read for each stage based on the configuration in [compliance-pipelines/defaults/defaults.yaml](https://github.ibm.com/one-pipeline/compliance-pipelines/blob/v10-preprocessor/defaults/defaults.yaml). These defaults take the highest precedence. If the defaults are missing from the compliance pipelines, the following default values are assigned to the parameters of each stage in the pipeline.

| Parameter Name    | Default Value                               | Required |
|-------------------|---------------------------------------------|----------|
| image             | registry.access.redhat.com/ubi8/ubi-minimal | Yes      |
| script            | None                                        | Yes      |
| dind              | false                                       | No       |
| abort_on_failure  | stopAndFail                                 | No       |
| image_pull_policy | IfNotPresent                                | No       |
| configmap         | not-provided-configmap                      | No       |
| secret            | not-provided-secret                         | No       |
| skip              | false                                       | No       |
| runAfter          | null                                        | No       |
{: caption="Table 2. Default Value assigned" caption-side="bottom"}

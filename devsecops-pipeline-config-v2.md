---

copyright:
  years: 2023, 2024
lastupdated: "2024-11-29"

keywords: DevSecOps, pipeline config, pipeline config v2

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Pipeline Configuration v2
{: #devsecops-pipeline-config-v2}

Pipeline Configuration v2 provides a flexible and modular approach to customizing Tekton Pipelines without modifying the original definitions. This approach is based on overlays, which are sets of customizations applied on top of the base pipelines. Overlays can modify, add, or remove stages from the pipeline. You can define these overlays in a `.pipeline-config.yaml` file, which works in conjunction with next-generation pipelines. For usage instructions, refer to [Nextgen pipelines](docs/devsecops?topic=devsecops-evsecops-nextgen).

Sample pipeline configuration:
![Sample](images/specifications.png "Sample pipeline configuration"){: caption="Sample of pipeline configuration" caption-side="bottom"}

The `preprocessor` tool applies overlays defined in `.pipeline-config.yaml` to the One Pipeline.
{: shortdesc}

## Specifications
{: #devsecops-pipeline-config-v2-specifications}

The `pipeline-config.yaml` for Pipeline Configuration v2 includes support for the following fields:

* Required:
    * version - Indicates the configuration version. Set this to 2 to signify Pipeline Configuration v2.
* Optional:
    * tasks - Specifies the execution details for the tasks that comprise the pipeline.
    * finally - Specifies the execution details for the finally that comprise the pipeline.

Example:

```yaml
version: 2
tasks:
  code-build:
    ...
  deploy-checks:
    ...
finally:
  ci-finish:
    ...
```

### Task
{: #devsecops-pipeline-config-v2-task}

A task can include the following optional fields, which align with Tekton terminology and follow camel case naming conventions:

#### Task Properties
{: #devsecops-pipeline-config-v2-task-prop}

| Property | Description | Type |
| -------------- | -------------- | -------------- |
| `steps` | Configures one or more steps to execute within the task. If not provided, the preprocessor uses the default configuration. | List of strings |
| `runAfter` | Specifies that the task should execute after one or more other tasks. | List of strings |
| `displayName` | Allows you to specify a user-friendly name for the task, which is used for display and differentiation in the dashboard. | String |
| `runtimeClassName` | Determines the VM profile to apply to the task. | String |
| `when` | Sets a condition in CEL (Common Expression Language) that must be met for the task to execute. | CEL expression |
| `from` | Refers to an existing task definition, enabling the task to fan out into multiple stages. | String |
| `timeout` | Sets a timeout for the task before it fails. | Duration (e.g., 1h30m, 1h, 1m, 60s) |
| `include` | Enables the task to include complex specifications, such as a sidecar to run docker-in-docker or a podman. The specified value must correspond to an existing task within the compliance pipelines.| List of strings |
{: caption="Task Properties" caption-side="bottom"}

#### Inherited Fields
{: #devsecops-pipeline-config-v2-task-inherited}

The following fields are inherited from Tekton Pipelines and IBM Continuous Delivery Pipeline:

| Tekton Pipelines API | CD Pipeline Configuration |
| -------------- | -------------- |
| steps | runtimeClassName |
| runAfter | from |
| displayName | include |
| when |  |
| timeout |  |
{: caption="Inherited Fields" caption-side="bottom"}

The IBM Continuous Delivery Pipeline extends Tekton Pipelines with additional fields to support more advanced pipeline configurations.
{: #note}

#### Task Specifications
{: #devsecops-pipeline-config-v2-task-specs}

When a pipeline is triggered, the preprocessor applies the task configurations specified in the `.pipeline-config.yaml` file to the tasks in the pipeline. The preprocessor then transforms the triggered pipeline to incorporate these configurations, generating a new pipeline that runs the workloads.

The resulting pipelineRun, pipeline, and all referenced tasks are annotated with the following metadata:

```yaml
annotations:
    devops.cloud.ibm.com/preprocessor: enabled
```
If a task is specified in the `.pipeline-config.yaml` file but does not exist in the triggered pipeline, it is ignored. This allows the same `.pipeline-config.yaml` file to be used across multiple pipelines.
{: #important}

##### runAfter
{: #devsecops-pipeline-config-v2-task-runafter}

The `runAfter` property is an optional component of the pipeline specification that allows you to specify the tasks that must complete before a specific task can start. In this section, we will cover the syntax and usage of runAfter in Pipeline Configuration v2.

###### Syntax
{: #devsecops-pipeline-config-v2-task-runafter-syntax}

The basic syntax of using `runAfter` in `.pipeline-config.yaml` is as follows:

```yaml
tasks:
  <task>:
    runAfter:
      - <parent-task>
      - <parent-task>
```

###### Example Usage
{: #devsecops-pipeline-config-v2-task-example}

To configure `.pipeline-config.yaml` to run `deploy-checks` after code-checks, apply the following specification:


```yaml
tasks:
  deploy-checks:
    runAfter:
      - code-checks
```

The original `ci-pipeline` has code-build in the runAfter configuration of `deploy-checks`. The processed `ci-pipeline` will be updated to include both `code-checks` and `code-build` in the runAfter configuration.

Snippet of the original `ci-pipeline`:

```yaml
kind: Pipeline
apiVersion: tekton.dev/v1beta1
metadata:
  name: ci-pipeline
spec:
  tasks:
    - name: deploy-checks
      taskRef:
        name: task-deploy-checks
      runAfter:
        - code-build
```

Snippet of the generated `ci-pipeline`:

```yaml
kind: Pipeline
apiVersion: tekton.dev/v1beta1
metadata:
  name: ci-pipeline
spec:
  tasks:
    - name: deploy-checks
      taskRef:
        name: task-deploy-checks
      runAfter:
        - code-build
        - code-checks
```

If you fail to follow the valid syntax, the following error will be produced when a string is specified instead of a list of strings:

Usage:

```yaml
tasks:
  deploy-checks:
    runAfter: code-checks
```

```yaml
Error:

[User Error] Type Error: 'yaml: unmarshal errors: line 3: cannot unmarshal !!str `code-checks` into []string'

Error parsing the pipeline config file into Pipeline Configuration v2 structure. Correct structure should look like:

version: 2
tasks:
  <task-name>:
    steps:
      - name: <step-name>
        image: <image>
```

This error indicates the line number where the issue occurs and specifies that code-checks is a string instead of a list of strings.

A valid usage is as follows:

```yaml
tasks:
  deploy-checks:
    runAfter:
      - code-checks
```

##### displayName
{: #devsecops-pipeline-config-v2-displayname}

displayName is optional and accepts a string.

Syntax:

The basic syntax of using `displayName` in `.pipeline-config.yaml` is:

```yaml
tasks:
  <task>:
    displayName: <name>
```

`displayName` provides an option to specify a human-readable name on the dashboard, bypassing k8s naming constraints for the tasks. If the `displayName` is provided, the pipelineTask specification in the pipeline will be updated to incorporate the `displayName` field.

`.pipeline-config.yaml`:

```yaml
tasks:
  code-build:
    displayName: Code Build
```

Snippet of the original `ci-pipeline`:

```yaml
kind: Pipeline
apiVersion: tekton.dev/v1beta1
metadata:
  name: ci-pipeline
spec:
  tasks:
    - name: code-build
      taskRef:
        name: task-code-build
```

Snippet of the generated `ci-pipeline`:

```yaml
kind: Pipeline
apiVersion: tekton.dev/v1beta1
metadata:
  name: ci-pipeline
spec:
  tasks:
    - name: code-build
      displayName: "Code Build"
      taskRef:
        name: task-code-build
```

##### runtimeClassName
{: #devsecops-pipeline-config-v2-runtimeclassname}

runtimeClassName is optional and accepts a string.

Syntax:

The basic syntax of using runtimeClassName in `.pipeline-config.yaml` is:

```yaml
tasks:
  <task>:
    runtimeClassName: <profile>
```

runtimeClassName is a string to indicate the runtime profiles. When a pipeline is executed on the IBM Managed, TaaS, or standard worker pool, a VM with a predefined default configuration is allocated. The runtimeClassName can be set to any available VM profile listed in the Profiles section below. If not specified, the default is set to medium.

For example, if you would like to configure `.pipeline-config.yaml` to run code-build on large VM, apply the following specification.

`.pipeline-config.yaml`:

```yaml
tasks:
  code-build:
    runtimeClassName: large
```

You can obtain the list of supported `runtimeClassName` from [here](docs/ContinuousDelivery?topic=ContinuousDelivery-tekton_environment#tekton_tshirt_sizing).

The runtime environment of your tasks can be easily found in the logs downloaded from the toolchains. The `runtimeClassName` is part of the pipelineRun resource, located at pipelineRun.spec.taskRunSpecs.

Validation:

If you fail to follow the valid syntax, the following error will be produced when an invalid value is specified:

Usage:

```yaml
 tasks:
   deploy-checks:
     runtimeClassName: foo
```

```yaml
Error:

[User Error] the value specified for runtimeClassName 'foo' is not valid for task 'deploy-checks'.

It must be one of the following: ("small", "medium", "large", "x86-large", "x86-xlarge", "x86-xxlarge", "s390x-small", "s390x-medium", "s390x-large", "s390x-xlarge", "s390x-xxlarge")
```

This error indicates that the task `deploy-checks` has an invalid `runtimeClassName`. A valid usage requires selecting a value from the supported runtimes.

Profiles

Configuration details of pipeline worker VMs, selectable by specifying runtimeClassName in the .pipeline-config.yaml for the desired pipeline task(s):

| runtimeClassName |vCPU | RAM | Storage|
|-|-|-|-|
|small| 1 | 4GB |32GB|
|medium|2|8GB|32GB|
|large|4|16GB|32GB|
|x86-large|4|16GB|150GB|
|x86-xlarge|8|32GB|300GB|
|x86-xxlarge|16|64GB|600GB|
|s390x-small|1|4GB|250GB|
|s390x-medium|2|8GB|250GB|
|s390x-large|4|16GB|250GB|
|s390x-xlarge|8|32GB|250GB|
|s390x-xxlarge|16|64GB|250GB|

##### when
{: #devsecops-pipeline-config-v2-task-when}

`when` is optional and accepts a CEL expression.

Syntax:

The basic syntax of specifying step task in .pipeline-config.yaml is:

```yaml
tasks:
  code-build:
    when: <condition>
```

The when field improves conditional execution of a task, ensuring that the task is only executed if the condition in when evaluates to true. You can define CEL expression in when, which is evaluated using the CEL interpreter.

For example, to run a task only on specific branches:

```yaml
tasks:
  code-build:
    # require the branch name to be master
    when: 'branch == master'
```

##### from
{: #devsecops-pipeline-config-v2-task-from}

The from field is primarily intended to support the matrix capability, and by default, the task executes in parallel with the task specified in the from field. This gives users the flexibility to specify multiple concurrent instances of any task with unique configurations and runtime profiles. Additionally, you can modify the task’s execution order by using the runAfter option.

For example, if you would like to configure `.pipeline-config.yaml` to run an additional instance of code-build, apply the following specification.

`.pipeline-config.yaml`:

```yaml
  tasks:
    code-build:
      image: ...
      script: ...
    code-build-2:
      from: code-build
      image: ...
      script: ...
```

The original `ci-pipeline` has a single instance of `code-build`. The processed `ci-pipeline` will be updated to include an additional instance of code-build under the tasks.

Snippet of the original `ci-pipeline`:

```yaml
kind: Pipeline
apiVersion: tekton.dev/v1beta1
metadata:
  name: ci-pipeline
spec:
  tasks:
    - name: code-build
      taskRef:
        name: code-build
      runAfter:
        - ci-start
```

Snippet of the generated `ci-pipeline`:

```yaml
kind: Pipeline
apiVersion: tekton.dev/v1beta1
metadata:
  name: ci-pipeline
spec:
  tasks:
    - name: code-build
      taskRef:
        name: code-build
      runAfter:
        - ci-start
    - name: code-build-2
      taskRef:
        name: code-build
      runAfter:
          - ci-start
```

Similarly, you can configure more than one instance of the same task code-build using from.

`.pipeline-config.yaml`:

```yaml
  tasks:
    code-build:
      image: ...
      script: ...
    code-build-2:
      from: code-build
      image: ...
      script: ...
    code-build-3:
      from: code-build
      image: ...
      script: ...
```

Here is the snippet of the generated `ci-pipeline`:

```yaml
kind: Pipeline
apiVersion: tekton.dev/v1beta1
metadata:
  name: ci-pipeline
spec:
  tasks:
    - name: code-build
      taskRef:
        name: code-build
      runAfter:
        - ci-start
    - name: code-build-2
      taskRef:
        name: code-build
      runAfter:
          - ci-start
    - name: code-build-3
      taskRef:
        name: code-build
      runAfter:
        - ci-start
```

Additionally, it is possible to configure multiple instances of code-build, each on a different runtime environment.

`.pipeline-config.yaml`:

```yaml
  tasks:
    code-build:
      runtimeClassName: large
    code-build-2:
      from: code-build
      runtimeClassName: small
    code-build-3:
      from: code-build
```

It is also possible to run multiple instances of code-build sequentially (i.e., `code-build` -> `code-build-2` -> `code-build-3`).

`.pipeline-config.yaml`:

```yaml
  tasks:
    code-build:
      image: ...
      script: ...
    # additional instance of code-build to run after code-build
    code-build-2:
      from: code-build
      image: ...
      script: ...
      runAfter:
        - code-build
    # additional instance of code-build to run after code-build-2
    code-build-3:
      from: code-build
      runAfter:
        - code-build-2
```

Snippet of the generated `ci-pipeline`:

```yaml
kind: Pipeline
apiVersion: tekton.dev/v1beta1
metadata:
  name: ci-pipeline
spec:
  tasks:
    - name: code-build
      taskRef:
        name: code-build
      runAfter:
        - ci-start
    - name: code-build-2
      taskRef:
        name: code-build
      runAfter:
        - ci-start
        - code-build
    - name: code-build-3
      taskRef:
        name: code-build
      runAfter:
        - ci-start
        - code-build-2
```

The above snippet generates the following resolved dependency graph in ci-pipeline:

```yaml
            ci-start
          /          \
    code-checks   code-build
                      |
                 code-build-2
                      |
                 code-build-3
```

Validation:

If you attempt to fan out a nonexistent task in ci-pipeline, the following error will occur:

Usage:

```yaml
 tasks:
   code-build-2:
     from: foo
```

```yaml
Error:

[User Error] the task specified to fan out from 'foo' is invalid, as the task 'foo' does not exist in the pipeline 'ci-pipeline'.

Only the following tasks can be fanned out: (ci-start, code-build, code-checks, deploy-checks, deploy-release).
```

This error indicates that the task code-build-2 has an invalid value in from. To resolve this, ensure the value is selected from the existing pipelineTasks.

##### timeout
{: #devsecops-pipeline-config-v2-task-timeout}

You can define a timeout for a stage/task within a pipeline by using the timeout field in the task’s specification. This sets the duration for how long a stage can execute within a pipelineRun. The timeout value is a duration, conforming to Go’s [ParseDuration](https://golang.org/pkg/time/#ParseDuration) format. For example, valid values are 1h30m, 1h, 1m, and 60s:

```yaml
tasks:
  code-build:
    timeout: 30m
```

Be careful when selecting this configuration. If a task or stage exceeds the timeout, the pipelineRun is suspended, the finally stage is executed, and the pipelineRun terminates with an error.

If a specific timeout isn’t set for a stage, the agent will default to the timeout defined for the pipelineRun. The default timeout can be found in the logs downloaded from the toolchain. The timeout is part of the pipelineRun resource, located at pipelineRun.spec.timeout.

##### include

include is optional and accepts a list of strings.

Syntax:

The basic syntax of using include in `.pipeline-config.yaml` is:

```yaml
tasks:
  <task>:
    include:
      - <task-1>
      - <task-2>
```

include is implemented to handle complex specifications, such as container runtimes. For example, selecting docker as the container runtime requires more than just a single-line specification, using include is ideal for such cases. Additionally, include can be extended to incorporate other specifications from compliance-pipelines, making it easier to implement future runtimes.

- docker as the container runtime

include dind task in your specifications to enable docker.

`.pipeline-config.yaml`:

```yaml
  tasks:
    code-build:
      include:
        - dind
```

These specifications are not complete; the step that needs to execute docker must include docker-socket, as explained in the following section.

Once these specifications are successfully applied, the following log should appear in the Agent Log:

```yaml
[PREPROCESSOR] Task 'code-build' has been updated to include all specifications from task 'dind'.
```

- Podman as the container runtime

include podman task in your specifications to enable podman.

.pipeline-config.yaml:

```yaml
  tasks:
    code-build:
      include:
        - podman
```

Similar to docker, these specifications are not complete; the step that needs to execute podman must include rootful-podman, as explained in the following section.

### Step
{: #devsecops-pipeline-config-v2-task-step}

Steps is a list of step with the following fields:

* Required:
    * name: The name of the step within the task. Step names should follow Kubernetes best practices, consisting of lowercase alphanumeric characters or hyphens (-).
    * script: A script that is invoked as if it was stored inside the container image.
* Optional:
    * image: Specifies the image to run this step/container with. The default is icr.io/continuous-delivery/pipeline/pipeline-base-ubi.
    * onError: Determines whether to continue execution when a step returns an error. The default is stopAndFail.
    * when: Allows you to set a condition in CEL (Common Expression Language). The step is executed only if this condition is met.
    * timeout: Allows you to set a timeout for a step before it fails. It accepts a duration, conforming to Go’s [ParseDuration](https://golang.org/pkg/time/#ParseDuration) format. For example, valid values are 1h30m, 1h, 1m, and 60s.
    * include: Enables the step to run rootful podman. It accepts podman as a valid option, with no default set.

Here is the classification of fields that are inherited from Tekton Pipelines and those defined by the IBM Continuous Delivery Pipeline:

|Tekton Pipelines API|CD Pipeline Configuration|
|-|-|
|name|include|
|script| |
|image| |
|onError| |
|when| |
|timeout| |

#### Step Specifications
{: #devsecops-pipeline-config-v2-task-step-specs}

The configuration specified for the steps in the `.pipeline-config.yaml` is applied to the steps in the referenced tasks of the triggered pipeline.

Any extra step specified in `.pipeline-config.yaml` that does not exist in the referenced task is ignored. This is designed to allow the same `.pipeline-config.yaml` to be used across multiple pipelines.

##### name
{: #devsecops-pipeline-config-v2-task-step-specs-name}

The name must correspond to the name of the step in the task.

name is required and accepts a string.

Syntax:

The basic syntax of specifying step name in .pipeline-config.yaml is:

```yaml
tasks:
  code-build:
    steps:
      - name: <name>
```

The specifications defined under this step are applied to the step in the specified task. For example, update the `.pipeline-config.yaml` as follows to specify the image and script for the `unit-test` step of a `code-build` task:

```yaml
tasks:
  code-build:
    steps:
      - name: unit-test
        image: icr.io/continuous-delivery/pipeline/pipeline-base-ubi:3.40
        script:
          #!/usr/bin/env bash
          cd "$WORKSPACE/$(load_repo app-repo path)"
          source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/test.sh
          save_deployment_artifact deployment_iks.yml IKS
          save_deployment_artifact deployment_os.yml OPENSHIFT
          run_test test com.ibm.unit_tests unit-test-result.xml 1
```

##### onError
{: #devsecops-pipeline-config-v2-onerror}

onError is optional and accepts a string.

Syntax:

The basic syntax of specifying step onError in `.pipeline-config.yaml` is:

```yaml
tasks:
  code-build:
    steps:
      - name: unit-test
        onError: < stopAndFail | continue >
```

abort_on_failure was a boolean used to determine whether to continue execution when a step returned an error, with the default being true in V1. abort_on_failure is replaced with onError in v2. onError can be set to either stopAndFail (the default) or continue.

If set to stopAndFail, a failed step will stop and fail the entire pipelineRun.
If set to continue, the failed step will not cause the pipelineRun to fail, and the remaining steps and stages will continue to execute.

#### timeout
{: #devsecops-pipeline-config-v2-task-step-specs-timeout}

timeout is optional and accepts a duration.

Syntax:

The basic syntax of specifying step timeout in .pipeline-config.yaml is:

```yaml
tasks:
  code-build:
    steps:
      - name: unit-test
        timeout: <duration>
```

If the execution time of a step exceeds the specified timeout, the step will terminate its running process, and any subsequent steps in the stage will not be executed.

Similar to the timeout field at the task level, it accepts a duration, conforming to Go’s [ParseDuration](https://golang.org/pkg/time/#ParseDuration) format.

```yaml
tasks:
  code-build:
    steps:
      - name: build-artifact
        timeout: 5m
```

If a step exceeds the timeout, the pipelineRun is suspended, the finally stage is executed, and the pipelineRun ends with an error.

If a specific timeout isn’t set for a step, the agent will use the timeout defined for the pipelineRun as the default.

##### when
{: #devsecops-pipeline-config-v2-task-step-specs-when}

when is optional and accepts a CEL expression.

Syntax:

The basic syntax of specifying step when in `.pipeline-config.yaml` is:

```yaml
tasks:
  code-build:
    steps:
      - name: unit-test
        when: <condition>
```

The when field improves conditional execution of a step, ensuring that the step is only executed if the condition in when evaluates to true. You can define CEL expression in when, which is evaluated using the CEL interpreter.

The skip field is being deprecated. It allowed users to skip the execution of a step, defaulting to false. The same behavior, skip: true, can now be achieved with when: false.

For example, to skip test from the code-build task, you can specify when:

```yaml
tasks:
  code-build:
    steps:
      - name: test
        when: 'false'
```

##### include
{: #devsecops-pipeline-config-v2-task-step-specs-include}

include is optional and accepts a list of strings.

Syntax:

The basic syntax of using include in `.pipeline-config.yaml` is:

```yaml
tasks:
  <task>:
    steps:
      - name: <step>
        include:
          - <step-1>
          - <step-2>
```

Similar to include at the task level, include under a step is implemented to merge specifications at the step level.

docker as the container runtime
With the task level include, merge docker-socket specifications to enable docker at that step:

```yaml
  tasks:
    code-build:
      include:
        - dind
      steps:
        - name: build-artifact
          include:
            - docker-socket
```

These specifications are equivalent to specifying dind: true in Pipeline Config V1. While they are more complex than that, they are designed to extend and implement a configuration-driven architecture.

## Comparison of Pipeline Configuration v1 and v2
{: #devsecops-pipeline-config-v2-comparision}

An example of `.pipeline-config.yaml` in v1:

```yaml
version: '1'
preprocessor_version: 'latest'

setup:
  image: icr.io/continuous-delivery/pipeline/pipeline-base-ubi:3.40
  script: |
    #!/usr/bin/env bash
    if [[ "$(get_env pipeline_namespace)" == *"pr"* ||  "$(get_env pipeline_namespace)" == *"ci"* ]]; then
      source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/code_setup.sh
    fi

test:
  image: icr.io/continuous-delivery/pipeline/pipeline-base-ubi:3.40
  abort_on_failure: false
  script: |
    #!/usr/bin/env bash
    cd "$WORKSPACE/$(load_repo app-repo path)"
    source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/test.sh

containerize:
  image: icr.io/continuous-delivery/pipeline/pipeline-base-ubi:3.40
  dind: true
  runtimeClassName: large
  script: |
    #!/usr/bin/env bash
    cd "$WORKSPACE/$(load_repo app-repo path)"
    source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/build_setup.sh
    source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/build.sh

deploy:
  image: icr.io/continuous-delivery/pipeline/pipeline-base-image:2.53
  script: |
    #!/usr/bin/env bash
    source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/deploy_setup.sh
    source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/deploy.sh

dynamic-scan:
  skip: true
```

The same example of `.pipeline-config.yaml` in v2:

```yaml
version: '2'

tasks:
  code-checks:
    steps:
      - name: checks-setup
        image: icr.io/continuous-delivery/pipeline/pipeline-base-ubi:3.40
        script: |
          #!/usr/bin/env bash
          if [[ "$(get_env pipeline_namespace)" == *"pr"* ||  "$(get_env pipeline_namespace)" == *"ci"* ]]; then
            source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/code_setup.sh
          fi

  code-build:
    include:
     - docker
    runtimeClassName: large
    steps:
      - name: setup
        image: icr.io/continuous-delivery/pipeline/pipeline-base-ubi:3.40
        script: |
          #!/usr/bin/env bash
          if [[ "$(get_env pipeline_namespace)" == *"pr"* ||  "$(get_env pipeline_namespace)" == *"ci"* ]]; then
            source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/code_setup.sh
          fi
      - name: unit-test
        onError: continue
        image: icr.io/continuous-delivery/pipeline/pipeline-base-ubi:3.40
        script: |
          #!/usr/bin/env bash
          cd "$WORKSPACE/$(load_repo app-repo path)"
          source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/test.sh
      - name: build-artifact
        include:
          - docker-socket
        image: icr.io/continuous-delivery/pipeline/pipeline-base-ubi:3.40
        script: |
          #!/usr/bin/env bash
          cd "$WORKSPACE/$(load_repo app-repo path)"
          source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/build_setup.sh
          source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/build.sh

  deploy-checks:
    steps:
      - name: deploy
        image: icr.io/continuous-delivery/pipeline/pipeline-base-image:2.53
        script: |
          #!/usr/bin/env bash
          source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/deploy_setup.sh
          source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/deploy.sh
      - name: dynamic-scan
        when: 'false'
```

## Matrix Strategy
{: #devsecops-pipeline-config-v2-matrix-strategy}

The pipeline configuration v2 introduces an option to specify a task definition in the .pipeline-config.yaml. This gives users the flexibility to specify multiple concurrent instances of any task.

```yaml
tasks:
  code-build:
    runtimeClassName: medium
    steps:
      - name: build-artifact
        script: |
          #!/usr/bin/env bash
          source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/build_setup.sh
          source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/build.sh

  code-build-beta:
    from: code-build
    runtimeClassName: small
    steps:
      - name: build-artifact
        script: |
          #!/usr/bin/env bash
          source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/build_setup.sh
          source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/build.sh
```

This design allows defining tasks running in parallel with unique configurations and runtime profiles. The existing matrix capability will be deprecated in favor of this design.

## Current Limitations
{: #devsecops-pipeline-config-v2-task-current-limitation}

Some of the 1PL tools require dind to execute successfully, and this must be specified in the .pipeline-config.yaml. For example, the following steps in the code-checks of the ci-pipeline require dind:
- detect-secrets
- compliance-checks
- static-scan

`.pipeline-config.yaml`:

```yaml
version: '2'
tasks:
  code-checks:
    include:
      - dind
    steps:
      - name: detect-secrets
        include:
          - docker-socket
      - name: compliance-checks
        include:
          - docker-socket
      - name: static-scan
        include:
          - docker-socket
```

`owasp-zap` scan will only work with the following specifications in `.pipeline-config.yaml`. The owasp-zap is triggered as an asynchronous pipeline with a single task async-stage. The async-stage task has three steps:
- start
- run-stage
- finish

The `run-stage` step requires Docker. However, when the pipeline configuration includes specifications only for `owasp-zap` (as in Pipeline Config V1), the preprocessor ignores them. These configurations are essential for setting the correct `script` by the 1PL config parser. In the next release, we will introduce a feature where the preprocessor will also handle the scripts. This will eliminate the need for this extra configuration.

```yaml
tasks:
  async-stage:
    include:
      - dind
    steps:
      - name: run-stage
        image: icr.io/continuous-delivery/pipeline/pipeline-base-ubi:3.46
        include:
          - docker-socket
      - name: owasp-zap
        script: |
          #!/usr/bin/env bash

          source $WORKSPACE/$PIPELINE_CONFIG_REPO_PATH/scripts/zap/trigger_zap_scans
```

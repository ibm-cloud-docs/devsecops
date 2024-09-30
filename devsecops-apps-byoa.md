---

copyright:
  years: 2021, 2024
lastupdated: "2023-09-29"

keywords: DevSecOps, bring your own app, bring your own application, app, application, microservice

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Bringing your own app to DevSecOps
{: #cd-devsecops-apps-byoa}

You can bring your own application to a DevSecOps toolchain for continuous integration and continuous deployment.
{: shortdesc}

Create your DevSecOps toolchain by using the default sample app that is provided. The [hello-compliance-app](https://us-south.git.cloud.ibm.com/open-toolchain/hello-compliance-app) hosts a Node.js server that provides a static web page. Alternatively, you can begin with one of the following samples:

- [Code-engine-compliance-app](https://us-south.git.cloud.ibm.com/open-toolchain/code-engine-compliance-app)
- [Go-compliance-app](https://us-south.git.cloud.ibm.com/open-toolchain/go-gin-compliance-app)
- [Python-compliance-app](https://us-south.git.cloud.ibm.com/open-toolchain/python-compliance-app)
- [Node-cloudant-compliance-app](https://us-south.git.cloud.ibm.com/open-toolchain/node-cloudant-compliance-app): This application combines the application source Node.js and the dependency definition, which is Cloudant defined as Infrastructure-as-Code.

## Adding stage parameters
{: #stage-parameters}

The source code of each sample app contains a `.pipeline-config.yaml` file. The `.pipeline-config.yaml` file is the core configuration file that is used by continuous integration and continuous deployment toolchains for all of the stages in the pipeline run process.

Add a `.pipeline-config.yaml` configuration file that contains the following properties that are required by each stage:

`image` {: #byoa-image}
:   The Docker image name that is used to run the stage. For example, use the following code to sign your images:

   ```bash
   image: icr.io/continuous-delivery/pipeline/image-signing:1.0.   0@sha256:e9d8e354668ba3d40be2aaee08298d2aa7f0e1c8a1829cca4094ec93830e3e6a
   ```
   {: codeblock}

`abort_on_failure` {: #byoa-abort-failure}
:   Set this property to `true` to stop the pipeline run if the stage fails. If this property is set to `false`, then the step is marked as passed with a warning state, also known as an amber state, and the pipeline continues to run.

`script` {: #byoa-script}
:   The script that performs the actions that are required in the stage. Create the scripts in the script directory within the app repository, and call the scripts from this location. For example, the following code snippet shows the content of the `script` section when you want to sign the images:

    ```bash
    script: |
       #!/usr/bin/env bash
       STAGE_DIND="true"
       STAGE_ABORT_ON_FAILURE="false"
       STAGE_IMAGE_PULL_POLICY="IfNotPresent"
       source scripts/sign_image.sh
    ```
    {: codeblock}

`dind` {: #byoa-dind}
:   Set this property to `true` if you want to enable `docker` functions in the running pipeline. After you determine which parameters are required, you can define various steps in the pipeline.

`setup` {: #byoa-setup}
:   Define this stage to run the pre-setup scripts.

    ```bash
    setup:
       image: icr.io/continuous-delivery/pipeline/pipeline-base-image:2.      12@sha256:ff4053b0bca784d6d105fee1d008cfb20db206011453071e86b69ca3fde706a4
       script: |
       #!/usr/bin/env bash
       echo "Please insert any required pre-build tasks in this stage."
    ```
    {: codeblock}

`test` {: #byoa-test}
:   Define this stage to run the test cases of the app.

    ```bash
    test:
       abort_on_failure: false
    image: icr.io/continuous-delivery/pipeline/pipeline-base-image:2.      12@sha256:ff4053b0bca784d6d105fee1d008cfb20db206011453071e86b69ca3fde706a4
    script: |
       #!/usr/bin/env bash
       cd ../"$(load_repo app-repo path)"
       #npm ci
       #npm test
       source test/test.sh
    ```
    {: codeblock}

`static-scan` {: #byoa-static-scan}
:   Define this stage to run a static scan on the code.

    ```bash
    static-scan:
       dind: true
       image: icr.io/continuous-delivery/pipeline/pipeline-base-image:2.12@sha256:ff4053b0bca784d6d105fee1d008cfb20db206011453071e86b69ca3fde706a4
       script: |
       #!/usr/bin/env bash
       echo "Please insert script to invoke/execute static scan tool like SonarQube on the application source code."
    ```
    {: codeblock}

`containerize` {: #byoa-containerize}
:   Define this stage to build and containerize your app.

    ```bash
    containerize:
       dind: true
       image: icr.io/continuous-delivery/pipeline/pipeline-base-image:2.      12@sha256:ff4053b0bca784d6d105fee1d008cfb20db206011453071e86b69ca3fde706a4
       script: |
       #!/usr/bin/env bash

       if [[ "$PIPELINE_DEBUG" == 1 ]]; then
       trap env EXIT
       env
       set -x
       fi
       source scripts/build_setup.sh
       source scripts/build.sh
    ```
    {: codeblock}

`deploy` {: #byoa-deploy}
:   Define this stage to deploy your app on the target environment.

    ```bash
    deploy:
       image: icr.io/continuous-delivery/pipeline/pipeline-base-image:2.      12@sha256:ff4053b0bca784d6d105fee1d008cfb20db206011453071e86b69ca3fde706a4
       script: |
       #!/usr/bin/env bash

       if [[ "$PIPELINE_DEBUG" == 1 ]]; then
       trap env EXIT
       env
       set -x
       fi
       source scripts/deploy_setup.sh
       source scripts/deploy.sh
    ```
    {: codeblock}

`sign-artifact` {: #byoa-sign-artifact}
:   By default the DevSecOps CI toolchain signs all the images that are built during the containerizing stage. GPG keys that are provided during the setup of the toolchain are used to [sign the images](https://github.com/containers/skopeo/blob/main/docs/skopeo-copy.1.md). If you want to customize the image signing process, then add the following stage definition in your `.pipeline-config.yaml`:

    ```bash
    sign-artifact:
       abort_on_failure: false
       image: icr.io/continuous-delivery/pipeline/image-signing:1.0.      0@sha256:e9d8e354668ba3d40be2aaee08298d2aa7f0e1c8a1829cca4094ec93830e3e6a
       script: |
       #!/usr/bin/env bash
       STAGE_DIND="true"
       STAGE_ABORT_ON_FAILURE="false"
       STAGE_IMAGE_PULL_POLICY="IfNotPresent"
       source scripts/sign_image.sh
    ```
    {: codeblock}

`acceptance-test` {: #byoa-acceptance-test}
:   Define this stage to run your acceptance test after deployment.

    ```bash
    acceptance-test:
       abort_on_failure: false
       image: icr.io/continuous-delivery/pipeline/pipeline-base-image:2.      12@sha256:ff4053b0bca784d6d105fee1d008cfb20db206011453071e86b69ca3fde706a4
       script: |
       #!/usr/bin/env bash
       export APP_URL=$(cat ../app-url)
       source scripts/setup_go.sh
       echo "APP_URL :- ${APP_URL}"
       go run acceptance-test/acceptance-test.test.go
    ```
    {: codeblock}

`release` {: #byoa-release}
:   Define this stage to upload evidence and artifacts that were generated from the previous stages.

    ```bash
    release:
       abort_on_failure: false
       image: icr.io/continuous-delivery/toolchains/devsecops/compliance-baseimage:2.26.      1@sha256:a780174a64474187b01b5e40a1721d8307f02897ac6f3eba2d482d4f4926edf1
       script: |
       #!/usr/bin/env bash
       source scripts/release.sh
    ```
    {: codeblock}

`scan-artifact` {: #byoa-scan-artifact}
:   Define this stage to run a scan for vulnerabilities in the generated artifacts.

    ```bash
    scan-artifact:
       abort_on_failure: false
       image: icr.io/continuous-delivery/pipeline/pipeline-base-image:2.      6@sha256:7f588468622a981f89cf5e1212aaf75fface9da6169b5345ca52ab63d8215907
       script: |
       #!/usr/bin/env bash
       source scripts/va_scan.sh
    ```
    {: codeblock}

`dynamic-scan` {: #byoa-dynamic-scan}
:   Define this stage to run dynamic scan on the deployed application.

    ```bash
    dynamic-scan:
       dind: true
       abort_on_failure: false
       image: icr.io/continuous-delivery/pipeline/pipeline-base-image:2.12@sha256:ff4053b0bca784d6d105fee1d008cfb20db206011453071e86b69ca3fde706a4
       script: |
       #!/usr/bin/env bash
       echo "Please insert script to invoke/execute dynamic scan tool like OWASP ZAP on the built and deployed application."
    ```
    {: codeblock}


    For more information about the stages, see [custom scripts](docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize).

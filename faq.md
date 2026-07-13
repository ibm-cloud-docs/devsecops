---

copyright: 
  years: 2022, 2026
lastupdated: "2026-07-09"

keywords: DevSecOps

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# FAQ for DevSecOps
{: #faq_devsecops}

Get answers to frequently asked questions about using DevSecOps.
{: shortdesc} 



## What is the difference between CI and CC pipeline?
{: #ci-cd-pipeline-diff}
{: faq}
{: support}

You might notice that the CI and CC pipeline have common steps. The scans and checks that are run are similar in nature and details. The following table provides the differences between CI and CC pipeline.

| CI pipeline | CC pipeline |
|---------|------------|
| It is part of the CI toolchain. | It is part of the CC toolchain. |
| It is triggered after a merge request is merged with the `master` branch | It can be triggered manually or at predefined intervals that are independent of a deployment schedule. |
| An application URL and application code repository details are entered as part of the setup process. | An application URL and application code repo details are provided after the CC toolchain is configured and before initiation of first pipeline run. |
| The incident issues that are created as part of various scans and checks during compliance checks do not carry a due date. | The incident issues that are created as part of various scans and checks during compliance checks carry a due date. |
| The incident issues that are created are found during the build. | The incident issues that are created are found during periodic scans of the staging or production environment. |
| The `summary.json` file is not generated at the end of each CI pipeline run. | The `summary.json` file is not generated at the end of each CI pipeline run. |
| It includes steps like application artifact creation, artifact signing, and deploy to development cluster. This in turn creates inputs for the CD pipeline.  | It runs only scans and checks that are needed for compliance testing. |
{: caption="Differences in the CI and CC pipelines" caption-side="bottom"}

## How can a user customize the pipeline?
{: #faq-ci-sec-pipeline-customize}
{: faq}
{: support}

A pipeline is customized by using custom scripts. Custom scripts are extension points in the pipeline where adopters, teams, and users can provide scripts to run custom tasks for their CI/CD strategies.

Custom scripts control the pipeline stages. You can use a configuration file `(pipeline-config.yaml)` to configure the behavior of the stages, script content, and the base artifact that runs the scripts. The scripts and configuration for pipeline stages are loaded from an application repository that are similar to `.travis.yml` or `Jenkinsfile` or a custom repository.

For more information, see [Customizing pipelines by using custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize).

## Multi-arch image for s390x and Power platforms limitations
{: #faq-ci-sec-pipeline-power-limitation}
{: faq}
{: support}

One-Pipeline provides native support for s390x and Power platforms using `runtimeClassName` (a string to indicate the runtime profile) in the one-pipeline configuration file.
However, this native support comes with some **limitations** and recommendations:
- Limitations:
    - This feature is available in **v11 only**.
    - The **pod start time** is higher than for x86 runtime class.
    - You must use **podman** with s390x or Power workloads. **Docker is not available**.
      - User scripts needs to be updated to work with podman command instead of docker command
      - The stage image should have podman installed.
- Recommendations: use x86 runtime classes for
    - scanning, as the various scanning tool images may not have multi-arch support.
    - image signing, as multi-arch support is not available (work in progress).

## How can I build a custom multi-architecture base image?
{: #faq-build-multiarch-image}
{: faq}
{: support}

One Pipeline provides an official multi-architecture base image that supports the following platforms:

- `linux/amd64`
- `linux/ppc64le`
- `linux/s390x`

For most use cases, we recommend using the official base image directly:

```
icr.io/continuous-delivery/toolchains/devsecops/baseimage:<version>
```

If your application requires additional operating system packages, language runtimes, or other custom dependencies, you can build your own custom multi-architecture base image as part of your CI pipeline.

Use Docker Buildx with the `--platform` option in your `build-artifact` step:

```bash
docker buildx build \
  --platform linux/amd64,linux/ppc64le,linux/s390x \
  -t <registry>/<namespace>/<image>:<tag> \
  --push .
```

This builds architecture-specific images and publishes them as a single multi-architecture image manifest. Container runtimes automatically pull the appropriate image for the target platform.

### Recommended approach

1. Use the One Pipeline base image as the `FROM` image in your Dockerfile.
2. Add only the additional packages or dependencies required by your application.
3. Build and publish the image using Docker Buildx in your CI pipeline.
4. Verify the published image using `docker buildx imagetools inspect` or `skopeo inspect`.

For more information about multi-arch support and limitations, see [Multi-arch image for s390x and Power platforms limitations](#faq-ci-sec-pipeline-power-limitation).

## Triggering pipeline using CLI
{: #faq-trigger-pipeline-cli}
{: faq}
{: support}

A pipeline can be triggered by using the IBM Cloud CLI or a API.
With the CLI, you can start a pipeline by providing the toolchain and pipeline IDs. Using the API, you can send a POST request with the right authentication and headers to trigger the pipeline.

For more information , see [Using Triggers](/docs/ContinuousDelivery?topic=ContinuousDelivery-tekton-pipelines&interface=api#using-triggers)

## What environment property is used for cloning Git repositories?
{: #faq-clone-git-repo}
{: faq}
{: support}

The DevSecOps pipelines use a hierarchical token system to authenticate Git repository operations, including cloning. The `git-token` environment property serves as the default token, but you can override it with more specific tokens for better security and access control.

### Token Priority Order

The pipeline resolves Git authentication tokens in the following priority order (highest to lowest):

1. **Personal Access Token (PAT)** specified in the toolchain integration for a specific repository
2. **Repository-specific token**: `git-token-[repo_name]-[repo_org]`
3. **Organization-specific token**: `git-token-[repo_org]`
4. **Default token**: `git-token`
5. **OAuth token** from toolchain integration (if using OAuth instead of PAT)

### Token Naming Examples

For a repository at `https://github.com/my-org/my-app`:
- Repository-specific: `git-token-my-app-my-org`
- Organization-specific: `git-token-my-org`

### Important Considerations

- If the same `git-token` is used for both reading repositories (cloning) and writing operations (setting PR/CI status, updating inventory), **read-only access is not sufficient**. The token must have write permissions.
- Using repository or organization-specific tokens allows you to apply the principle of least privilege by granting only the necessary permissions for each repository.

For more information about repository token functions, see [Retrieving repository information and tokens](/docs/devsecops?topic=devsecops-devsecops-get_repo_params).

## How to test changes to OnePipeline configuration?
{: #faq-test-onepipeline-config}
{: faq}
{: support}

Testing changes to your OnePipeline configuration requires a systematic approach to avoid disrupting production pipelines while validating customizations.

### Understanding OnePipeline Components

OnePipeline consists of two main customizable components:

- **Tekton Pipeline Definitions**: Centrally managed pipeline definitions for PR, CI, CD, and CC workflows, available in the [compliance-pipelines repository](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-pipelines)). New versions are published every 2-week sprint.
  - **v10**: Stable version with limited concurrency
  - **v11**: Next-generation version with maximum customizability and concurrency
  
- **Pipeline Configuration (`.pipeline-config.yaml`)**: Custom configuration file that overrides default pipeline behaviors, including pipeline images, scripts, architectures, and properties. This file can be stored in your application repository or in a centralized configuration repository.

### Testing Approach 1: Using a Test Configuration File

This is the recommended approach for testing configuration changes without affecting production pipelines.

1. **Create a test branch**
   - Create a branch in the repository containing your `.pipeline-config.yaml` file
   - Make your configuration changes in this branch

2. **Set up a test trigger**
   - Duplicate the existing pipeline trigger in your toolchain
   - Name it clearly (for example, `Manual-Test-Config`)
   - Update the trigger properties to point to your test configuration:
     - `pipeline-config`: Filename of your configuration file
     - `pipeline-config-branch`: Your test branch name
     - `pipeline-config-repo`: Repository URL containing the configuration

3. **Test in development mode**
   - Enable [development mode](/docs/devsecops?topic=devsecops-devsecops-devmode) in the trigger properties
   - Run the pipeline to validate your custom scripts
   - Development mode skips evidence collection, compliance issue creation, and inventory updates, making it ideal for rapid iteration
   - **Important**: Development mode is not suitable for production workloads

4. **Test with compliance checks**
   - After validating scripts in development mode, disable `dev-mode`
   - Keep inventory updates disabled during testing
   - Run a full pipeline with evidence collection and issue management
   - Verify all compliance checks pass as expected

5. **Promote to production**
   - When testing is satisfactory, create a pull request to merge your changes to the main branch
   - The changes will automatically apply to production triggers
   - If issues arise, you can quickly revert the changes

### Testing Approach 2: Modifying Pipeline Layout

For v11 pipelines, you can extensively modify the pipeline layout using Matrix Strategy and other features (e.g. `runAfter` and `from`):
- Leverage the [matrixing capability](/docs/devsecops?topic=devsecops-devsecops-pipeline-config-v2#devsecops-pipeline-config-v2-matrix-strategy) in `.pipeline-config.yaml`
- Test using Approach 1 after making layout changes

**Forking Pipeline Definitions** (Advanced)
- Fork the [compliance-pipelines repository](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-pipelines))
- Develop and test changes in your fork
- Add the forked repository as a Git integration in your toolchain
- Temporarily update the pipeline definition to reference your fork
- Configure a [development mode](/docs/devsecops?topic=devsecops-devsecops-devmode) trigger to test the new pipeline definition
- Follow the testing steps from Approach 1

### Best Practices

- Always test in development mode first to catch script errors quickly
- Use descriptive names for test triggers to avoid confusion
- Document your configuration changes in commit messages
- Keep test triggers disabled or delete them after testing to prevent accidental runs
- Consider creating a dedicated test toolchain for major pipeline changes
- Review pipeline logs carefully to ensure all stages execute as expected

For more information about customizing pipelines, see [Custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize) and [Pipeline configuration v2](/docs/devsecops?topic=devsecops-devsecops-pipeline-config-v2).

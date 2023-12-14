---

copyright: 
  years: 2023, 2023
lastupdated: "2023-12-12"

keywords: DevSecOps, development mode, IBM Cloud, pipelines

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Running pipelines in `development mode`
{: #devsecops-devmode}

`development mode` for CI and CD pipelines tests the implementation of your DevSecOps configuration (yaml) file, without running any security or compliance-related task. `development mode` also reduces pipeline execution time.
{: shortdesc}

Use `development mode` for development purposes only. `development mode` is not a replacement for the official DevSecOps CI and CD pipelines, which remain the reference implementations.
{: attention}

Develop, implement, and test changes made to the `.pipeline-config.yaml` file by using a simplified pipeline. This pipeline has the following characteristics:

- Runs the various stages of the `.pipeline-config.yaml` file only.
- Skips the compliance and security-related tasks to optimize the time to run your code.

## Prerequisites
{: #devsecops-devmode-preq}

- An existing DevSecOps CI or CD toolchain.
- A `.pipeline-config.yaml` and the corresponding build, test, and deploy scripts.

## Setting up `development mode`
{: #devsecops-devmode-setup}

Take the following steps to set up `development mode` pipeline:

1. Go to the **Triggers** page of your CI or CD pipeline.
1. Duplicate the manual trigger. Click the **Actions** icon ![Actions icon](../icons/actions-icon-vertical.svg) on the **Manual Trigger** and click **Duplicate**.

   ![Duplicate manual trigger](images/dup-trigger.png){: caption="Figure 1. Duplicate manual trigger" caption-side="bottom"}

1. Name the trigger. For example, `Manual-Dev-Mode`.
1. Change the `EventListener` to `dev-mode-listener` (for CI) or `dev-mode-cd-listener` (for CD).

   ![Dev Mode Trigger](images/dev-mode-trigger.png){: caption="Figure 2. Change event listener" caption-side="bottom"}

1. Save your changes.

## Run the `development-mode` pipeline
{: #devsecops-devmode-run}

Take the following steps to run the `development mode` pipeline:

1. Go to the **PipelineRuns** page. 
1. Click **Run Pipeline** and select the `Manual-Dev-Mode` trigger that you created.
1. Click **Run**.

The `development-mode` pipeline runs the code that is in the `.pipeline-config.yaml` file only.
{: note}

All other security and compliance tasks do not run, reducing the pipeline execution time.

![Standard and development modes compared](images/comparison.png){: caption="Figure 3. Standard and development modes compared" caption-side="bottom"}

##Development mode for CI pipeline
{: #devsecops-devmod-ci_pipeline}

The CI pipeline builds deployable artifacts from application repositories.

The following table lists the stages in development mode of the CI pipeline.

| Dev-mod-ci pipeline stage  | Stage description |
|:------|:------------|
| `code-ci-start` |Set up the pipeline environment. This process includes the cloning of the configuration and application repositories to the pipeline local file system. |
|`code-setup` | Set up your build and test environments to prepare them for the following pipeline stages. |
|`code-unit-tests`| Run unit tests and application tests on the application code. |
|`build-artifact`| Build the artifacts and images. |
|`deploy-dev`| Deploy the built artifacts to the dev environment. |
|`deploy-acceptance-tests`| Run acceptance and integration tests on the deployed built artifacts on the dev environment |
|`deploy-release`| Add the built artifacts to the inventory. The Continuous Delivery pipeline uses this inventory. |
|`code-ci-finish`| Collect, create, and upload the logs files, artifacts, and evidence to the evidence locker. If any of the compliance checks that were previously executed in the pipeline fail, this stage also fails. This stage represents the “fail safe” behavior to ensure that compliance issues are remediated before a PR can be merged or a build can be deployed. |
{: caption="Table 1. Development stages of the CI pipeline" caption-side="top"}

## Development mode for CD pipeline

The CD pipeline deploys the build to an environment, such as staging or production.

The following table lists the stages in development mode of the CD pipeline.

|CD Pipeline Stage |Stage Description |
|:------|:------------|
|`prod-start`| Calculate deployment delta. |
|`prod-setup` | Set up your build and test environments. |
|`prod-verify-artifact` | Verifiy signatures of images that are built in the CI pipeline. |
|`prod-deployment`|Deploy the build to an environment.|
|`prod-acceptance-tests`|Run acceptance tests.|
|`prod-finish`|Publish acceptance tests, deploy record, collect CycloneDX SBOM, and close change requests.|
{: caption="Table 2. Development stages of the CD pipeline" caption-side="top"}

## Returning to standard mode
{: #devsecops-devmode-standard}

When you are satisfied with the implementation of the `.pipeline-config.yaml` file and its associated scripts, either disable or delete the `Manual-Dev-Mode` trigger, and instead use the `standard` trigger that you use to run security and compliance checks.

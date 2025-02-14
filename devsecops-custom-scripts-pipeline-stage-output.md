---

copyright:
  years: 2024
lastupdated: "2024-05-24"

keywords: DevSecOps, inventory model, inventory, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Stage output
{: #cd-devsecops-scripts-stageoutput}

Some data is expected to be available by other stages on the workspace, so be sure to create them by using the [`pipelinectl` commands](/docs/devsecops?topic=devsecops-devsecops-pipelinectl). To make the required outputs available to the other stages and scripts in the pipeline, use pipelinectl's save methods, like save_repo, save_artifact, save_result, and set_env.

|Source stage      | Description	| `Pipelinectl` method | Required |
|------------------|--------------|-------------------   |----------|
|`setup` 		       |You can add more repos to the pipeline compliance checks and static code scan		|`save_repo`		|No		|
|`test`		         |To [attach test results to the compliance evidence as evidence artifacts](/docs/devsecops?topic=devsecops-cd-devsecops-add-pipeline-steps), use the `save_result` command in this stage.		|`save_result`			|No		|
|`static-scan`		 |To [attach test results to the compliance evidence as evidence artifacts](/docs/devsecops?topic=devsecops-cd-devsecops-add-pipeline-steps), use the `save_result` command in this stage. 		|`save_result`		|No		|
|`dynamic-scan`		 |To [attach test results to the compliance evidence as evidence artifacts](/docs/devsecops?topic=devsecops-cd-devsecops-add-pipeline-steps), use the `save_result` command in this stage. 		|`save_result`		|No		|
|`containerize`		 |[Add artifact names and digests to the pipeline](/docs/devsecops?topic=devsecops-cd-devsecops-config-github).		|`save_artifact`		|Yes		|
|`sign-artifact`   |Add the image signature from the GPG signing output.   	|`save_artifact`			|Yes		|
|`acceptance-test` |To [attach test results to the compliance evidence as evidence artifacts](/docs/devsecops?topic=devsecops-cd-devsecops-add-pipeline-steps), use the `save_result` command in this stage. 		|`save_result`			|No		|
{: caption="Stage output" caption-side="top"}

## Stage results API
{: #cd-devsecops-scripts-resultsapi}

The result states of these custom stages are saved with `pipelinectl`. The result state of a stage is either success, failure, or skipped. These results can be used later to evaluate whether the custom stage ran successfully.

The following tasks and stages are available:

| Pipeline                                  | Task or stages |
|-------------------------------------------|----------------|
|**Pull request pipeline stages**           | setup, test, detect-secrets, and branch-protection. The detect-secrets and branch-protection stages are not custom stages. They are provided by the pipelines by default.|
|**Continuous integration pipeline stages** | `setup`, `test`, `static-scan`, `containerize`, `sign-artifact`, `deploy`, `acceptance-test`, `scan-artifact`, `release`, `detect-secrets`, `branch-protection`, `bom-check`, `cis-check`, and `vulnerability-scan`. The `detect-secrets`, `branch-protection`, `bom-check`, `cis-check`, and `vulnerability-scan` stages are not custom stages. They are provided by the pipelines by default. |
|**Continuous deployment pipeline stages**  | `setup`, `deploy`, `acceptance-test`, `create-change-request`, `change-request-check-approval`, `change-request-change-state-to-implement`, and `close-change-request`. The `create-change-request`, `change-request-check-approval`, `change-request-change-state-to-implement`, and `close-change-request` stages are not custom stages. They are provided by the pipelines by default.|
|**Continuous compliance pipeline stages**  | `setup`, `test`, `static-scan`, `scan-artifact`, `acceptance-test`, `detect-secrets`, `branch-protection`, `bom-check`, `cis-check`, and `vulnerability-scan`. The `detect-secrets`, `branch-protection`, `bom-check`, `cis-check`, and `vulnerability-scan` stages are not custom stages. They are provided by the pipelines by default.|
{: caption="Tasks and stages" caption-side="bottom"}

### Example usage
{: #devsecops-scripts-resultsapi-example}

```bash
# List saved stage results
$ get_data result
detect-secrets
branch-protection

# Get stage result
$ get_data result detect-secrets
success
```
{: codeblock}

For more information about the Stage Results API, see [Using the Stage Results API in custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-stage-results).

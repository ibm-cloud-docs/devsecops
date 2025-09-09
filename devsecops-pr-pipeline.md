---

copyright:
  years: 2021, 2023
lastupdated: "2023-05-02"

keywords: DevSecOps

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Pull request pipeline
{: #cd-devsecops-pr-pipeline}

Pull request pipeline runs a set of compliance status checks on a pull request for the specified application repository.
{: shortdesc}

Attempts to merge a pull request into the master branch might be blocked because of failed compliance status checks. Opening or updating a pull request against the master branch triggers the pull request pipeline to run. You can run your own setup for the pipelines and tests in [Custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize).

## Stages and tasks
{: #cd-devsecops-pipeline-order}

The table below lists the tasks run in a PR Pipeline. In addition, the table also provides an overview of each of these stages:

- **Task or Stage**: This refers to the name of the stage as defined within the `.pipeline-config.yaml` configuration file.

- **Short description**: This provides a concise explanation of the actions performed during the execution of the stage.

- **Customisation permissible**: This indicates whether users have the flexibility to modify or replace the default behavior of the stage by inserting a custom script in the `.pipeline-config.yaml` file.

- **Default Reference Implementation**: This indicates whether the DevSecOps pipelines come with a pre-defined or default implementation for the stage. Notably, for certain stages like `unit-tests` or `setup`, the DevSecOps pipeline doesn't offer any out-of-the-box implementation. Instead, users are required to provide custom scripts or code tailored to their application's requirements.

- **Evidence Collection**: This indicates whether the stage performs the collection of standard evidence. When DevSecOps **Pipeline** provide a reference implementation for a stage, evidence collection is performed out-of-the-box. However, if **User** choose to modify or replace these predefined stages, they must ensure that their custom implementations include appropriate evidence collection. The same responsibility falls on users for stages where the DevSecOps pipeline doesn't provide an out-of-the-box implementation, necessitating them to perform evidence collection. The column indicates the entity (**User/Pipeline**) responsible for carrying out the evidence collection.

- **Skip permissible (applicable to version >= v10)**: This indicates whether users can opt out of running this stage by setting the skip property to true in the ``.pipeline-config.yaml``. However, caution is advised when using this feature, especially for stages designed to collect evidence. Skipping such stages might lead to missing essential evidences for the build.

|Task or stage |Short description	| Customisation permissible in `.pipeline-config.yaml` | Default Reference Implementation | Evidence Collection Requirement|Skip permissible|
|:----------|:------------------------------|:------------------|--|--|--|
|`start` 		 |Set up the pipeline environment. 		|No		|Yes | NA | No |
|`setup`		 |Set up your build and test environment.			|Yes| No | NA |No|
|`detect-secrets`		|Run detect secrets scan on the application code.		|Yes		| Yes | NA |No|
|`unit-tests`|Run unit tests and app tests on app code.		|Yes | No | NA |Yes|
|`compliance-checks` 	 |Run Code Risk Analyzer scans and other compliance checks on app repos.   	|Yes		| Yes | NA |Yes|
|`finish`| Consolidate the pipeline status. | Yes | Yes | NA |Yes|
{: caption="Pipeline order" caption-side="top"}

For more information about how to customize stages by using the `.pipeline-config.yaml` file, see [Custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize) and [Pipeline parameters](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm).

## Detect Secrets scan
{: #cd-devsecops-pr-pipeline-detect-secrets}

The [IBM Detect Secrets](https://github.com/IBM/detect-secrets) tool identifies where secrets are visible in app code. More information on setting up your repo for the scan is available [here](/docs/devsecops?topic=devsecops-cd-devsecops-detect-secrets-scans).

## Scans and checks in compliance checks
{: #cd-devsecops-cd-pipeline-compliancechecks}

| Scan or check |  Description |
|---------|------------|
| Code Risk Analyzer vulnerability scan | Finds vulnerabilities for all of the app package dependencies, container base images, and operating system packages. Uses the Code Risk Analyzer tool. |
| Code Risk Analyzer CIS check |  Runs [configuration checks](/docs/cli?topic=cli-cra-cli-plugin#deployment-command) on Kubernetes deployment manifests. Uses the Code Risk Analyzer tool.|
| Code Risk Analyzer Bill of Material (BOM) check | The BOM for a specified repo that captures the pedigree of all of the dependencies. This BOM is collected at different granularities. For example, the BOM captures the list of base images that are used in the build, the list of packages from the base images, and the list of app packages that are installed over the base image. The BOM acts as a ground truth for the analytic results and can potentially be used to enforce policy gates. Uses the Code Risk Analyzer tool. |

| Repository compliance checking | Checks that branch protection settings are correct. |
{: caption="Compliance scans and checks" caption-side="top"}

These scripts are run on all of the app repos that the pipeline is aware of. To add repos to these scans, use the [`pipelinectl`](/docs/devsecops?topic=devsecops-devsecops-pipelinectl) interface that is provided in your setup stage.

For more information about the expected output from user script stages, see [Custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize).

## Task jobs
{: #cd-devsecops-pipeline-jobs}

| Task job |  Description |
|---------|------------|
| `code-pr-start` | Clones app and DevSecOps repos, and sets the initial pending state for status checks on {{site.data.keyword.gitrepos}} repos. |
| `code-setup` | The placeholder for a user-defined setup custom script where the user can complete their pipeline setup. |
| `code-detect-secrets` |  Runs the detect secrets scan to identify where secrets are visible in app code. |
| `code-unit-tests` |  The placeholder for a user-defined test custom script where the user can run their own tests. |
| `code-pr-finish` | Runs all of the required compliance checks, comments the results to the pull request, and sets their result on the {{site.data.keyword.gitrepos}} repos. |
{: caption="Compliance scans and checks" caption-side="top"}

## Using PR/MR changes for pipeline config repo
{: #cd-devsecops-app-repo-pipeline-config-repo}

If the PR pieline should consider the changes of the config files/scripts coming from the PR/MR then, the config repo and branch should be empty which can be set as below:
- The env variables `one-pipeline-repo` , `one-pipeline-config-repo` and `pipeline-config-repo` are empty or not specified in the env variables 
- The env variables `one-pipeline-config-branch` and `pipeline-config-branch` are empty or not specified in the env variables.

## Merge pull requests with issues
{: #cd-devsecops-merge-pr}

You can use Administrator rights to merge pull requests with failed status checks to the repo. However, these pull requests register a `failure` result in their evidence for the failing task. This result is included in the evidence summary and change request description, and impacts the final compliance score on the Security and Compliance Center.

## Enable evidence collection in PR pipeline
{: #cd-devsecops-pr-evidence-collection}

PR pipeline supports evidence collection along with issue management. By default, PR pipeline does not collect evidences or open any issues but users can opt-in to this feature. In order to enable evidence collection and issue management, set the environment variable `collect-evidence-in-pr` as one of the following enum:

- `none`: (default) Set `collect-evidence-in-pr` to `none`, to prevent evidence collection in PR pipeline.
- `all`: Set `collect-evidence-in-pr` to `all` to collect all evidences irrespective of the status of the PR pipeline. Issues will be opened, updated or closed according to `collect-evidence` script.
- `success`: Set `collect-evidence-in-pr` to `success` to collect evidences only if the entire PR pipeline ran successfully. If the PR pipeline failed, evidences will be not be collected or published to the evidence locker and issue management will not be taking place.

Note: Since, PR pipelines are generally run quite frequently, choosing the correct mode of `collect-evidence-in-pr` will save from unnecessary evidence collection. It is suggested to select the `success` mode during development phase or in cases where failures are anticipated, in order to prevent evidence collection if the pipeline fails. 

If the PR pipeline triggers an Async pipeline, `collect-evidence-in-pr` set to `success` mode is not supported. Incase the PR pipeline triggers an async pipeline, set `collect-evidence-in-pr` to `all` in order to collect evidences.
{:  note}

Note: If the application repository is a GitLab repository and the MR (merge request) being contributed is from a forked repository, user will need to provide a value for `git-token` env variable which has access to both the source and the target repositories to set the proper statuses on the merge request. This means the git token would need to belong to a user who is a contributor in both the base repository and the forked repository and the token has permissions to set status on a commit.
{:  note}

## Surfacing CVE in PR
{: #surfacing-cve-in-pr}

When a PR is created and vulnerabilities are found by the PR pipeline, the pipeline adds the vulnerability information like severity, the cve identifier, package along with its description and the fix if available as a comment. This enables users to quickly check the vulnerabilities to be fixed in the PR instead of having to go through the pipeline logs.

An opt-in flag `opt-in-pr-updates`, is available to enable / disable this feature in the PR pipeline. This is enabled by default.

## Setting-up a PR pipeline to handle Github Merge Queue PR's
{: #merge-queue-pr}

A Merge Queue is a Github feature that helps increase velocity by automating pull request merges into a busy branch and ensuring the branch is never broken by incompatible changes. More on setting-up and using Github Merge Queue [here](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/managing-a-merge-queue#about-merge-queues).

### Create a new Git Trigger
Create a new Git Trigger or duplicate an existing one. Keep all default settings, only override the following properties:
- `Name`: prefer to have a trigger name that reflects Merge Queue context - ex: `PR - Merge Queue`
- `EventListener`: select `pr-listener-merge-queue`
- `Trigger on`: select `CEL filter`
  - `CEL filter`: enter `body.action == 'checks_requested'`

Merge Queue PR's use ephemeral branches - created "on the fly" when the original PR is added to the Merge Queue - from which the Merge Queue PR is created. As a consequence, corresponding Git event payload (that triggers the Pull Request pipeline) does not contain any information about PR URL or HTML URL.
{: note}

Irrelevant in Merge Queue context, the following DevSecOps features must be disabled by adding corresponding text properties:
- `skip-merge-pr-to-base`: should be set to `true` (default false)
- `opt-in-pr-updates`: should be set to `0` (default 1)

Save the trigger.

### Testing the Merge Queue trigger
- Create a new Pull Request against the main branch of the application source code repository.
- Observe: the "standard" DevSecOps PR pipeline is triggered
- Still on the PR page, click on `Merge when ready`, then click on `Attempt merge when ready` - this will `enqueue` the PR to the Merge Queue once the "standard" PR has completed.
- Wait for the "standard" DevSecOps PR to complete successfully
- Observe: once PR pipeline has completed, PR is added to Merge Queue and Merge Queue PR is triggered:
  - Wait for the Merge Queue PR to complete
  - If successful, PR is merged with comment like `Merged via the queue into main with commit abcdefg`
  - If not successful (ex: failed compliance check), PR will not be auto-merged, and removed from Merge Queue.

---

## PR Payload Overview

### Payload
{: #cd-devsecops-payload}

A **payload** is a generic term used to describe a structured block of data, usually in JSON format—that is transmitted as part of an event or request. Payloads are commonly used in webhooks, APIs, and automation systems to convey relevant information in a machine readable form.

Payloads can represent a wide range of content depending on the context—for example, build metadata, user activity, issue updates, or, in this case, pull request details.

### PR Payload
#cd-devsecops-pr-payload

The **PR Payload** is a specific instance of a payload that encapsulates metadata and contextual information about a pull request (PR). It is generated when a pull request event occurs and provides structured access to key attributes related to that PR.

This payload includes a wide range of data such as:

* PR title and description
* Author and reviewers
* Source (head) and target (base) branches
* Commit history and SHA references
* Repository details
* Timestamps, labels, statuses, and more

While the full payload contains extensive information, only a **specific subset of fields** is currently being extracted and exposed as environment variables for use in pipelines, scripts, and configuration files.

## Environment Properties Extracted from the PR Payload

The following environment variables are currently derived from the PR Payload and actively used:

| Variable Name      | Description                                             |
| ------------------ | ------------------------------------------------------- |
| `head-branch`      | The source branch of the PR (branch being merged from)  |
| `head-sha`         | The commit SHA of the latest commit on the head branch  |
| `head-repo`        | The repository from which the PR originates             |
| `base-branch`      | The target branch of the PR (branch being merged into)  |
| `base-repo`        | The full reference to the base repository               |
| `base-repo-name`   | The name of the base repository                         |
| `base-repo-owner`  | The owner (user or organization) of the base repository |
| `commit-timestamp` | The timestamp of the most recent commit in the PR       |
| `pr-url`           | The API URL of the pull request                         |
| `pr-html-url`      | The web (HTML) URL of the pull request                  |
| `pr-title`         | The title of the pull request                           |
| `action`           | status of PR                                            |
| `base_ref`         | target branch of PR                                     |

These values are injected as environment variables to simplify access during execution in automated workflows.

The PR Payload includes **many additional fields** beyond what is listed above. However, only the extracted subset is currently leveraged for operational purposes. If needed, access to the complete payload can be provided for advanced use cases or future expansion.

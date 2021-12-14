---

copyright:
  years: 2021
lastupdated: "2021-12-08"

keywords: DevSecOps

subcollection: devsecops

---

{:shortdesc: .shortdesc}
{:table: .aria-labeledby="caption"}
{:external: target="_blank" .external}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:download: .download}
{:help: data-hd-content-type='help'}
{:support: data-reuse='support'}

# Adding test and build steps to pipelines
{: #cd-devsecops-add-pipeline-steps}

You can connect your existing test and build flow to the continuous integration pipeline by adding the results of your new or existing test and build scripts to the DevSecOps pipeline continuous integration flow.
{: shortdesc}

You can use the following stages within the continuous integration pipeline to add test and build steps:

* Setup stage
* Test stage
* Containerize stage (build)
* Release stage

## Setup
{: #cd-devsecops-add-pipeline-setup}

You can use the Setup stage to set up your test and build environment and pull information into the pipeline. For example, you can use multiple app-related repos in a single build. You can clone all of the repos that you need and make the pipeline aware of those repos in compliance-related checks and scans.

The default app repo cloned by the pipeline internally and added to the pipeline by using the `save_repo pipelinectl` interface with the reference name `app-repo`. The default repo is either provided by the repo pipeline UI parameter or selected by its toolchain binding name, if you set up the pipeline from the toolchain template.

If you want to use more repos, clone them in the setup stage and use the same `save_repo` interface to add them to the pipeline.

### Example
{: #cd-devsecops-add-pipeline-setupexample}

```bash
#
# your scripts cloning the repositories
#
# make sure you prepare or export the following data from each cloned repository:
# - repository URL
# - path where it was cloned, relative to the $WORKSPACE path
# - cloned branch
# - latest commit hash
#
your_clone_scripts

#
# when cloning is complete
# use `save_repo` to add these information to the pipeline
# repo-reference-name can be any name, it is used to refer to the stored repo
#
save_repo <repo-reference-name> \
    url="${REPO_URL}" \
    path="${REPO_PATH}" \
    branch="${CLONED_BRANCH}" \
    commit="${LATEST_GIT_COMMIT}"
```    
    
This way the rest of the pipeline can scan these repos for compliance violations and vulnerabilities.

Paths saved by using `save_repo` must be relative to the workspace path.
{: important}

You don't need to install the `pipelinectl` tool for your scripts or base images, the reference pipeline provides the binaries for the context of your script.

## Test
{: #cd-devsecops-add-pipeline-test}

This stage is where you run your tests on your code repos. You can access your repos added in the setup stage using the `list_repos` and `load_repo` pipelinectl interfaces.

### Example
{: #cd-devsecops-add-pipeline-test-example}

```bash
exit_code=0

#
# `list_repos` returns the list of the reference names of saved repos
#
list_repos | while IFS= read -r repository ; do

    #
    # load_repo returns a property of a saved repository
    #
    # Usage: 
    # load_repo <repo-reference-name> <property> 
    # 
    url="$(load_repo "$repository" url)"
    sha="$(load_repo "$repository" commit)"
    branch="$(load_repo "$repository" branch)"
    path="$(load_repo "$repository" path)"

    #
    # use your repos to test, etc
    #
    run_tests 
    result=$?
    
    if [ $result != 0 ]; then
        exit_code=$result
    fi
done

exit $exit_code
```

The unit-test compliance control is determined by the exit code of the stage script itself. If your tests pass, exit with 0, if not, return a non-zero exit code at the end.

### Saving results
{: #cd-devsecops-add-pipeline-save-results}

Your tests might generate some report artifacts, such as a test results JSON or XML. To attach those to the created compliance evidence as evidence artifacts, use the save_result pipelinectl interface in this stage.


```bash
#
# run tests with some test suite runner, and save output to results.json
#
test_runner -o results.json

#
# save the result for the pipeline, so it can attach it to the unit test evidence
#
save_result test results.json
```

The first parameter of save_results must be the DevSecOps pipeline config stage name, like test, scan-artifact or acceptance-test. Otherwise the evidence collector won't be able to find it and attach it to the proper piece of evidence.
{: important}

Using the `save_result` pipelinectl interface ensures that the pipeline finds your result artifacts, they are uploaded to the evidence locker, and attached to the compliance evidence that is created by the pipeline.

Example evidence created for the unit tests while using `save_result`:

```bash
{
  "evidence_type_id": "com.ibm.unit_tests",
  "evidence_type_version": "1.0.0",
  "date": "2021-03-31T07:41:31.881Z",
  "result": "success",
  "pipeline_id": "8c2b6750-91db-45fb-98ee-51684843b821",
  "pipeline_run_id": "89a04de9-2795-4e8e-be90-52a92ac7f9c1",
  "issues": [],
  "artifacts": [
    {
      "url": "https://s3.us-south.cloud-object-storage.appdomain.cloud/cos-bucket-name/ci/89a04de9-2795-4e8e-be90-52a92ac7f9c1/artifacts/compliance-app-COMPACT-20210218231513608/unit-tests-results.json_d9619521e7444fef0ff052e59fd54049",
      "hash": "d9619521e7444fef0ff052e59fd54049"
    }
  ],
  "toolchain_crn": "crn:v1:bluemix:public:toolchain:us-south:a/40111714589c4f7099032529b26a7a63:39d4f080-55e5-42ee-a787-26d936fb2b97::",
  "log": [
    {
      "url": "https://cloud.ibm.com/devops/pipelines/tekton/8c2b6750-91db-45fb-98ee-51684843b821/runs/89a04de9-2795-4e8e-be90-52a92ac7f9c1/code-unit-tests/run-stage?env_id=ibm:yp:us-south",
      "hash": null
    },
    {
      "url": "https://s3.us-south.cloud-object-storage.appdomain.cloud/cos-bucket-name/ci/89a04de9-2795-4e8e-be90-52a92ac7f9c1/artifacts/logs/code-unit-tests/run-stage.log_dae902fb1455b1fc9c565273aa4fe1bc",
      "hash": "dae902fb1455b1fc9c565273aa4fe1bc"
    }
  ]
}
```

## Build or containerize
{: #cd-devsecops-add-pipeline-build}

In this stage you can build your artifacts. The pipeline provides some default features for docker image type artifacts, but you're able to build any kind of artifacts here. What is important, is to save the created artifacts for the pipeline, so later it can run scans on it, or use them in your release stage.

To provide information on your built artifacts, use the `save_artifact` pipelinectl interface.

### Example
{: #cd-devsecops-add-pipeline-build-example}

```{: #cd-devsecops-add-pipeline-test-example}
#
# your scripts building the artifact
#
# make sure you prepare or export the following data from each built artifact:
# - type (image for docker images, package for rpms, npm tarballs, etc )
# - full artifact URL with version tag
# - artifact digest
#
your_build_scripts

#
# when the build is complete
# use `save_artifact` to add these information to the pipeline
# artifact-reference-name can be any name, it is used to refer to the stored artifact
#
save_artifact <artifact-reference-name> \
    type=image" \
    name="${IMAGE_URL}" \
    digest="${IMAGE_DIGEST}"
```    

The preferred format for image name is `image-URL:build-tag`, for example, `wcp-compliance-automation-team-docker-local.artifactory.swg-devops.com/compliance-baseimage:2.8.0`.
{: important]

If you build Docker images, use the `save_artifact` interface to send those images for the default built-in image signing and CR VA scanning tasks.

## Release
{: #cd-devsecops-add-pipeline-release}

At the end of the pipeline, the built artifacts must be added to the inventory, so they can be promoted to deployment.
The release stage provides flexibility if you want to add other artifacts to the inventory, such as Helm charts.

In this stage, you can use the CLI `cocoa inventory add` command, and the data from `pipelinectl` commands, to create the inventory entries.

### Example
{: #cd-devsecops-add-pipeline-release-example}

```bash
#
# `list_artifacts` returns the list of the reference names of saved artifacts
#
list_artifacts | while IFS= read -r artifact ; do
    #
    # Add a new value to the inventory repository. `cocoa inventory add` creates a new file with the name option, 
    # if does not exist otherwise overwrites it.
    #
    cocoa inventory add \
        --name="${artifact}" \
        --artifact="$(load_artifact $artifact name)" \
        --repository-url="$(load_repo app-repo url)" \
        --commit-sha="$(load_repo app-repo commit)" \
        --build-number="${BUILD_NUMBER}" \
        --pipeline-run-id="${PIPELINE_RUN_ID}" \
        --version="$(get_env version)" \
        --app-artifacts="{ \
            \"signature\": \"$(load_artifact $artifact signature)\", \
            \"provenance\": \"$(load_artifact $artifact name)\"\
        }"
done
```

To use the CLI, you must install it in your scripts, or use a base image that has the CLI pre-installed.

## Related information
{: #cd-devsecops-add-pipeline-related}

* Documentation on stages for user-defined scripts
* API documentation for pipelinectl
* Example test script
* Example build scripts
* Example release script
* The default built-in image signing script
* The default built-in VA scan checker script

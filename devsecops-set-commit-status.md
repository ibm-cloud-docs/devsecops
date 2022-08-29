---

copyright:
  years: 2022
lastupdated: "2022-08-26"

keywords: DevSecOps, set-commit-status, script, 

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# set-commit-status script
{: #devsecops-set-commit-status}


The script set status checks for commits, either for Pull (or Merge) Requests or already merged commits in a main branch.

The `set-commit-status` script is provided by the pipeline. It does not need to be installed. The script has the following dependencies:

- bash
- `libstdc++` shared library 
- `libgcc` shared library

Make sure that the dependencies are installed in the base image that uses this tool for setting Git commit statuses.

## Usage
{: #set-commit-status-usage}

The script `set-commit-status` requires the following parameters:

- `--repository`  
   The repository URL. Make sure that the repository is added to the toolchain as an integration.
- `--commit-sha`  
   The full SHA hash of the commit to set the status on
- `--state`  
   State of the check. The state can be one of the following values:
   `failure`, `pending`, `success`, or `error`
- `--description`
   Short description of the status check

The following parameters are optional:

- `--context`  
   Context of the check, default is `devsecops/<CUSTOM STAGE NAME>`  
   For example: `devsecops/unit-tests`
- `--task-name`  
   Current pipeline task name, default is `"<CUSTOM STAGE NAME>"`
   For example: `unit-tests`
- `--step-name`  
   Current pipeline step name, default is `run-stage`

Use the following command to get help:

```text
set-commit-status --help
```
{: pre}

### Example usage
{: #set-commit-status-usage-example}

```bash
set-commit-status \
  --repository "https://url-to.application-repo.git" \
  --commit-sha "49756b0c7e6e89516caa76ebbc697c2d55852fbc" \
  --state "success" \
  --description "Unit tests finished running." \
  --context "tekton/code-unit-tests" \
  --task-name "code-unit-tests" \
  --step-name "run-stage"
```
{: codeblock}


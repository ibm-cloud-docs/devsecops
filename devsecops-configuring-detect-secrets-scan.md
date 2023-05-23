---

copyright:
  years: 2022, 2023
lastupdated: "2023-05-23"

keywords: DevSecOps, IBM Cloud, compliance

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Configuring Detect Secrets scan
{: #detect-secrets-scan}

Detect-secrets is a client-side security tool built for developers and is designed to detect secrets within a codebase for the sake of remediation and prevention of secret leaks.

https://github.com/Yelp/detect-secrets



Detect-secrets scans the entire current codebase and outputs a snapshot of currently identified secrets, which is used to audit the identified secrets as true positives or false positives and the devsecops toolchains would accordingly create git issues for the same.


## Use and importance of baseline file

A baseline file is the result snapshot of a detect secret scan run, it is used to list potential secrets on every run and mark false positives which will be avoided from flagging as issues in the subsequent runs.

The baseline file is meant to be updated periodically, to update and audit the baseline file, which should get committed and pushed back into the repo. By including these false positives in the baseline, it ensures that no issues are created for these existing files during future scans.

## Conditions of Secrets Found

When a detect-secrets scan has been completed, the detected "secrets" are yet to be classified as real or false positives.
3 different conditions are used to classify a potentially detected secret.

1. **Unaudited** - A secret is categorized as unaudited if it is a potential secret, that needs to be reviewed in the audit process.

2. **Audited as real** - A secret is considered audited as real, if the secret has been marked as a true positive during an audit and the baseline file still contains the secret.

3. **Live** - This means that the mentioned secret has been confirmed as a live secret by the plugins utilized by Detect Secrets.

## Managing Baseline

The `detect-secrets scan` command generates a baseline file or updates the existing one to contain a snapshot of all detected potential secrets of the run. 

The `detect-secrets audit` command is used for manually labelling secrets, this can be used to narrow down your checklist of secrets to migrate.

Doing an audit gives the chance to distinguish the detected secrets between true and false positives, this will update the baseline file so the false positives can be excluded from the future scan runs.

#### Steps to update Baseline

Make sure detect-secrets is installed locally on your system, follow the steps mentioned here to set up `detect-secrets` on your local terminal. 

`pip install detect-secrets`

or install via brew

`brew install detect-secrets`



Run the following commands to scan and generate / update the baseline file in your repo.

1. Scan the repo folder with this command 

`detect-secrets scan --baseline .secrets.baseline`



2. Review and audit the baseline file created with the scan step using this command
 `detect-secrets audit .secrets.baseline`

This will bring up an interactive terminal to mark false positives, where you can indicate (**y**)es if the secret found is an actual secret or (**n**)o if it is a false positive.

Commit and push the `.secrets.baseline` file back to the repo, this process has to be done locally on a periodic basis.


## Available parameters for Detect Secrets scan
{: #detect-secrets-parameters}

If you have an existing baseline file, the scan will use the existing configuration, your previous auditing results and settings will not be overwritten. If no baseline file exists, a temporary one will be created automatically for the run.

The `detect-secrets-baseline-filename` parameter allows you to specify a custom filename for the baseline file used by Detect Secrets. By default, Detect Secrets looks for a file named `.secrets.baseline` in the repository root directory. However, if you have named your baseline file differently, you can provide its filename using this parameter.

The `exclusion-list` is an environment property that will override the default exclusion list when a run is done without an existing baseline file. This is useful to narrow down on specific files to ignore, to avoid issues being created linked to them.

The `detect-secrets-verbose` property will output the current file being scanned to the logs, this is useful if a file takes too long to scan, as that can be excluded in the exclusion list or updated in the baseline file itself. 

|Name | Type |Description |Required or Optional |
|:----------|:---------|:------------------------------|:------------------|
| `detect-secrets-baseline-filename` | String |The name of the baseline file in your app repository. Defaults to `.secrets.baseline` | Required if your baseline file is not the default name of `.secrets.baseline`. |
| `exclusion-list` | String | A regex list of files to be excluded in the detect-secrets scan. Defaults to `requirements.txt|go.mod|pom.xml|build.gradle|package-lock.json` | Optional, This list will only be used to override the general exclusion list when there is no `.secrets.baseline` file present. |
| `detect-secrets-verbose` | String | This will emit which file is currently being scanned. Once you've identified the file taking too long, you can update your baseline or use `--exclude-files` option to skip the offending file(s). Defaults to `0`  | Optional, Debug flag 0 - off, 1 - on | 

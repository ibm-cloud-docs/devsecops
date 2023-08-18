---

copyright:
  years: 2023, 2023
lastupdated: "2023-08-15"

keywords: DevSecOps, IBM Cloud, compliance

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Configuring detect-secrets scan
{: #devsecops-detect-secrets-scan}

Detect-secrets is a client-side security tool that detects secrets within a codebase to remediate and prevent secret leaks.
{: shortdesc}

For more information, see [IBM/detect-secrets](https://github.com/IBM/detect-secrets){: external}.

Detect-secrets scans the entire current codebase and outputs a snapshot of currently identified secrets. The secrets list is then used to audit the identified secrets as true positives or false positives, the DevSecOps toolchains then create Git issues for the true positives.


## Using the baseline file
{: #devsecops-detect-secrets-baseline}

A baseline file is the snapshot of a detect secret scan. The file lists potential secrets on every run and marks false positives, which are not flagged as issues in subsequent runs.

The baseline file must be updated periodically. Update and audit the baseline file by committing it and pushing it back into the repo. Including these false positives in the baseline ensures that no issues are created for these existing files during future scans.

## Classifying secrets
{: #devsecops-detect-secrets-classify}

When a detect-secrets scan is complete, the detected secrets have yet to be classified as real positives or false positives. 

Three conditions classify a potentially detected secret:

* **Unaudited** - A secret is categorized as unaudited if it is a potential secret that must be reviewed in the audit process.
* **Audited as real** - A secret is categorized as audited as real if it is marked as a true positive during an audit and the baseline file still contains the secret.
* **Live** - A secret is categorized as live if the secret is confirmed as a live secret by the plug-ins that are used by detect-secrets.

## Managing the baseline
{: #devsecops-detect-secrets-base}

The following commands manage the secrets baseline:

* The `detect-secrets scan` command generates a baseline file or updates the existing file to contain a snapshot of all detected potential secrets of the run. 
* The `detect-secrets audit` command labels secrets so that you can narrow down your checklist of secrets to migrate.

Audits identify the detected secrets as either true or false positives, you can then update the baseline file so the false positives can be excluded from the future scan runs.

## Updating the baseline
{: #devsecops-detect-secrets-update}

Take the following steps to update your baseline:

1. Install detect-secrets on your local computer:

   `pip install --upgrade "git+https://github.com/ibm/detect-secrets.git@master#egg=detect-secrets"`

1. Run the following commands to scan, generate, or update the baseline file in your repo:

   1. Run the following command to scan the repo folder:

      `detect-secrets scan --update .secrets.baseline`

      You can use the `--exclude-files` flag when you want to ignore certain files that are known to contain false positives or do not require scanning. The exclusion rules are based on regular expressions format.

      To perform a scan with file and folder exclusions, use the following command:

      `detect-secrets scan --update .secrets.baseline --exclude-files '<folder_to_ignore>|<file_to_ignore>'`

      Example : `detect-secrets scan --update .secrets.baseline --exclude-files "package-lock.json|go.sum"`

      The list of excluded files is recorded in the baseline file. 
      
      If no `--exclude-files` option is provided in subsequent scans, detect secrets automatically respects the existing exclude list from the baseline file.
      
      However, if you want to specify a new exclude list during the `detect-secrets scan`, the new list overwrites the existing exclude list in the baseline file.


   1. Run the following command to review and audit the baseline file that is created with the scan step:

      `detect-secrets audit .secrets.baseline`

      This command brings up an interactive terminal to mark false positives. You can indicate **(y)es** if the secret found is an actual secret or **(n)o** if it is a false positive.

   1. Commit and push the `.secrets.baseline` file back to the repo, his process must be done locally on a periodic basis.


## Detect-secrets scan parameters
{: #devsecops-detect-secrets-params}

If you have an existing baseline file, the scan uses the existing configuration. Your previous auditing results and settings are not overwritten. If no baseline file exists, a temporary baseline is created automatically for the run.

The `detect-secrets-baseline-filename` parameter specifies a custom file name for the baseline file that is used by detect-secrets. By default, detect-secrets looks for a file that is named `.secrets.baseline` in the repository root directory. However, if you name your baseline file differently, you can provide its file name by using this parameter.

The `detect-secrets-exclusion-list` parameter overrides the default exclusion list when a run is done without an existing baseline file. This parameter identifies files to ignore so that issues are not created that are linked to them.

The `detect-secrets-image` parameter can be used to specify a different detect-secrets image to use, such as a custom image or a specific version of the official detect-secrets image.

The `detect-secrets-verbose` parameter, when set to 1, logs the name of the current file that is being scanned.

|Name | Type |Description |Required or Optional |
|:----------|:---------|:------------------------------|:------------------|
| `detect-secrets-baseline-filename` | String |The name of the baseline file in your app repository. Defaults to `.secrets.baseline`. | Required if your baseline file is not the default name `.secrets.baseline`. |
| `detect-secrets-exclusion-list` | String | A regex list of files to be excluded in the detect-secrets scan. Defaults to `requirements.txt\|go.mod\|go.sum\|pom.xml\|build.gradle\|package-lock.json`. | Optional, This file list overrides the general exclusion list only when there is no `.secrets.baseline` file present. |
|`detect-secrets-image` | String | Specifies an alternative detect-secrets image, including custom images or specific versions of the official image. | Optional |
| `detect-secrets-verbose` | String | Outputs the name of the file that is currently that is being scanned. Defaults to `0`.  | Optional, Debug flag 0 - off, 1 - on. | 
{: caption="Table 1. Secrets-scan parameters" caption-side="top"}


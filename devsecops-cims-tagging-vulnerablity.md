---

copyright:
  years: "2024"
lastupdated: "2024-06-19"

keywords: DevSecOps, IBM Cloud, compliance, cims, multiscan, container image

subcollection: devsecops-working

---

{{site.data.keyword.attribute-definition-list}}

# Tagging vulnerability results as false positives or other classifications
{: #cims-restricting-vulnerability-results}

You can annotate the vulnerabilities that CIMS identified with one or more tags that the [IBM Software Security Centre of Excellence (SCoE) team](https://w3.ibm.com/w3publisher/cims/recipes/cve-tagging/standard-cve-tag-names){: external} defines. Use the [Vulnerability Intelligence Platform UI](https://pages.github.ibm.com/scoe/vip-documentation/vip.html#using-vulnerability-intelligence-platform-vip){: external} to complete the process. After you apply tags to vulnerabilities and save them to a tagging master manifest file, filter out one or more tags from your filtered vulnerability results in each pipeline run.

The tagging and filtering process is as follows:

## **Step 1**: Creating the tagging scan manifest file
{: #cims-tagging-manifest-file}

The tagging scan manifest file includes a row for every finding, standard CVE or otherwise that is discovered across all images that CIMS scans during the current pipeline run.

Instruct the pipeline to save the tagging scan manifest file to a GitHub repository or to {{site.data.keyword.cos_full}} by setting the tagging manifest options pipeline environment properties. Refer to the following Tagging manifest options section.

To save the tagging scan manifest file to a different file name or path within the designated repository or {{site.data.keyword.cos_short}} bucket, set the `multiscan-tagging-scan-manifest` pipeline environment property to the path and file name that is relative to the root of the repository or bucket.

- For example: `multiscan-tagging-scan-manifest`=`scan-manifests/trigger-1-scan-manifest.json`

This step is required if you want the pipeline run to produce a launch-in-context link to the Vulnerability Intelligence Platform UI.
{: important}

The recommended best practice is to save this file to {{site.data.keyword.cos_short}}, or to a GitHub repository that is separate from your toolchain configuration repository. Check out the Tagging manifest options section.

To save the tagging scan manifest file to a specific GitHub repository branch, set the `multiscan-tagging-manifest-branch` pipeline environment property to the name of the repository branch. By default, the pipeline saves the file to the default branch for the repository.

## **Step 2**: Importing and defining the name and path of the tagging master manifest file
{: #cims-tagging-manifest-file-import}

The Vulnerability Intelligence Platform UI produces the tagging master manifest file when one or more vulnerabilities are tagged. This file must be imported into each pipeline run if you are filtering one or more tags from your scan results. Before CIMS scans the images in each pipeline run, it imports the tags from the tagging master manifest file, if it exists. These tags appear in the scan result JSON (under the `tags` element) and CSV files (under the `tags` column) that were uploaded to your evidence repository.

Set the `multiscan-tagging-master-manifest` pipeline environment property to the relative path and file name of the tagging master manifest file, as relative to the root of the repository or storage bucket.

The tagging master manifest file uses the same GitHub repository or {{site.data.keyword.cos_short}} bucket as the tagging scan manifest file.
{: note}

## **Step 3**: Create or edit the tagging master manifest file by using the Vulnerability Intelligence Platform UI
{: #cims-tagging-manifest-file-create-edit}

You can create or edit the tagging master manifest file by using the [Vulnerability Intelligence Platform UI](https://vulnmgr.dal2a.ciocloud.nonprod.intranet.ibm.com/){: external}. For more information, see the [Vulnerability Intelligence Platform UI documentation](https://pages.github.ibm.com/CCS-CISO-DevSecOps/psirt-cims-integration/vip.html#using-vulnerability-intelligence-platform-vip){: external} for detailed steps and instructions.

## **Step 4**: Filtering out tags from multiscan results
{: #cims-filter-tag-multiscan-results}

Set the `multiscan-filter-tag-names` pipeline environment property to specify which tags from the tagging master manifest file are to be filtered out of the scan results. Any vulnerability row that contains one or more of the specified tags is not included. If you're specifying multiple tags, separate each tag by using a semicolon.

For example, if you set `multiscan-filter-tag-names`=`false-positive(scanner-error);false-positive(latent-vulnerability)`, any vulnerability that is tagged as `false-positive(scanner-error)` or `false-positive(latent-vulnerability)` is excluded from the filtered scan results that are used by issue management.

## Tagging manifest options
{: #cims-tagging-manifest-options}


The following tables provides information on how to tag a manifest file.


|                 Property                | Required |              Default            | Description |
| --------------------------------------- | --------- | ------------------------------- | ----------- |
| `multiscan-tagging-manifest-save`         | No        |                                 | The indication of whether the tagging scan manifest file is to persist, in addition to being persisted with scan results. Set to any nonzero length string to enable this property. |
| `multiscan-tagging-manifest-storage-type` | No        | `cos`                           | The tagging manifest storage host. Set to either `github` or `cos`. |
| `multiscan-tagging-scan-manifest`         | No        | `tagging-scan-manifest.json`    | The relative path and file name where the tagging scan manifest file is to be persisted, for example, `scan-manifests/tagging-scan-manifest.json`.  |
| `multiscan-tagging-master-manifest`       | No        | `tagging-master-manifest.json`  | The relative path and file name where the tagging master manifest file is to be imported from, for example, `master-manifest/tagging-master-manifest.json`. |
{: caption="Table 1. Tagging manifest options" caption-side="bottom"}


The tagging scan manifest can be persisted to, and the tagging master manifest imported from, a GitHub Enterprise repository or to {{site.data.keyword.cos_short}}. Set the pipeline environment property `multiscan-tagging-manifest-storage-type` to the default, `cos`, or `github`.

- GitHub Enterprise repository storage options

|             Property              | Required |              Default            | Description |
| --------------------------------- | --------- | ------------------------------- | ----------- |
| `multiscan-tagging-manifest-repo`   | Yes       | The pipeline configuration repository. | The URL for the repository, for example `https://github.ibm.com/myOrg/multiscanner-tagging-manifests-repo.git`. Required if `multiscan-tagging-manifest-storage-type`=`github`. |
| `multiscan-tagging-manifest-branch` | No        | The repository default branch.   | The repository branch where the tagging manifest files are read from and written to, for example `myTaggingBranch`. |
{: caption="Table 2. GitHub Enterprise repository storage options" caption-side="bottom"}

- {{site.data.keyword.cos_full}} options

|         Property          | Required |   Default   | Description |
| ------------------------- | --------- | ------------| ----------- |
| `cos-endpoint`              | Yes       |             | The name of the Cloud Object Storage endpoint. Required if `multiscan-tagging-manifest-storage-type`=`cos`. |
| `multiscan-cos-bucket-name` | No        |             | The name of the Cloud Object Storage bucket. This value overrides the value for the `cos-bucket-name` pipeline environment property. |
| `cos-bucket-name`           | No        |             | The name of the Cloud Object Storage bucket. This pipeline environment property can be used in pipelines where Cloud Object Storage is already configured. |
{: caption="Table 3. {{site.data.keyword.cos_short}} options" caption-side="bottom"}

For more information, see the [{{site.data.keyword.cos_full}} documentation](/docs/cloud-object-storage).

The {{site.data.keyword.cos_short}} bucket must be readable and writable by using the {{site.data.keyword.cloud_notm}} API key that the `ibmcloud-api-key` pipeline environment property specifies.



Multiple parallel pipeline runs that use the same `multiscan-tagging-scan-manifest` value can fail to persist to the database successfully, and the scan results from one scan can overwrite the results from other scans that were already in progress.
{: important}

---

copyright:
  years: 2023, 2023
lastupdated: "2023-03-24"

keywords: DevSecOps, IBM Cloud, compliance, Contrast

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Configuring Contrast SAST scans
{: #cd-devsecops-contrast-sast-scans}

The Contrast static application security testing (SAST) script runs the source code scanning in your DevSecOps pipeline and collects evidence based on the scan results.
{: shortdesc}

The Contrast SAST scan runs for each source repository in your pipeline that is returned by the `list_repos` method. For more information, see [list_repos](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#list_repos).

To scan an application, you must upload binary packages to the Contrast SaaS. The Contrast SAST tool observes the data flows in the source code and identifies vulnerabilities in the custom code. After you fix any issues, run the scan again to verify that your code changes removed those vulnerabilities.

For more information on supported languages in SaaS instance, see [Contrast Scan supported languages](https://docs.contrastsecurity.com/en/scan-supported-languages.html){: external}.

Before you run the script, follow the instructions in [View your API keys](https://docs.contrastsecurity.com/en/personal-keys.html){: external} to get the required parameters.






## Running the script
{: #cd-devsecops-contrast-sast-run-script}



You must use the `save_repo` property to register least one source repository registered in `pipelinectl`.

### Required Contrast SAST parameters
{: #cd-devsecops-contrast-sast-params-req}

The parameters in Table 1 are required regardless of the language that you are using.

| Parameter name | Description |
|-|-|
| `contrast-api-key` | Use the `API Key` that is under your profile page in the Contrast dashboard. |
| `contrast-auth-header` | Click **Copy** on the **Authorization Header** that is under your profile page in the Contrast dashboard. |
| `contrast-base-url` | The base URL of the Contrast SAST API, which is `https://app.contrastsecurity.com/Contrast/api/sast/v{version_number}`. To replace the `{version_number}`, go to [Contrast API documentation](https://api.contrastsecurity.com/){: external} and select **Scan** for Contrast SAST. You can see a specific version number. |
| `contrast-org-id` | The identifier of the organization. You can get `Org ID` from your profile page in the Contrast dashboard. |
| `contrast-service-key` | Use the `Service Key` that is under your profile page in the Contrast dashboard. |
| `contrast-username` | This is the Contrast login username.  |
| `contrast-sast-project-name` | Name that appears in the Contrast dashboard. |
| `opt-in-contrast-sast` | Set this parameter to any nonempty string value to include the Contrast SAST scan as part of the Compliance Checks pipeline stage. |
{: caption="Table 1. Required Contrast SAST parameters" caption-side="top"}



### Optional Contrast SAST parameters
{: #cd-devsecops-contrast-sast-params-opt}

| Parameter name | Default value | Description |
|-|-|-|
| `contrast-sast-print-scan-results` | `false` (property is not set) | Enter any nonzero length string to print Contrast SAST scan results for both JSON and CSV files to the stage log. |
{: caption="Table 2a. Optional Contrast SAST parameters" caption-side="top"}

## Evidence and attachments
{: #cd-devsecops-contrast-sast-evid-attach}



The evidence that is created is based on the values in Table 3. The DevSecOps pipeline uploads evidence to the locker and includes the evidence in the evidence summary for Change Requests.

| field | value |
| ----- | ----- |
| `tool type`     | `contrast-sast` |
| `evidence type` | `com.ibm.static_scan` |
| `asset type`    | `repo` |
| `attachments`  | `<scan log>` |
| `attachments`   | `<scan report containing detected list of vulnerabilities in JSON>` |
| `attachments`  | `<scan report containing detected list of vulnerabilities in CSV>` |
{: caption="Table 3. Evidence fields and values" caption-side="top"}

## Debugging and logging
{: #cd-devsecops-contrast-sast-debug-log}

| Parameter name | Default value | Description |
|-|-|-|
| pipeline-debug | 0 | Debug flag 0 off 1 on |
{: caption="Table 4. Debug parameters" caption-side="top"}

## Accessing your scan results
{: #cd-devsecops-contrast-sast-acc-results}

Acess your scan results by using any of the following methods:

- View them on the Contrast user interface.
- Set the `contrast-sast-print-scan-results` pipeline environment property to instruct the pipeline to print the scan report JSON to the stage log.
- Use the DevSecOps CLI command line tool to download your scan results from the evidence locker by using the information printed in the stage log. For more information see these resources:

- [`cocoa locker evidence get`](/docs/devsecops?topic=devsecops-cd-devsecops-cli#locker-evidence-get)
- [`cocoa locker attachment get`](/docs/devsecops?topic=devsecops-cd-devsecops-cli#locker-attachment-get)



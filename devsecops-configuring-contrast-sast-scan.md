
{: caption="Table 1. Required Contrast SAST parameters" caption-side="top"}

### Optional Contrast SAST parameters
{: #cd-devsecops-contrast-sast-params-opt}

| Parameter name | Default value | Description |
|-|-|-|
| `contrast-sast-print-scan-results` | `false` | Set to `true` to print Contrast SAST scan results for both JSON and CSV files to the stage log. |
| `contrast-sast-print-scan-logs` | `false` | Set to `true` to print the Contrast SAST scan log to the stage log. |
| `contrast-sast-base-url` | `https://app.contrastsecurity.com/Contrast/api/sast` | Base URL of the Contrast server API | 
| `contrast-sast-artifactory-token` | This value defaults to the Artifactory ID token in your Toolchain Artifactory integration settings | Your Artifactory `ID Token`. |
| `contrast-sast-artifactory-user` | This value defaults to the user ID in your Toolchain Artifactory integration settings | Username that is associated with your Artifactory ID token. |
| `contrast-sast-scan-labels` | | Add one or more labels to your scan. You can find the labels in the **LABEL** column for the scan in the Contrast UI. Separate multiple labels by using commas. |
| `contrast-sast-scan-timeout` | The default timeout is `30` (30 minutes) | Integrer value representing the time, in minutes, the Contrast SAST scan will spend executing a scan against a source repository before timing out. Set this to an integrer value greater than or equal to `30`. |
| `contrast-sast-project-name` | A concatenated string that includes the GitHub org and repository name, such as `one-pipeline/compliance-commons-internal`. | The scan project name for the scan, as it is displayed in the Contrast UI. |
| `contrast-sast-project-per-branch` | `false` | Set to `true` to append the GitHub repository branch name to the default scan project name created for each source repository scanned. This setting is only used if `contrast-sast-project-name` is not set. |
{: caption="Table 2. Optional Contrast SAST parameters" caption-side="top"}

If you set `contrast-sast-project-name` when you're scanning multiple GitHub repositories, you can find the results in the Contrast UI with the project name that you set. Labels for the GitHub org/repository (for example, `Repo:one-pipeline/compliance-commons-internal`) and GitHub branch (for example, `Branch:master`) are set for each scanned repository so that you can correlate each scan result to its corresponding GitHub repository.
{: note}

If you are scanning multiple branches of the same source repository, even if they are scanned in different pipeline runs, set `contrast-sast-project-per-branch`=`true` and do not set `contrast-sast-project-name`. Otherwise, the most recent source repository branch results will overwrite any previous branch's scan results on the Contrast server.
{: note}


## Evidence and attachments
{: #cd-devsecops-contrast-sast-evid-attach}



The evidence that is created is based on the values in Table 4. The DevSecOps pipeline uploads evidence to the locker and includes the evidence in the evidence summary for Change Requests.

| field | value |
| ----- | ----- |
| `tool type`     | `contrast-sast` |
| `evidence type` | `com.ibm.static_scan` |
| `asset type`    | `repo` |
| `attachments`  | `<scan log>` |
| `attachments`   | `<scan report containing detected list of vulnerabilities in JSON>` |
| `attachments`  | `<scan report containing detected list of vulnerabilities in CSV>` |
{: caption="Table 4. Evidence fields and values" caption-side="top"}

## Debugging and logging
{: #cd-devsecops-contrast-sast-debug-log}

| Parameter name | Default value | Description |
|-|-|-|
| pipeline-debug | 0 | Debug flag 0 off 1 on |
{: caption="Table 5. Debug parameters" caption-side="top"}

## Accessing your scan results
{: #cd-devsecops-contrast-sast-acc-results}

Acess your scan results by using any of the following methods:

- View them on the Contrast user interface.
- Set the `contrast-sast-print-scan-results` pipeline environment property to instruct the pipeline to print the scan report JSON to the stage log.
- Use the CoCoa CLI command line tool to download your scan results from the evidence locker by using the information printed in the stage log. For more information see these resources:

  - [Download the CoCoa CLI command line tool](https://test.cloud.ibm.com/docs/devsecops?topic=devsecops-cd-devsecops-cli#cd-devsecops-cli-binary)
  - [`cocoa locker evidence get`](https://test.cloud.ibm.com/docs/devsecops?topic=devsecops-cd-devsecops-cli#locker-evidence-get)
  - [`cocoa locker attachment get`](https://test.cloud.ibm.com/docs/devsecops?topic=devsecops-cd-devsecops-cli#locker-attachment-get)

For more information on Contrast SAST, see [Contrast Security SAST](https://pages.github.ibm.com/Supply-Chain-Security/Contrast-Security-SAST/){: external}. 

## Handling false positive results

For any given scan project in the [Contrast UI](https://app.contrastsecurity.com/), you can designate vulnerabilities as "Not a Problem". Once you have designated a vulnerability as "Not a Problem" for a given scan project, it will be filtered out of the results returned for subsequent scans against that scan project, and if you have SAST scan issue management enabled, the issue will not be opened/re-opened by the pipeline. 

To designate a vulnerability via the [Contrast UI](https://app.contrastsecurity.com/):
1. Select `Scans` from the top menu bar
2. Select the scan project from the `Scan Projects` table 
3. Select the `Vulnerabilities` tab
4. Click the `Status` field in the table row which matches the vulnerability you wish to designate
5. Select "Not a Problem" 
6. Provide an explanation for the designation in the pop-up dialog, then click the `Override` button

</staging>

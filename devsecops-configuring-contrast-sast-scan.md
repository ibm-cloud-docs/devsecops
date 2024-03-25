
{: caption="Table 1. Required Contrast SAST parameters" caption-side="top"}

### Optional Contrast SAST parameters
{: #cd-devsecops-contrast-sast-params-opt}

| Parameter name | Default value | Description |
|-|-|-|
| `contrast-sast-artifactory-token` | This value defaults to the Artifactory ID token in your Toolchain Artifactory integration settings | Your Artifactory `ID Token`. |
| `contrast-sast-artifactory-user` | This value defaults to the user ID in your Toolchain Artifactory integration settings | Username that is associated with your Artifactory ID token. |
| `contrast-sast-base-url` | `https://app.contrastsecurity.com/Contrast/api/sast` | Base URL of the Contrast server API | 
| `contrast-sast-exclude-json-file` | | **Beta feature** False positives/excludes vulnerabilities JSON file name and path, relative to the root of the pipeline configuration repository |
| `contrast-sast-excluded-close-issues` | `false` | **Beta feature** Set to 'true' to cause the pipeline run to close and previously opened GitHub issues for vulnerabilities detected by the Contrast-SAST scan in the current pipeline run, which match vulnerabilites listed in the file indicated by the `contrast-sast-exclude-json-file` pipeline environment property |
| `contrast-sast-jar-url` | https://na.artifactory.swg-devops.com/artifactory/css-whitesource-team-java-contrast-agent-maven-local/sast-local-scan-runner-1.0.8.jar | URL for the JAR file to be used for Contrast SAST scanning. |
| `contrast-sast-print-scan-results` | `false` | Set to `true` to print Contrast SAST scan results for both JSON and CSV files to the stage log. |
| `contrast-sast-print-scan-logs` | `false` | Set to `true` to print the Contrast SAST scan log to the stage log. |
| `contrast-sast-project-name` | A concatenated string that includes the GitHub org and repository name, such as `one-pipeline/compliance-commons-internal`. | The scan project name for the scan, as it is displayed in the Contrast UI. |
| `contrast-sast-project-per-branch` | `false` | Set to `true` to append the GitHub repository branch name to the default scan project name created for each source repository scanned. This setting is only used if `contrast-sast-project-name` is not set. |
| `contrast-sast-scan-memory` | `2` | Integer value representing the number of gigabytes of memory the Contrast-SAST scanner will allocate to the scan process. |
| `contrast-sast-scan-labels` | | Add one or more labels to your scan. You can find the labels in the **LABEL** column for the scan in the Contrast UI. Separate multiple labels by using commas. |
| `contrast-sast-scan-timeout` | The default timeout is `30` (30 minutes) | Integrer value representing the time, in minutes, the Contrast SAST scan will spend executing a scan against a source repository before timing out. Set this to an integrer value greater than or equal to `30`. |
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

## Configuring scan memory allocation
{: #cd-devsecops-contrast-sast-scan-memory}

By default, the Contrast-SAST scan allocates 2 gigabytes of memory to the scan. You may optionally request a larger amount of memory for the scan by setting the `contrast-sast-scan-memory` pipeline environment property to an integer value greater than `2`. If the requested amount is greater than the amount available to the container executing the scanning stage on the pipeline worker, a warning message will appear in the stage log, and the default memory allocation will be used.  

Example requesting 4 gigabytes of memory to be allocated for the scanner: 
  - `contrast-sast-scan-memory`=`4`

## Enabling false positive results handling automation

The Contrast-SAST product does not currently include the ability to designate vulnerabilities detected by the scan to be excluded from the scan results returned from the Contrast server. This feature processes the vulnerabilities after the scan results are returned from the Contrast server, in order to provide adopters an approach to removing designated vulnerabilities from the scan results uploaded to evidence and used for GitHub issue management. This is a beta feature, and is subject to change or removal in future versions.
{: note}

### Filtering out excluded vulnerabilities detected by the scan 

You may designate vulnerabilities for the pipeline run to filter out from the scan results returned by the Contrast server for all of the source repositories scanned within the pipeline run. Set the `contrast-sast-exclude-json-file` pipeline environment property to the file name and path of the excludes file, relative to the root of the pipeline configuration repository. Any vulnerabilities detected by a scan in the pipeline run which match the excludes/false positives vulnerabilities listed in the excludes file will be:
1. Filtered out of the scan results returned by the pipeline which are uploaded to your evidence repository 
2. Filtered out of the scan results which are passed to [OnePipeline GitHub issue management](static-scan#enabling-onepipeline-github-issue-creation-for-sast-scans), if you have enabled issue management. This prevents a new GitHub issue from being opened for the vulnerability
3. Marked "Not a Problem" on the Contrast server 

Example stage log output for an excluded vulnerability matching a vulnerability detected in the current pipeline run:
```
Vulnerability [Unsafe Cookie@user/session/session.go:29] matches false positives/exclude file vulnerability [Unsafe Cookie@user/session/session.go], and will be filtered out for reason [Golang fixed in incompatibile version]
Marking the VULN_ID: 65f48efbe355ce7170199fe3 from PROJECT_ID: db8b005a-f962-432c-9e21-7a63affc5519 to NOT_A_PROBLEM status in Contrast.
```   

### Excludes file format

The excludes/false positives file is a JSON file which is formatted similarly to the JSON scan results returned by the Contrast-SAST scan. 

Example excludes file:
```
[
  {
    "vulnerability_name": "Unsafe Cookie@util/cookie.go",
    "category": "MISCONFIGURATION",
    "rule_name": "Unsafe Cookie",
    "reason": "Golang fixed in incompatibile version"
  },
  {
    "vulnerability_name": "Target Blank Vulnerability@templates/template.xss",
    "category": "INJECTION",
    "rule_name": "Target Blank Vulnerability",
    "reason": "Only in test code module which is not called"
  }
]
```

- The `vulnerability_name` value from each entry in the excludes file is compared to each scan vulnerability as a partial/substring match, such that if the scan vulnerability `vulnerability_name` value **starts with** the `vulnerability_name` value from the excludes file, it will be considered a matching vulnerability name.
- The `category` value should exactly match the `category` value for the targeted scan vulnerability. 
- The `rule_name` value should exactly match the `rule_name` value for the targeted scan vulnerability.
- The `reason` value will be the reason that is supplied when matching scan vulnerabilities are marked "Not a Problem" on the Contrast server, and is included in the issue closure comment for any GitHub issues closed by the automation. 

In order for a scan vulnerability to match an excludes file vulnerability, the `rule_name` and `category` values must be an exact match, and the scan vulnerability `vulnerability_name` value must **start with** or exactly match the `vulnerability_name` value from the excludes file. 
{: note}

The excludes file above was created from the scan results JSON below: 

Sample scan results JSON: 
```
[
  {
    "vulnerability_name": "Unsafe Cookie@util/cookie.go:48",
    "vulnerability_id": "65f98f6d2577d3179e18df37",
    "category": "MISCONFIGURATION",
    "rule_name": "Unsafe Cookie",
    "severity": "MEDIUM",
    "status": "REPORTED",
    "first_seen_time": "2024-03-19T13:13:17.897Z",
    "last_seen_time": "2024-03-19T13:13:17.897Z",
    "application_name": "govva-main-false-positives-test-demo",
    "application_id": "03a42d78-a9e1-43a3-9ed7-c2ed0dd0e528",
    "how_to_fix": "https://app.contrastsecurity.com/Contrast/static/ng/index.html#/****/scans/03a42d78-a9e1-43a3-9ed7-c2ed0dd0e528/vulnerabilities/65f98f6d2577d3179e18df37/howtofix",
    "cwe": "http://cwe.mitre.org/data/definitions/200.html",
    "owasp": "Not Available",
    "reference": "http://projects.webappsec.org/w/page/13246936/Information%20Leakage",
    "language": "GO",
    "comment": ""
  },
  {
    "vulnerability_name": "Unsafe Cookie@util/cookie.go:32",
    "vulnerability_id": "65f98f6d2577d3179e18df32",
    "category": "MISCONFIGURATION",
    "rule_name": "Unsafe Cookie",
    "severity": "MEDIUM",
    "status": "REPORTED",
    "first_seen_time": "2024-03-19T13:13:17.897Z",
    "last_seen_time": "2024-03-19T13:13:17.897Z",
    "application_name": "govva-main-false-positives-test-demo",
    "application_id": "03a42d78-a9e1-43a3-9ed7-c2ed0dd0e528",
    "how_to_fix": "https://app.contrastsecurity.com/Contrast/static/ng/index.html#/****/scans/03a42d78-a9e1-43a3-9ed7-c2ed0dd0e528/vulnerabilities/65f98f6d2577d3179e18df32/howtofix",
    "cwe": "http://cwe.mitre.org/data/definitions/200.html",
    "owasp": "Not Available",
    "reference": "http://projects.webappsec.org/w/page/13246936/Information%20Leakage",
    "language": "GO",
    "comment": ""
  },
  {
    "vulnerability_name": "Target Blank Vulnerability@templates/template.xss2.html:32",
    "vulnerability_id": "65f98f6d2577d3179e18df24",
    "category": "INJECTION",
    "rule_name": "Target Blank Vulnerability",
    "severity": "HIGH",
    "status": "REPORTED",
    "first_seen_time": "2024-03-19T13:13:17.897Z",
    "last_seen_time": "2024-03-19T13:13:17.897Z",
    "application_name": "govva-main-false-positives-test-demo",
    "application_id": "03a42d78-a9e1-43a3-9ed7-c2ed0dd0e528",
    "how_to_fix": "https://app.contrastsecurity.com/Contrast/static/ng/index.html#/****/scans/03a42d78-a9e1-43a3-9ed7-c2ed0dd0e528/vulnerabilities/65f98f6d2577d3179e18df24/howtofix",
    "cwe": "https://cwe.mitre.org/data/definitions/1022.html",
    "owasp": "Not Available",
    "reference": "https://owasp.org/www-community/attacks/Reverse_Tabnabbing",
    "language": "HTML",
    "comment": ""
  }
]
```

### Automatic closure of open GitHub issues for excluded vulnerabilities detected by the scan

Optionally, you may instruct the pipeline to search for any GitHub issues opened by previous scans which match the designated vulnerabilities and close them by setting the `contrast-sast-excluded-close-issues` pipeline environment property to `true`. When the Contrast scan returns a vulnerability which matches a vulnerability designated in your excludes file, the pipeline will search the GitHub repository designated by the `incident-repo` pipeline environment property for open issues, and automatically close the issue. The closed issue URL and vulnerability name are displayed in the pipeline stage log, as shown in the following example:
```
Vulnerability [Unsafe Cookie@user/session/session.go:29] matches false positives/exclude file vulnerability [Unsafe Cookie@user/session/session.go], and will be filtered out for reason [Golang fixed in incompatibile version]
Marking the VULN_ID: 65f48efbe355ce7170199fe3 from PROJECT_ID: db8b005a-f962-432c-9e21-7a63affc5519 to NOT_A_PROBLEM status in Contrast.
Closing issue https://github.ibm.com/Common-CICD-for-CPP/ciso-scs-compliance-issues-2/issues/10 (vulnerability ID: 65f48efbe355ce7170199fe3) for reason [Golang fixed in incompatibile version]
```

## Manually designating false positive results

For any given scan project in the [Contrast UI](https://app.contrastsecurity.com/), you can designate vulnerabilities as "Not a Problem". Once you have designated a vulnerability as "Not a Problem" for a given scan project, it will be filtered out of the results returned for subsequent scans against that scan project, and if you have SAST scan issue management enabled, the issue will not be opened/re-opened by the pipeline. 

To designate a vulnerability via the [Contrast UI](https://app.contrastsecurity.com/):
1. Select `Scans` from the top menu bar
2. Select the scan project from the `Scan Projects` table 
3. Select the `Vulnerabilities` tab
4. Click the `Status` field in the table row which matches the vulnerability you wish to designate
5. Select "Not a Problem" 
6. Provide an explanation for the designation in the pop-up dialog, then click the `Override` button

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

</staging>

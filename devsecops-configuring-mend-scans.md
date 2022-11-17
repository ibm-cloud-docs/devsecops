---

copyright:
  years: 2022
lastupdated: "2022-11-17"

keywords: DevSecOps, IBM Cloud, compliance, Mend

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Configuring Mend Unified Agent scans
{: #mend-scans}

These scripts provide the capability to run the Mend Unified Agent dependency scan in your DevSecOps pipeline and collect evidence based on the scan results. The Mend Unified Agent dependency scan will be run for each source repository in your pipeline that is returned by the `list_repos` [pipelinectl CLI](https://github.ibm.com/one-pipeline/core-baseimage/blob/master/pipelinectl/README.md). 

The script will search for an existing configuration file (see the [Unified Agent Configuration Parameters](https://docs.mend.io/bundle/unified_agent/page/unified_agent_configuration_parameters.html) page in the Mend product documentation) in the root of each source repository being scanned. If the file is not found, a default configuration file will be created for each pipeline run at execution time, but will not be persisted in the source code repository. If the configuration file is found, it will pass that configuration file when the scan is performed. The pipeline will override the `apiKey` and `userKey` properties in the configuration file at execution time with the values of the `mend-org-token` and `mend-user-key` pipeline environment properties.   

You should install all of the build dependencies required for your source repositories to build prior to executing this script in order for the Mend Unified Agent dependency scan to return accurate results.






### Running the script


You must have at least one source repository registered in `pipelinectl` using `save_repo`

#### Required Mend Unified Agent parameters:

| Parameter name | Description |
|-|-|
| mend-server-url | The base URL to the Mend server |
| mend-org-token | The identifier of the organization. Use the org token returned by the onboarding request in the `Org Token` text field |
| mend-user-key | This is a Secret. A Product ServiceUser created after ServiceNow onboarding |
| mend-product-name | Use the product name returned by the onboarding request in the `Product Name on Mend` text field |
| mend-product-token | Use the product token returned by the onboarding request in the `Product Token` text field |
| opt-in-mend | Set this to any non-empty string value to include the Mend Unified Agent scan as part of the Compliance Checks pipeline stage |



#### Optional Mend Unified Agent parameters:

| Parameter name | Default value | Description |
|-|-|-|
| mend-config-file-name | `mendUnifiedAgent.config` | The file name of the Mend Unified Agent configuration file the scan will look for in the root of each source code repository being scanned |
| mend-print-scan-results | `false` (property is not set) | Enter any non-zero length string to cause each Mend scan results json file to be printed to the stage log |
| mend-print-scan-summaries | `false` (property is not set) | Enter any non-zero length string to cause each Mend scan summary report to be printed to the stage log |
| mend-jar-url | `https://unified-agent.s3.amazonaws.com/wss-unified-agent.jar` | The scanner jar file that will be downloaded for each pipeline run |

### Evidence and attachments


The created evidence for these tasks will be based on these values. The DevSecOps pipeline will upload evidence to the locker, and include it in the evidence summary for Change Requests.


| field | value | 
| ----- | ----- |
| tool type     | `mend` |
| evidence type | `com.ibm.code_vulnerability_scan` |
| asset type    | `repo` |
| attachments   | `<scan report containing detected libraries and list of vulnerabilities in JSON>` |
| attachments   | `<scan summary report in text format>` |

### Debugging and logging

| Parameter name | Default value | Description |
|-|-|-|
| pipeline-debug | 0 | Debug flag 0 off 1 on | 

### Accessing your scan results

You can access your scan results by:
- Viewing them on the Mend UI.

- Set the `mend-print-scan-results` pipeline environment property to instruct the pipeline to print the scan report JSON to the stage log
- Set the `mend-print-scan-summaries` pipeline environment property to instruct the pipeline to print the scan summary text to the stage log
- Use the [DevSecOps/CoCoa CLI](https://cloud.ibm.com/docs/devsecops?topic=devsecops-cd-devsecops-cli) command line tool to download your scan results from the evidence locker using the information printed in the stage log.  Refer to the following links for more details:
  - `cocoa locker evidence get ...` [command line reference](https://cloud.ibm.com/docs/devsecops?topic=devsecops-cd-devsecops-cli#locker-evidence-get)
  - `cocoa locker attachment get ...` [command line reference](https://cloud.ibm.com/docs/devsecops?topic=devsecops-cd-devsecops-cli#locker-attachment-get)

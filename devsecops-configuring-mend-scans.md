---

copyright:
  years: 2022
lastupdated: "2022-12-01"

keywords: DevSecOps, IBM Cloud, compliance, Mend

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Configuring Mend Unified Agent scans
{: #cd-devsecops-mend-scans}

The Mend script runs the Mend Unified Agent dependency scan in your DevSecOps pipeline and collects evidence based on the scan results.
{: shortdesc}

 The Mend Unified Agent dependency scan runs for each source repository in your pipeline that is returned by the `list_repos` method. For more information, see [list_repos](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#list_repos). 

The script searches for an existing configuration file in the root of each source repository that is scanned. For more information, see [Unified Agent Configuration Parameters](https://docs.mend.io/bundle/unified_agent/page/unified_agent_configuration_parameters.html){: external}. If the configuration file is not found, a default configuration file is created for each pipeline run at execution time but is not persisted in the source code repository. If the configuration file is found, the script passes that configuration file when the scan is performed. The pipeline overrides the `apiKey` and `userKey` properties in the configuration file at execution time with the values of the `mend-org-token` and `mend-user-key` pipeline environment properties. 

Install all of the build dependencies required for your source repositories to build before you run the script to ensure that the Mend Unified Agent dependency scan returns accurate results.






## Running the script
{: #cd-devsecops-mend-run-script}



You must use `save_repo` to have at least one source repository registered in `pipelinectl`. 

### Required Mend Unified Agent parameters
{: #cd-devsecops-mend-params-req}

| Parameter name | Description |
|-|-|
| `mend-server-url` | The base URL of the Mend server. |
| `mend-org-token` | The identifier of the organization. Use the org token returned by the onboarding request in the `Org Token` text field. |
| `mend-user-key` | A secret Product ServiceUser that is created after ServiceNow onboarding. |
| `mend-product-name` | Use the product name that is returned by the onboarding request in the `Product Name on Mend` text field. |
| `mend-product-token` | Use the product token that is returned by the onboarding request in the `Product Token` text field |
| `opt-in-mend` | Set this parameter to any nonempty string value to include the Mend Unified Agent scan as part of the Compliance Checks pipeline stage. |
{: caption="Required Mend Unified Agent parameters" caption-side="top"}



### Optional Mend Unified Agent parameters
{: #cd-devsecops-mend-params-opt}

| Parameter name | Default value | Description |
|-|-|-|
| `mend-config-file-name` | `mendUnifiedAgent.config` | The file name of the Mend Unified Agent configuration file that the scan looks for in the root of each scanned source code repository. |
| `mend-print-scan-results` | `false` (property is not set) | Enter any nonzero length string to print each Mend scan results JSON file to the stage log. |
| `mend-print-scan-summaries` | `false` (property is not set) | Enter any nonzero length string to print each Mend scan summary report to the stage log. |
| `mend-jar-url` | `https://unified-agent.s3.amazonaws.com/wss-unified-agent.jar` | The scanner JAR file that is downloaded for each pipeline run. |
{: caption="Optional Mend Unified Agent parameters" caption-side="top"}

## Evidence and attachments
{: #cd-devsecops-mend-evid-attach}



The created evidence is based on the values in table 3. The DevSecOps pipeline uploads evidence to the locker and includes the evidence in the evidence summary for Change Requests.

| field | value | 
| ----- | ----- |
| tool type     | `mend` |
| evidence type | `com.ibm.code_vulnerability_scan` |
| asset type    | `repo` |
| attachments   | `<scan report containing detected libraries and list of vulnerabilities in JSON>` |
| attachments   | `<scan summary report in text format>` |
{: caption="Evidence fields and values" caption-side="top"}

## Debugging and logging
{: #cd-devsecops-mend-debug-log}

| Parameter name | Default value | Description |
|-|-|-|
| pipeline-debug | 0 | Debug flag 0 off 1 on | 
{: caption="Debug parameters" caption-side="top"}

## Accessing your scan results
{: #cd-devsecops-mend-scan-acc-results}

You can access your scan results using any of the following methods:

- Viewing them on the Mend UI.
- Setting the `mend-print-scan-results` pipeline environment property to instruct the pipeline to print the scan report JSON to the stage log.
- Setting the `mend-print-scan-summaries` pipeline environment property to instruct the pipeline to print the scan summary text to the stage log.
- Using the [DevSecOps/CoCoa CLI](/docs/devsecops?topic=devsecops-cd-devsecops-cli) command line tool to download your scan results from the evidence locker by using the information printed in the stage log.  For more information see the following resources:
   - [`cocoa locker evidence get`](/docs/devsecops?topic=devsecops-cd-devsecops-cli#locker-evidence-get)
   - [`cocoa locker attachment get`](/docs/devsecops?topic=devsecops-cd-devsecops-cli#locker-attachment-get)

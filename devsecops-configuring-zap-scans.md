---

copyright: 
  years: 2022, 2023
lastupdated: "2023-08-25"

keywords: DevSecOps, IBM Cloud, compliance

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Configuring ZAP scans
{: #cd-devsecops-zap-scans}

Zed Attack Proxy (ZAP) is a free and open source penetration testing (PEN) tool that is maintained under the umbrella of OWASP. ZAP is a "man-in-the-middle" proxy that sits between the tester's web browser and a web server. ZAP intercepts packets, modifies the content if needed, and forwards those packets to the web server.
{: shortdesc}

## Available parameters for ZAP scans
{: #zap-parameters}

Create a file `trigger_zap_scans` inside your application repository, at a location of your choice, for example, inside a `scripts` directory.

Set the values for the corresponding parameters by adding these lines to the `trigger_zap_scans` as follows - `set_env <parameter name> <value>`. For more information about a reference implementation, see [hello-compliance-app](https://us-south.git.cloud.ibm.com/open-toolchain/hello-compliance-app){: external}.

You can set and customize the following parameters to configure ZAP scans to run penetration testing on your running app.

|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|`cluster-name`|String|The name of the cluster where you want the zap scanner to be deployed and run from, if not running in DinD.| Required if not running in DinD, that is, `zap-dind` is set to `false`.|
|`ibmcloud-api`|String|Specifies the cloud environment for the ZAP cluster. Defaults to `https://cloud.ibm.com`|Required if not running in DinD, that is, `zap-dind` is set to `false`.|
|`ibmcloud-api-key`|String|API Key to deploy ZAP on a cluster.|Required if not running in DinD, that is, `zap-dind` is set to `false`.|
|`iam-token-endpoint`|String|The endpoint to retrieve the token for IAM authentication. Defaults to `https://iam.cloud.ibm.com/identity/token`|Optional, but if provided, `target-api-key` must also be provided.|
|`target-api-key`|String|API Key for IAM authentication.|Optional, but if provided, `iam-token-endpoint` must also be provided.|
|`target-application-server-url`|String|The base URL of the application server for zap to scan. It can be the same as the URL of the deployed application.|Required|
|`filter-options`|String|Set it to filter out alerts based on severity. Possible values: `Critical`, `High`, `Medium`, `Low`, `Informational`. Defaults to `Informational`, can be a comma-separated list.|Optional|
|`zap-ui-scan`|String|Flag to determine whether the scan is the UI scan or the API scan. Possible values: `true`, `false`. Setting this flag to `true` runs the UI scan, setting it to false runs the API scan. This parameter works with `opt-in-dynamic-api-scan` and `opt-in-dynamic-ui-scan` parameters.|Required|
|`zap_dind`|String|Flag to switch between deploying ZAP to a cluster or running DinD. Default: `true`. Possible values: `true`, `false`. Setting it to `true` runs ZAP as DinD, setting it to `false` deploys ZAP to a cluster for which `cluster-name`, `ibmcloud-api`, and `ibmcloud-api-key` must be set.|Optional|
|`zap-dind-localhost`|String|Hostname for ZAP Scanners when running in DinD, default: `localhost`.|Optional|
|`zap-namespace`|String|Namespace to deploy ZAP API and UI scanners in the cluster, default: `zap`.|Optional for running ZAP in cluster, `Not required` for ZAP running in DinD.|
|`zap-polling-interval`|String|Interval for the scanners to poll ZAP for completion of the scanning, default: `120s`.|Optional|
|`zap_evidence_type`|String|The evidence type for evidence gathering for scans performed by ZAP, default: `com.ibm.dynamic_scan`.|Optional|
|`show-container-log`|String|Flag to enable or disable the container logging capability for the ZAP scanners when running in DinD, default: `false`.|Optional|
|`show-zap-log`|String|Flag to enable or disable ZAP server logging capability, default: `false`.|Optional|
{: caption="Table 1. ZAP scan configuration parameters" caption-side="bottom"}

## Configuring ZAP API scans
{: #zap-api-scans}

Inside your application repository, you must have at least one API definition file present, in `json` format. This API definition file needs to be a valid `swagger` or `openapi` definition. These files can be present at any location inside the application repository, for example, inside a `definitions` directory. For clarity, these files are referred to as follows: `definitions1.json`, `definitions2.json`, and so on.

More parameters are required for configuring ZAP API scans. These parameters can be set by adding the following lines to the `trigger_zap_scans` as follows - `set_env <parameter name> <value>`.

|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|`zap-api-custom-script`|String|Path to the file that contains the request transformation logic before initiating the ZAP API scan.|Required|
|`swagger-definition-files`|String|Path to the files that contain the Swagger definitions. Can be comma-separated list.|Required|
|`zap-api-port`|String|Port to run the ZAP API scanner when deployed to a cluster, default: `9086`.|Optional|
|`zap_api_deployment_name`|String|Name of the ZAP API scanner deployment, default: `zap-api-deployment`.|Optional|
|`zap-api-image`|String|Set this parameter to use a custom ZAP API Scanner image. Otherwise, the IBM default is used.|Optional|
|`flatten-zap-api-scan-report`|String|Set this parameter to `true` to flatten the ZAP API scan results into one single html and json file. Default `false`.|Optional|
{: caption="Table 2. ZAP API configuration parameters" caption-side="bottom"}

Create a file `custom-api-script` inside your application repository, at a location of your choice, for example, inside `scripts/zap-custom-scripts` directory. This file can be named differently too, and set the path to this file against the `zap-api-custom-script` parameter.

Use the file `custom-api-script` to modify the request that goes into the ZAP scanner. The parameters that can be used in the request payload are as follows:

|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|`excludeScanTypes`|Array|List of scans that the user wants to exclude. Complete list of scans to choose from: `CRLF_Injection`,`Buffer_Overflow`, `Directory_Browsing`, `External_Redirect`, `Format_String_Error`, `Parameter_Tampering`, `Remote_File_Inclusion`, `Script_Active_Scan`, `Server_Side_Code_Injection`, `Server_Side_Include`, `SQL_Injection`, `Xpath_Injection`, `XXE_External_Entity`, `SOAP_Action_Spoofing`, `SOAP_XML_Injection`, `Relative_Path_Confusion`, `Remote_OS_Command_Injection`, `ElmahScanRule` and, `HtAccessScanRule`.|Optional|
|`apisToScan`|Array|List of APIs to scan along with required params. If `"apisToScan": ["all"]` is mentioned, then all APIs mentioned in the swagger definition are scanned with mock data for mandatory parameters/body. To customize this array to have selective APIs to be scanned, use this substructure `apisToScan: [ { "path": "/path/to/endpoint", method: "get" } ]`.|Required|
|`globalExcludeUrls`|Array|Array of URL regexes to instruct ZAP not to scan for those routes. For example, `"globalExcludeUrls": [ "^http:\/\/foo.bar$", "^http:\/\/john.doe$" ]`.|Optional|
|`authenticationType`|String|Authentication mechanism for request headers. Supported auth mechanisms are `Bearer`, `Basic`, or `ApiKey`.|Optional|
|`apiKey`|String|If `authenticationType` is not sent, this API key is used for IAM authentication that is sent in Authorization header for all requests. If `authenticationType` is set to `Bearer`, the header that is formed is `Authorization: Bearer <apiKey>`. If `authenticationType` is set to `ApiKey`, the header that is formed is `Authorization: <apiKey>`. Field is ignored for `authenticationType` as `Basic`.|Optional|
|`username`|String|Username to be used for `authenticationType` `Basic`.|Required if `authenticationType` as `Basic`|
|`password`|String|Password to be used for `authenticationType` `Basic`.|Required if `authenticationType` as `Basic`|   
{: caption="Table 3. Parameters in the request payload" caption-side="bottom"}

For sensitive information like credentials, you must read these values from a vault or a secret store.
{: note}

## Configuring ZAP UI scans
{: #zap-ui-scans}

Similar to configuring ZAP API scans, create or update the file `trigger_zap_scans` inside your application repository, at a location of your choice, for example, inside a `scripts` directory.

Set the values for the corresponding parameters by adding these lines to the `trigger_zap_scans` as follows - `set_env <parameter name> <value>`. For more information about a reference implementation, see [hello-compliance-app](https://us-south.git.cloud.ibm.com/open-toolchain/hello-compliance-app). 

Set the `zap-ui-scan` parameter to `true` for ZAP UI scan to run.
{: note}

Parameters that are required for configuring ZAP UI scans are as follows.

|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|`zap-ui-custom-script`|String|Path to the file that contains the modification logic for the UI scan payload, if required, before the ZAP UI scan is initiated.|Optional|
|`zap-ui-script-directory`|String|Path to the directory that contains the UI tests and required files to run the UI scan.|Required|
|`context-file`|String|Path to a custom context file for UI scans provided to ZAP.|Optional|
|`zap-ui-port`|String|Port to run the ZAP UI scanner when deployed to a cluster, default: `9085`.|Optional|
|`zap_ui_deployment_name`|String|Name of the ZAP UI scanner deployment, default: `zap-ui-deployment`.|Optional|
|`zap-proxy-service`|String|Name for the ZAP Proxy service required by the UI scanner, default: `zap-proxy-service`.|Optional|
|`zap-ui-image`|String|Set this parameter to use a custom ZAP UI Scanner image. Otherwise, the IBM default is used.|Optional|
|`zap-proxy-image`|String|Set this to use a custom ZAP Proxy image. Otherwise, the IBM default is used.|Optional|
{: caption="Table 4. ZAP UI configuration parameters" caption-side="bottom"}

Create a folder `uiscripts` inside the `scripts/zap` directory inside your application repository. Also set this folder path against `zap-ui-script-directory` in the `trigger_zap_scans` file. This folder can be created anywhere else, too. The path should be adjusted for `zap-ui-script-directory`.

Create a file `run.sh` inside the `uiscripts` directory. `run.sh` drives the UI tests. We can configure Protractor-based tests or any other suitable framework.

Create a file `export.sh` inside the `uiscripts` directory. This file holds all the different environment variables that are required for the test framework to use. Set those environment values by adding `export <VARIABLE_NAME>=<VALUE>`.

To hold any configuration information for the test framework, create a folder `conf` inside the `uiscripts` directory to house configuration information for the test framework.

For a reference implementation that uses Protractor as the test framework, see [hello-compliance-app](https://us-south.git.cloud.ibm.com/open-toolchain/hello-compliance-app){: external}.

## Managing ZAP Scan results 
{: #zap-scan-results}

Based on the value of the parameters `opt-in-dynamic-api-scan` and `opt-in-dynamic-ui-scan`, we can choose to selectively run either the API scan or the UI scan or both.

Inside the `trigger_zap_scans` file, we can trigger individual scans based on the opt-in parameters as follows.

```bash
if [ -n "$(get_env opt-in-dynamic-api-scan "")" ]; then
    # start the api scan
    set_env "zap-ui-scan" "false"
    source "${COMMONS_PATH}"/owasp-zap/run_scan.sh
fi

if [ -n "$(get_env opt-in-dynamic-ui-scan "")" ]; then
    # set for zap-ui-scans and start zap-ui-scan
    set_env "zap-ui-scan" "true"
    source "${COMMONS_PATH}"/owasp-zap/run_scan.sh
fi
```
The `COMMONS_PATH` refers to a location that holds the build scripts and run scripts for various tools that are a part of the DevSecOps pipeline. For more information, see [Common scripts](/docs/devsecops?topic=devsecops-devsecops-common-scripts).

You can compute the success of the ZAP scan by setting an exit code based on whether the API scan or the UI scan was run. Any vulnerabilities found are also reported. This can be checked by tapping into the variables `api-scan-result` and `ui-scan-result`.

The possible values that these parameters can take are `success` or `failure`, and defaults to `notRun`.

```bash
ZAP_API_RESULT=$(get_env api-scan-result "notRun")
ZAP_UI_RESULT=$(get_env ui-scan-result "notRun")

if [[ "${ZAP_API_RESULT}" =~ ^(success|notRun)$ && "${ZAP_UI_RESULT}" =~ ^(success|notRun)$ ]]; then
   exit 0
else
   exit 1
fi
```

A sample implementation can be found in the `trigger_zap_scans` file inside the `scripts/zap` directory inside [this repository](https://us-south.git.cloud.ibm.com/open-toolchain/hello-compliance-app){: external}.

## Configuring ZAP scan for CC pipeline 
{: #zap-scan-for-cc}

Table 5 lists the additional parameters that are required to run ZAP scans inside the [CC pipeline](/docs/devsecops?topic=devsecops-devsecops-cc-pipeline#devsecops-cc-pipeline-dynamic-scan).

|Name |Type	|Description |Required or Optional |
|:----------|:------------------------------|:------------------|:----------|
|`app-url`|String|URL of the deployed application on which the ZAP scan runs. Use the `staging` url of the application.|Required|
|`repo-url`|String|URL of the repository of the deployed application.|Optional, if `inventory repo` has all the artifacts from only one application repo.|
{: caption="Table 5. ZAP parameters in the CC pipeline" caption-side="bottom"}

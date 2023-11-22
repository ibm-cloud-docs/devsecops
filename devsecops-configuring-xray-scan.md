---

copyright:
  years: 2023, 2023
lastupdated: "2023-11-22"

keywords: DevSecOps, xray, jfrog, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Configuring JFrog Xray scans
{: #cd-devsecops-xray-scans}

The Xray scan runs on JFrog artifactory images. It finds violations and vulnerabilities by unpacking every package.
This scan is run as a part of the scan-artifact stage of CI and CC pipelines.
This scan runs for each JFrog artifactory image in the saved artifacts `list_artifacts` method. For more information, see [list_artifacts](/docs/devsecops?topic=devsecops-devsecops-pipelinectl#list_artifacts).

## Available parameters for Xray scans
{: #xray-parameters}
To run the Xray scan, you need a JFrog artifactory with Xray set up, then add the following parameters to your CI/CC pipeline:

| Name | Type | Description |Required or Optional |
|----------|------------------------------|------------------|----------|
|[`xray-opt-in`](#xray-opt-in) |text   |To turn on Xray scans, add this parameter with any value. |Required |
|[`xray-api-url`](#xray-api-url)  |text   |The JFrog artifactory URL with Xray set up. |Required |
|[`xray-artifactory`](#xray-artifactory) |tool integration | The JFrog artifactory integration's with Xray set up. |Required |
|[`xray-retry-count`](#xray-retry-count) |text   |The number of retries to wait between checking xray report readiness. |Optional |
|[`xray-retry-sleep`](#xray-retry-sleep)  |text   |The amount of wait time between retries. |Optional |
|[`xray-watch-name`](#xray-watch-name) |text   |The Xray watch name. |Required |
{: caption="Table 1. xray scan environment variables" caption-side="top"}

### xray-opt-in
{: #xray-opt-in}

You can turn on Xray integration with giving this variable any value in the CI/CC pipeline. For more information about the Xray integration, see [Artifact scan and sign](/docs/devsecops?topic=devsecops-cd-devsecops-ci-pipeline#devsecops-ci-pipeline-artifactscan).

### xray-api-url
{: #xray-api-url}

This is the artifactory's base URL. To run Xray scans in the pipeline, make sure that your artifactory is set up to run Xray.

### xray-artifactory
{: #xray-artifactory}

This is a `tool integration` type environment property. Add your artifactory an Xray configured integration.

### xray-watch-name
{: #xray-watch-name}

Your Xray watch name. For more information about watches, see [JFrog Xray - Watches](https://www.jfrog.com/confluence/display/XRAY2X/Watches).{: external}

### xray-retry-count
{: #xray-retry-count}

Use a retry method to gather the results of the scan. You can modify this value to change the number of retries you can check the Xray report readiness. The default value is `5` (times).

### xray-retry-sleep
{: #pipeline-parm-xray-retry-sleep}

 Use a retry method to gather the results of the scan. You can modify this value to set how many seconds you must wait between retries. The default value is `10` (seconds).

## Related links
{: #devsecops-xray-links}

* [JFrog XRay](https://jfrog.com/help/r/get-started-with-the-jfrog-platform/jfrog-xray){: external}

<evidence>---

copyright:
  years:  2020, 2023
lastupdated: "2023-10-11"

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Gathering evidence for CIS check, Static scan, and Dynamic Scans
{: #evidence-checks-scans}

When you are going through an audit, you must provide evidence that your development and production environments are meeting the criteria in the regulatory standards that you claim compliance with. Use the following guidance to view and provide evidence of the appropriate checks and scans in the pipeline-run logs.

## Before you begin
{: #before-checks}

Before you can start gathering evidence, be sure that you have the following prerequisites:

* A configured pipeline
* The Viewer role or higher on the Continuous Delivery service. For more information, see [Managing access for toolchains in resource groups](/docs/ContinuousDelivery?topic=ContinuousDelivery-toolchains-iam-security).

## Evidence of CIS check in pipeline-run logs
{: #cis-check}

1. In the {{site.data.keyword.cloud_notm}} console, click the **Menu** icon ![Menu icon](../../icons/icon_hamburger.svg) **> DevOps > Toolchains**, and select the toolchain for which you want to provide evidence.
2. In the **Delivery pipelines** section on the **Overview** tab, click `cd-pipeline`. A list of the previous pipeline runs is displayed.
3. Select `cd-pipelinerun` on the **Pipeline runs** tab. The details of the run open.
4. In the code-compliance-check section of the run-stage tab, and search for `com.ibm.code_cis_check`.
5. In the logs there should be two entries. Take a screen capture of the following entries:
    1. An entry highlighting the start of CIS check
    1. An  entry that displays the CIS check status

## Evidence of Static scan in pipeline-run logs
{: #static-scan}

1. In the {{site.data.keyword.cloud_notm}} console, click the **Menu** icon ![Menu icon](../../icons/icon_hamburger.svg) **> DevOps > Toolchains**, and select the toolchain for which you want to provide evidence.
2. In the **Delivery pipelines** section on the **Overview** tab, click `cd-pipeline`. A list of the previous pipeline runs is displayed.
3. Select `cd-pipelinerun` on the **Pipeline runs** tab. The details of the run open.
4. In the code-static-scan section of the run-stage tab,  and search for `com.ibm.static_scan`.
5. In the logs there should be two entries. Take a screen capture of the following entries:
    1. An entry highlighting the start of static scan
    1. An entry that displays the static scan status

## Evidence of Dynamic scan in pipeline-run logs
{: #dynamic-scan}

1. In the {{site.data.keyword.cloud_notm}} console, click the **Menu** icon ![Menu icon](../../icons/icon_hamburger.svg) **> DevOps > Toolchains**, and select the toolchain for which you want to provide evidence.
2. In the **Delivery pipelines** section on the **Overview** tab, click `cd-pipeline`. A list of the previous pipeline runs is displayed.
3. Select ` stage-owasp-zap run` on the **Pipeline runs** tab. The details of the run open.
4. From the run-stage tab, and search for `com.ibm.dynamic_scan`.
5. In the logs there should be four entries. Take a screen capture of the following entries:
    1. An entry highlighting the start of owasp-zap scan
    1. An entry that displays the owasp-zap status
    1. An entry highlighting the start of owasp-zap-ui scan
    1. An entry that displays the owasp-zap-ui status

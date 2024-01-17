---

copyright: 
  years: 2023, 2024
lastupdated: "2024-01-11"

keywords: DevSecOps, x-force, vulnerability-advisor, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Configuring IBM Cloud Vulnerability Advisor scans
{: #cd-devsecops-va-scans}

The IBM Cloud Vulnerability Advisor checks the security status of container images that are provided by IBM, third parties, or added to your organization's registry namespace. The DevSecOps CI/CC pielines uses the IBM Cloud VA Scan to detect vulnerabilities within the Docker Container Images generated and scanned within the pipelines.

## Getting the severity for a vulnerability from IBM X-Force
{: #devsecops-xforce-vuln}

The {{site.data.keyword.cloud_notm}} Vulnerability Advisor does not provide a severity rating for the detected vulnerabilities. This causes the DevSecOps CI Pipelines to assign a default `HIGH` rating to all CVEs that are detected by Vulnerability Advisor.
{: shortdesc}

To get accurate severity ratings for vulnerabilities, pipelines can use {{site.data.keyword.IBM_notm}} X-Force. To activate this feature, you need to get an {{site.data.keyword.IBM_notm}} X-Force account and credentials. For more information, see [{{site.data.keyword.IBM_notm}} X-Force Exchange API](https://api.xforce.ibmcloud.com/doc/){: external}

When you have the necessary credentials, add the following environment variables to your CI and CC pipelines to receive vulnerability ratings.

| Name | Type | Description |
 |--|--|--|
 | `xforce-url` | TEXT | The API URL for {{site.data.keyword.IBM_notm}} X-Force, used to get vulnerability ratings. Example: `https://api.xforce.ibmcloud.com/vulnerabilities/` |
 | `xforce-api-key` | SECRET | X-Force API key. |
 | `xforce-password` | SECRET | X-Force Password. |
{: caption="Table 1. Vulnernability environment variables" caption-side="top"}

## Related links
{: #devsecops-xforce-links}

* [{{site.data.keyword.IBM_notm}} X-Force Exchange API](https://api.xforce.ibmcloud.com/doc/){: external}
* [X-Force usage charges tiers and price plan](https://exchange.xforce.ibmcloud.com/faq#tiers\_of\_usage){: external}
* [Post Vulnerabilities](https://api.xforce.ibmcloud.com/doc/#/Vulnerabilities/post_vulnerabilities){: external}

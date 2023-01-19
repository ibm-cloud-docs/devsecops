---

copyright: 
  years: 2023, 2023
lastupdated: "2023-01-19"

keywords: DevSecOps, x-force, vulnerability-advisor, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Getting the severity for a vulnerability from IBM X-Force
{: #devsecops-xforce-vuln}

The DevSecOps pipeline uses the Vulnerability Advisor (VA) to identify vulnerabilities (CVEs) within Docker images. Because the VA does not supply a severity rating for the detected vulnerabilities, the pipeline assigns a `HIGH` rating to all CVEs that are found.
{: shortdesc}

To get accurate severity ratings for vulnerabilities, pipelines can use {{site.data.keyword.IBM_notm}} X-Force. To activate this feature, you need to get an {{site.data.keyword.IBM_notm}} X-Force account and credentials. For more information, see [{{site.data.keyword.IBM_notm}} X-Force Exchange API](https://api.xforce.ibmcloud.com/doc/){: external}

When you have the necessary credentials, add the following environment variables to your CI and CC pipelines to receive vulnerability ratings.

| Name | Type | Description |
 |--|--|--|
 | `xforce-url` | TEXT | The API URL for {{site.data.keyword.IBM_notm}} X-Force, used to get vulnerability ratings. Example: `https://api.xforce.ibmcloud.com/vulnerabilities/` |
 | `xforce-api-key` | TEXT | X-Force API key. |
 | `xforce-password` | SECRET | X-Force Password. |
{: caption="Table 1. Vulnernability environment variables" caption-side="top"}

## Related links
{: #devsecops-xforce-links}

* [{{site.data.keyword.IBM_notm}} X-Force Exchange API](https://api.xforce.ibmcloud.com/doc/){: external}
* [X-Force usage charges tiers and price plan](https://exchange.xforce.ibmcloud.com/faq#tiers\_of\_usage){: external}
* [Post Vulnerabilities](https://api.xforce.ibmcloud.com/doc/#/Vulnerabilities/post_vulnerabilities){: external}

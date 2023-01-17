---

copyright:
  years: 2023
lastupdated: "2023-01-17"

keywords: DevSecOps, x-force, vulnerability-advisor, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Get severity for a vulnerability from IBM x-force
{: #devsecops-xforce-vuln}

The DevSecOps pipeline uses the IBM Vulnerability Advisor (VA) to identify vulnerabilities (CVEs) within Docker images. Because the VA does not supply a severity rating for the detected vulnerabilities, the pipeline will assign a `HIGH` rating to all vulnerabilities (CVEs) that are found.

To obtaget accurate severity ratings for vulnerabilities, pipelines can use IBM X-Force. To activate this feature, you need to get an IBM X-Force account and credentials. 

To set up your IBM X-Force account and get the necessary credentials, see [IBM X-Force Exchange API](https://api.xforce.ibmcloud.com/doc/)
-	[Eclipse](https://www.eclipse.org/downloads/).

When you have the necessary credentials, add the following environment variables to your pipeline to receive vulnerability ratings.

| Name | Type | Description |
 |--|--|--|
 | `xforce-url` | TEXT | The API URL for IBM X-Force, utilized for obtaining vulnerability ratings. |
 | `xforce-api-key` | TEXT | X-Force API key |
 | `xforce-password` | SECRET | X-Force Password|
{: caption="Table 1. Vulnernability environment variable" caption-side="top"}

 ## Related Links
{: #devsecops-xforce-links}

* [IBM X-Force Exchange API](https://api.xforce.ibmcloud.com/doc/)
* [XForce Usage Charges tiers and price plan](https://exchange.xforce.ibmcloud.com/faq#tiers\_of\_usage)
* [Post Vulnerabilities](https://api.xforce.ibmcloud.com/doc/#/Vulnerabilities/post_vulnerabilities)


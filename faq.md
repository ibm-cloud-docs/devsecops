---

copyright:
  years: 2021
lastupdated: "2021-09-15"

keywords: DevSecOps

subcollection: devsecops

---

{:tsSymptoms: .tsSymptoms}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:faq: data-hd-content-type='faq'}
{:support: data-reuse='support'}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:download: .download}

# FAQs for DevSecOps
{: #faq_devsecops}

Get answers to frequently asked questions about using DevSecOps.
{:shortdesc} 

## How can I run the CD pipeline with an existing Change Request ID?
{: #cd-pipeline-cr-id}
{: faq}
{: support}

In the CD [pipeline parameters](/docs/devsecops?topic=devsecops-cd-devsecops-pipeline-parm#cd-parameters), locate the `change-request-id` variable.

Change the default `notAvailable` value (which will trigger a new Change Request creation) to the ID of an open change request. 


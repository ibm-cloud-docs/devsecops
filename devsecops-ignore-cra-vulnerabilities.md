---

copyright:
   years: 2021, 2022
lastupdated: "2022-03-04"

keywords: pipeline, toolchain, CC, automate, automation, continuous delivery, continuous integration, continuous compliance, devsecops, DevOps, IBM Cloud, CRA, code risk analyzer, ignore, omit, vulnerability, vulnerabilities, advisory

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Omitting vulnerabilities found by the Code Risk Analyzer
{: #devsecops-omit-vulnerabilities}

Under exceptional circumstances, you might need to deploy your application even though it uses vulnerable packages. The Code Risk Analyzer's (CRA) function is to find vulnerable packages before an advisory does.
{: shortdesc}

Whenever possible, update vulnerable packages or remove them.

You can create the file `.cra/.cveignore` (formerly `.cracveignore`) in the scanned repository's root with the following schema:

```json
[
  {
    "cve": "string",
    "alwaysOmit": "bool",
    "untilRemediationAvailable": "bool",
    "expiration": "string"
  },
]
```

The following code snippet shows a sample `.cveignore` file:

```json
[
  {
    "cve": "CVE-2021-27290",
    "alwaysOmit": true
  },
  {
    "cve": "CVE-2020-8244",
    "untilRemediationAvailable": true,
  },
  {
    "cve": "SNYK-JAVA-ORGAPACHEHTTPCOMPONENTS-31517",
    "expiration": "2022-02-10T22:08:41+04:00",
    "comment": "Additional security measures are in place"
  }
]
```

The previously used `.cracveomit` file is deprecated now and replaced by `.cra/.cveignore`.

You can learn more about the Code Risk Analyzer [plug-in's docs](/docs/code-risk-analyzer-cli-plugin), or you can also check the [deprecated CRA docs](/docs/ContinuousDelivery?topic=ContinuousDelivery-cd-configure-cra-repos).

---

copyright:
  years: 2022
lastupdated: "2022-04-28"

keywords: DevSecOps, IBM Cloud

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Configuring custom grace periods on the CC pipeline
{: #configure-custom-grace-period}

The Continuous Compliance (CC) pipeline calculates the due dates of incident issues based on the severity of an issue.
{: shortdesc}

By default, the following configuration is used:

| Severity      | Grace Period |
| :-----------: | :----------: |
| Informational | 90 days      |
| Low           | 45 days      |
| Medium        | 45 days      |
| High          | 15 days      |
| Critical      | 15 days      |
{: caption="Table 1. Default grace periods" caption-side="bottom"}

To change this default configuration, create a new property in the CC pipeline's environment properties named `grace-period-configuration`. This environment property must be a JSON string, matching the following format:

```json
{
  "informational": 50,
  "low": 40,
  "medium": 30,
  "high": 20,
  "critical": 10
}
```
{: codeblock}

Make sure that the contents of the environment variable match the format in the example. If the environment property does not match the expected format or is not a valid JSON string, the pipeline uses the default values.
{: note}

The environment property `grace-period-configuration` sets due dates for issues that do not have a due date set already. For issues that have a due date set, reconfiguring the `grace-period-configuration` does not update those due dates.
{: note}

For more information, see [Incident issues with Due Date](/docs/devsecops?topic=devsecops-devsecops-issues-due-date).

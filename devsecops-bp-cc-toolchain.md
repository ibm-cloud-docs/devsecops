---

copyright:
  years: 2022, 2024
lastupdated: "2024-02-22"

keywords: DevSecOps, ibm cloud, best practices, secure application, continuous integration,

subcollection: devsecops
---

{{site.data.keyword.attribute-definition-list}}

# Best practices in continuous compliance toolchain
{: #practices-cc-toolchain}

The [CC pipeline](/docs/devsecops?topic=devsecops-devsecops-cc-pipeline) is useful for a continuous scanning of existing deployed artifacts and their source repositories independent of your deployment schedule. The CC pipeline uses the [async subpipeline](/docs/devsecops?topic=devsecops-devsecops-async-stages#async-stages-setup-triggers) that runs in parallel to the main pipeline run to optimize pipeline run time and improve pipeline resiliency.
{: shortdesc}

The implementation of the continuous compliance (CC) toolchain DevSecOps follows these practices.

- Runs a static code scanner at pre-defined intervals on the application repositories. - Uses `detect secrets` to prevent secret leaks in the application source code.
- Scan the container image for any security vulnerabilities.
- Opens incident issues to track problems found during the pipeline run and is marked with a due date.
- Generate a `summary.json` file and stores in {{site.data.keyword.cos_full_notm}} or Git repositiory to summarize the results of the scan.

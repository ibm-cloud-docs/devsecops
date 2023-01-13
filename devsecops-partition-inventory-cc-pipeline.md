---

copyright:
  years: 2022
lastupdated: "2022-04-26"

keywords: DevSecOps, IBM Cloud, compliance, glob pattern

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Partitioning inventory for CC pipeline
{: #partition-inventory-cc-pipeline}

By default, the Continuous Compliance (CC) pipeline runs all compliance checks and tests for each artifact that is listed in the inventory. It is also possible to run the checks and tests only against a subset of the inventory entries.
{: shortdesc}

To run the checks and tests against a subset of inventory entries, complete these steps:
1. Provide a comma-separated list of entries that you want to include to the `inventory-include` environment property. For example, `entry-a` or `entry-a,entry-b`.
1. Provide a comma-separated list of entries that you want to exclude to the `inventory-exclude` environment property. For example, `entry-a` or `entry-a,entry-b`.

If `inventory-include` is used, only the entries that are provided in that environment property are scanned and tested within the pipeline. Entries that are provided in `inventory-exclude` are excluded from scanning or testing. If both `inventory-include` and `inventory-exclude` are present, `inventory-include` takes precedence, and then `inventory-exclude` excludes items from the subset that is defined by the inclusion list. It is also possible to define entries by using glob patterns.

For more information about glob patterns, see the [glob](https://man7.org/linux/man-pages/man7/glob.7.html){: external} manual.
{: note}

## Example
{: #partion-inventory-cc-pipeline-example}

The following inventory is available:

* service-a/entry-a
* service-a/entry-b
* service-a/entry-c
* service-b/entry-a
* service-b/entry-b

If you want to have two separate pipelines for artifacts of service-a and service-b, you can create two triggers where you set `inventory-include` to `service-a/**/*` to include only the artifacts of service-a. Then, set `service-b/**/*` on the second trigger to include only the artifacts of service-b.

When you decide that you also need an extra pipeline for `service-a/entry-c`, you then can exclude that entry from the previous pipeline by setting `inventory-exclude` to `service-a/entry-c`. Then, create another trigger with `inventory-include` set to `service-a/entry-c`.

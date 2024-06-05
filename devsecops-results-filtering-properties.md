---

copyright:
  years: "2024"
lastupdated: "2024-05-13"

keywords: DevSecOps, IBM Cloud, compliance, cims, multiscan, container image

subcollection: devsecops-working

---

{{site.data.keyword.attribute-definition-list}}

# Filtering CIMS results with properties
{: #cd-devsecops-filtering-results-properties}

Enable the pipeline to produce filtered results, in addition to unfiltered results. If you configure the pipeline to produce filtered scan results, the collect-evidence scripts that create GithHub issues use only the filtered results. With this configuration, you can make sure that GitHub issues are not created for specific results such as low severity vulnerabilities.

To set up the pipeline to produce filtered results, specify one or more filtering pipeline environment properties.

## Restricting CVEs or vulnerabilities with last modified date
{: #cims-vulnerabilities-published-modified-date}

Set the `multiscan-filter-scan-results-date` pipeline environment property to limit the reported results to detected CVEs with a published or last modified date (per public vulnerability databases) that are within the date range. The format for the string is `YYYY-MM-DD:YYYY-MM-DD`.

Examples:
 - To get all CVEs published up to and including 20 April 2022, use `0000-01-01:2022-04-20`.
 - To get all CVEs published in 2022, use `2022-01-01:2022-12-31`.

## Restricting CVEs or vulnerabilities by severity
{: #cims-vulnerabilities-severity}

Set the `multiscan-filter-severity` pipeline environment property, if you want the pipeline to include only vulnerabilities that meet or exceed a specified severity level.

Valid values for this property are:
 - `critical`
 - `high`
 - `medium`
 - `low`

## Restricting CVEs or vulnerabilities by fixing availability
{: #cims-vulnerabilities-availability}

Set the `multiscan-filter-by-has-fix` pipeline environment property to a nonempty string to include only vulnerabilities that have a fix available. Use this property as determined by the report field `fixedVersions`, where `fixedVersions` is equal to a nonempty string with a value other than `under investigation` or `will not fix`.


## Restricting CVEs or vulnerabilities by image layer
{: #cims-vulnerabilities-image-layer}

The `baseLayerVulnerability` field in the scan results indicates whether the multiscanner determined that the vulnerability is from the base layer of the scanned image.

To include only vulnerabilities that are from the base image layer, for example, vulnerabilities that originate from a UBI base image:

 - Set the `multiscan-filter-base-layer` pipeline environment property to `true` to include only vulnerabilities where the `baseLayerVulnerability` field contains the value `true`.
 - Set the `multiscan-filter-base-layer-include-empty` pipeline environment property to `true` (default value) to include vulnerabilities where the `baseLayerVulnerability` column value is empty. An empty column value indicates that the image layer of the vulnerability was not determined.
 - Set the `multiscan-filter-base-layer-include-empty` pipeline environment property to `false` to filter out vulnerabilities where the `baseLayerVulnerability` column value is empty.

To include only vulnerabilities that are not from the base image layer, for example, vulnerabilities that originate from an application layer:

 - Set the `multiscan-filter-base-layer` pipeline environment property to `false` to include only vulnerabilities where the `baseLayerVulnerability` field contains the value `false`.
 - Set the `multiscan-filter-base-layer-include-empty` pipeline environment property to `true` (default value) to include vulnerabilities where the `baseLayerVulnerability` column value is empty. An empty column value indicates that the image layer of the vulnerability was not determined.
 - Set the `multiscan-filter-base-layer-include-empty` pipeline environment property to `false` to filter out vulnerabilities where the `baseLayerVulnerability` column value is empty.

### Restricting CVEs or vulnerabilities by CIMS tag
{: #cims-vulnerabilities-image-layer}

Set the `multiscan-filter-tag-names` pipeline environment property to filter out vulnerabilities that were tagged with one or more of the specified CIMS tags. Any vulnerability row that contains one or more of the specified tags is not included. If you are specifying multiple tags, separate each tag by using a semicolon.

For more information, see [Tagging vulnerability results as false positives or other classifications](/docs/devsecops?topic=devsecops-cd-devsecops-cims#tagging-vulnerability-results-as-false-positives-or-other-classifications).

### Example:
{: #cims-example-cims}

If you set `multiscan-filter-tag-names`=`false-positive(scanner-error);false-positive(latent-vulnerability)`, any vulnerability that was tagged as either `false-positive(scanner-error)` or `false-positive(latent-vulnerability)` is excluded from the filtered scan results.

## Scanning multi-arch images
{: #cims-scanning-multi-arch-images}

When you specify images to scan that are multi-arch images, which refer to a manifest list, CIMS automatically inspects the manifest list. Then, CIMS scans each image in the manifest list separately. You can specify which architectures in the manifest list are to be scanned by using the `multiscan-architectures` pipeline environment property as a comma-separated list. The default value for `multiscan-architectures` is `amd64,ppc64le,s390x,arm64`.

Valid values:
- `amd64`
- `ppc64le`
- `s390x`
- `arm64`

CIMS produces separate scan result JSON and CSV files for each architecture that is scanned.
{: #note}

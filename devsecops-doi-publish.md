---

copyright: 
  years: 2024
lastupdated: "2024-08-09"

keywords: DevSecOps, doi publish

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# doi-publish
{: #doi-publish}

`doi-publish` is a function designed to download an attachment and publish it as a test record to DevOps Insights.

This function is provided by the [`doi-publish-test-record`](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-commons/-/blob/master/doc/doi__doi-publish-testrecord.md) script that is part of the [Commons Scripts Library](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-commons#tool-inventory).

The function does the following tasks:

- Uses the download-attachment function to retrieve the file specified by evidence-file and attachment-label, saving it to attachment-output-path.
- Depending on whether url or app-name is provided, it calls either doi-publish-testrecord or doi-publish-testrecord-with-appname to publish the test record.

## Usage
{: #doi-publish-usage}

The script `doi-publish` requires the following parameters:

  - `--evidence-file` : Path to the evidence JSON file that contains the details of the attachment to be downloaded.
  - `--attachment-label` : Label for the attachment to be downloaded.
  - `--url` : Repository URL. If provided, doi-publish-testrecord will be called.
  - `--app-name` : App name. If provided, doi-publish-testrecord-with-appname will be called.
  - `--record-type` : Record type to be used by doi-publish-testrecord.

The following parameter is optional:

  - `--attachment-output-path` : Full path of the file where the downloaded attachment output should be saved. If not specified, a temporary file will be created.

Use the following command to get help:

```text
doi-publish --help
```
{: pre}

### Note
   - Must provide either `--url` or `--app-name`, but not both.

### Example usage
{: #doi-publish-usage-example}

```bash

source "${COMMONS_PATH}/doi/doi-publish-testrecord.sh"
doi-publish \
  --evidence-file "./evidence.json" \
  --attachment-label "test-results" \
  --url "https://git.repository.url/owner/repo" \
  --record-type "unittest"
```
{: codeblock}

## Multiple attachments in doi-publish 
{: #multi-attachment-doi-publish}

The doi-publish function does not support multiple attachments. It is designed to handle only a single attachment per execution. If you need to publish multiple attachments, you will need to run the doi-publish function separately for each attachment.

---

copyright:
  years: 2024
lastupdated: "2024-08-09"

keywords: DevSecOps, download attachment, script, 

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# download_attachment script
{: #download-attachment}


The download_attachment script is used to download attachments based on a provided label from a specified evidence file.

The `download-attachment` script is provided by the pipeline. It does not need to be installed. The script has the following dependencies:

- bash
- `libstdc++` shared library 
- `libgcc` shared library

Make sure that the dependencies are installed in the base image that uses this tool for setting Git commit statuses.

## Usage
{: #download-attachment-usage}

The script `download-attachment` requires the following parameters:

- `--evidence-filepath`  
   Path of the file containing the evidence.json.
- `--label`  
   Label corresponding to the attachment that needs to be downloaded.
- `--output-filename`  
   Path where the downloaded file will be stored.

Use the following command to get help:

```text
download_attachment --help
```
{: pre}

### Example usage
{: #download-attachments-usage-example}

```bash
download_attachment \
  --evidence-filepath "/path/to/evidence.json" \
  --label "test-results" \
  --output-filename "/path/to/outputfile"
```
{: codeblock}


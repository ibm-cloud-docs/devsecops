---
copyright: 
  years: 2025
lastupdated: "2025-01-09"

keywords: DevSecOps, doi publish, doi publish testrecord

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# doi-publish-testrecord
{: #doi-publish-testrecord-script}

`doi-publish-testrecord` is a script containing helper functions for Devops Insights, using the `ibmcloud doi` CLI plugin to publish a test record to DevOps Insights.  

This script is part of the [Commons Scripts Library](https://us-south.git.cloud.ibm.com/open-toolchain/compliance-commons#tool-inventory).

## Inputs
{: #doi-publish-testrecord-inputs}

The below properties are fetched using pipelinectl command `get_env` :

Property | Description
---------|------------
`ibmcloud-api-key` | The IBM Cloud API key that interacts with the DevOps Insights instance where DOI records are uploaded.
`doi-buildnumber` | The build number value to use as the `--buildnumber` flag in `ibmcloud doi` commands. (Defaults to `<build number provided by pipeline>`)
`doi-logicalappname` | The app name to use as the `--logicalappname` flag in `ibmcloud doi` commands. (Defaults to `<repo name portion of repository URL>`)

## Functions
### doi-publish-testrecord()
{: #doi-publish-testrecord}

Publish a test record to DevOps Insights deriving logical app name from the repository URL. The `doi-publish-testrecord` function calls the `doi-publish-testrecord-with-appname` function.

Usage : `doi-publish-testrecord <record_type> <test_record> <repo_url>`

#### Arguments
Argument | Name | Description
---------|------|------------
  $1 | record_type | DevOpsInsights record type to use as the `--type` flag value in `ibmcloud doi` commands
  $2 | test_record | Path to test file to use as the `--filelocation` flag value  in `ibmcloud doi` commands
  $3 | repo_url | repository URL that will is relevant for the DOI test record. Used to get the logical app name

#### Example Usage:
{: #doi-publish-testrecord-example}

```bash
#!/usr/bin/env bash

source "${COMMONS_PATH}/doi/doi-publish-testrecord.sh"

doi-publish-testrecord "unittest" "./results.json" "https://git.repository.url/owner/repo"
```
{: codeblock}

### doi-publish-testrecord-with-appname()
{: #doi-publish-testrecord-with-appname}

Publish a test record to DevOps Insights using `ibmcloud doi testrecord-publish` command. For more information, please refer [DevOps Insights CLI](/docs/ContinuousDelivery?topic=ContinuousDelivery-CLI_devops-insights#publishtestrecord).

Usage : `doi-publish-testrecord-with-appname <record_type> <test_record> <app_name>`

#### Arguments
Argument | Name | Description
---------|------|------------
  $1 | record_type | DevOpsInsights record type to use as the `--type` flag value in `ibmcloud doi` commands
  $2 | test_record | Path to test file to use as the `--filelocation` flag value  in `ibmcloud doi` commands
  $3 | app_name | The app name to use as the `--logicalappname` flag in `ibmcloud doi` commands

#### Example Usage:
{: #doi-publish-testrecord-with-appname-example}

```bash
#!/usr/bin/env bash

source ${COMMONS_PATH}/doi/doi-publish-testrecord.sh

doi-publish-testrecord-with-appname \
  "<record_type>" \
  "<test_record>" \
  "<app_name>"
```
{: codeblock}

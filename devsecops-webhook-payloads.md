---

copyright:
  years: 2021
lastupdated: "2021-10-13"

keywords: DevSecOps

subcollection: devsecops

---

{:shortdesc: .shortdesc}
{:table: .aria-labeledby="caption"}
{:external: target="_blank" .external}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:download: .download}
{:help: data-hd-content-type='help'}
{:support: data-reuse='support'}

# Accessing arguments from webhook payloads 
{: #cd-devsecops-webhook-payloads}

Your scripts can access the full trigger webhook payload that started the current pipeline run. These scripts are located in the `TRIGGER_PAYLOAD` ENV variable.
{: shortdesc}

The following code snippet shows how to access the webhook trigger payload and provide or override a variable in the pipeline.
It reads the author of the recent commit that triggered the pipeline and saves it for the toolchain to use later.

```bash
AUTHOR_EMAIL="$(echo "${TRIGGER_PAYLOAD}" | jq '.head_commit.author.email')"

set_env author-email $AUTHOR_EMAIL

#
# later in a separate pipeline stage, you can access this
#
get_env author-email
```

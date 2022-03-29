---

copyright:
  years: 2021, 2022
lastupdated: "2022-03-29"

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

Your scripts can access the full trigger webhook payload that started the current pipeline run. They are located in the file under the `/trigger-payload/payload.json` path.
{: shortdesc}

The option to use the `TRIGGER_PAYLOAD` environment variable is deprecated.
{: deprecated}

The following code snippet shows how to access the webhook trigger payload and provide or override a variable in the pipeline.
It reads the author of the recent commit that triggered the pipeline and saves it for the toolchain to use later.

```bash
AUTHOR_EMAIL="$(jq '.head_commit.author.email' '/trigger-payload/payload.json')"

set_env author-email $AUTHOR_EMAIL

#
# later in a separate pipeline stage, you can access this
#
get_env author-email
```

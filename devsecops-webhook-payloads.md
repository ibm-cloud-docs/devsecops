---

copyright: 
  years: 2022, 2026
lastupdated: "2026-05-06"

keywords: DevSecOps

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Accessing arguments from webhook payloads 
{: #cd-devsecops-webhook-payloads}

Your scripts can access the full trigger webhook payload that started the current pipeline run. They are located in the file whose path is defined in the `TRIGGER_PAYLOAD_PATH` environment variable.
{: shortdesc}

The option to use the `TRIGGER_PAYLOAD` environment variable is deprecated.
{: deprecated}

The following code snippet shows how to access the webhook trigger payload and provide or override a variable in the pipeline.
It reads the author of the recent commit that triggered the pipeline and saves it for the toolchain to use later.

```bash
AUTHOR_EMAIL="$(jq '.head_commit.author.email' $TRIGGER_PAYLOAD_PATH)"

set_env author-email $AUTHOR_EMAIL

#
# later in a separate pipeline stage, you can access this
#
get_env author-email
```

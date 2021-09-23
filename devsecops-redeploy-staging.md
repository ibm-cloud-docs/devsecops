---

copyright:
  years: 2021
lastupdated: "2021-07-27"

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

# Deploying content to staging again
{: #cd-devsecops-redeploy-staging}

You might need to deploy all of your content to staging again, where the inventory in staging does not contain any new items and the `staging_latest` tag points to the current commit.
{: shortdesc}

1. The continuous delivery pipelins starts and tags the current commit with the Pipeline Run ID.
2. The pipeline picks up the content of the staging branch from that tag.
3. The pipeline calculates the deployment delta between the current commit and the contents of the `staging_latest` tag. Because the content is identical, the delta is empty.
4. The pipeline attempts to deploy. A successful deployment concludes by attaching the `prod_latest` tag to the commit that you work with. Because the content is identical, there are no changes.

---

copyright:
  years: 2021, 2022
lastupdated: "2022-03-23"

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

To make this possible, set the variable `force-redeploy` value to `true`

1. The continuous delivery pipeline starts and tags the current commit with the Pipeline Run ID.
2. The pipeline picks up the content of the staging branch from that tag.
3. The pipeline calculates the deployment delta between the current commit and the contents of the `staging_latest` tag. Because the content is identical, the delta is empty. The setting `force-redeploy` makes it possible for the pipeline to continue.
4. The pipeline attempts to deploy. Because the delta is empty, you might want to rely on the content of the inventory instead. You can access it on a file level by using `$WORKSPACE/$(get_env INVENTORY_REPO_DIRECTORY_NAME)`.
5. A successful deployment concludes by attaching the `prod_latest` tag to the commit that you work with. Because the content is identical, there are no changes.

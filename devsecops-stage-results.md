---

copyright:
  years: 2021, 2022
lastupdated: "2022-01-25"

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

# Using the Stage Results API in custom scripts
{: #cd-devsecops-stage-results}

You can use the Stage Results API to access the results of specific stages and custom stages.
{: shortdesc}

1. Type `get_data result` to print which stages have saved results:

   ```bash
   $ get_data result
   test
   detect-secrets
   branch-protection
   ```
   {: codeblock}

1. Type `get_data result <stage-name>` to print the result for a specific stage:

   ```bash
   $ get_data result detect-secrets
   success
   $ get_data result test
   failure
   ```
   {: codeblock}

1. Iterate through the saved results:

   ```bash
   #!/usr/bin/env bash
   while read -r stage; do
         result=$(get_data result $stage)
         echo "The result of '$stage' stage is: '$result'"
   done < <(get_data result)
   ```
   {: codeblock}

Outputs the following lines to the console:

   ```text
   $ The result of 'detect-secrets' stage is: 'success'
   $ The result of 'branch-protection' stage is: 'success'
   ```
   {: screen}

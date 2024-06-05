---

copyright:
  years: 2024, 2024
lastupdated: "2024-05-28"

keywords: DevSecOps, IBM Cloud, git clone, submodules

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Using the Stage Results API in custom scripts
{: #cd-devsecops-stage-results}

You can use the [Stage Results API](/docs/devsecops?topic=devsecops-custom-scripts) to access the results of specific stages and custom stages. This API can only be used in your custom scripts.
{: shortdesc}

Customize your output to view the desired output in your Stage results. Use the following code snippets to generate various kinds of output:

1. Type `get_data result` to print which stages have saved results.

      ```bash
         $ get_data result
         test
         detect-secrets
         branch-protection
      ```
      {: codeblock}

1. Type `get_data result <stage-name>` to print the result for a specific stage.

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

On running the snippet in you console , the following output is generated:

      ```text
         The result of 'detect-secrets' stage is: 'success'
         The result of 'branch-protection' stage is: 'success'
      ```
      {: screen}

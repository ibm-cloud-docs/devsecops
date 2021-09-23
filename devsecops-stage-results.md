---

copyright:
  years: 2021
lastupdated: "2021-06-18"

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

 ```
$ get_data result
test
detect-secrets
branch-protection
```

1. Type `get_data result <stage-name>` to print the result for a specific stage:

 ```
$ get_data result detect-secrets
success
$ get_data result test
failure
```

1. Iterate through the saved results:

 ```
#!/usr/bin/env bash
while read -r stage; do
    result=$(get_data result $stage)
    echo "The result of $stage stage is: $result"
done < <(get_data result)
```


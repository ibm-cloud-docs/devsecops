---

copyright:
  years: 2022
lastupdated: "2022-03-25"

keywords: DevSecOps, IBM Cloud

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

# Assigning incident issues to users
{: #assign-incident-issues}

Incident issues that are created by the continuous integration (CI) or continuous compliance (CC) pipelines can have default assignees.
{: shortdesc}

To achieve this, set the `incident-assignees` environment property to one or more usernames that are separated by a comma. For example, `First-User` or `First-User,Second-User`.

The pipeline takes these usernames and assigns these users to the incident issues that are created by the [`collect-evidence`](/docs/devsecops?topic=devsecops-devsecops-collect-evidence) script. If a username is invalid, the logs provide a warning about an unsuccessful assignment, but the evidence collection is successful.

You can define this environment property per trigger so that each trigger can assign different users.
{: tip}

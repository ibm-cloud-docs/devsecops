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

# Postponing the due date of an incident issue
{: #due-date-postpone}

If you want to postpone the due date of an incident issue, you can ask for a review from a security focal. Depending on the review, the due date can be postponed by modifying the due date field in the incident issues description. Be sure to reference the security-focal review in the issue, such as providing a link to it in a comment.
{: shortdesc}

To change the due date for the issue, edit the issue description in the repository by changing the `Due date` field:

```markdown
Issue: **CVE-2020-0001**
Due date: **2022-01-01** <!-- important: update onlyl the value between ** if you need to update this value -->
Subject: `my-subject`
URL: https://example.com
Tool Type: Whitesource scan
```
{: codeblock}

Be sure to edit only the text between the `**` characters in the issues description.
{: note}

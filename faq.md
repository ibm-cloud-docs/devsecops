---

copyright: 
  years: 2022, 2025
lastupdated: "2025-01-28"

keywords: DevSecOps

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# FAQ for DevSecOps
{: #faq_devsecops}

Get answers to frequently asked questions about using DevSecOps.
{: shortdesc} 



## What is the difference between CI and CC pipeline?
{: #ci-cd-pipeline-diff}
{: faq}
{: support}

You might notice that the CI and CC pipeline have common steps. The scans and checks that are run are similar in nature and details. The following table provides the differences between CI and CC pipeline.

| CI pipeline | CC pipeline |
|---------|------------|
| It is part of the CI toolchain. | It is part of the CC toolchain. |
| It is triggered after a merge request is merged with the `master` branch | It can be triggered manually or at predefined intervals that are independent of a deployment schedule. |
| An application URL and application code repository details are entered as part of the setup process. | An application URL and application code repo details are provided after the CC toolchain is configured and before initiation of first pipeline run. |
| The incident issues that are created as part of various scans and checks during compliance checks do not carry a due date. | The incident issues that are created as part of various scans and checks during compliance checks carry a due date. |
| The incident issues that are created are found during the build. | The incident issues that are created are found during periodic scans of the staging or production environment. |
| The `summary.json` file is not generated at the end of each CI pipeline run. | The `summary.json` file is not generated at the end of each CI pipeline run. |
| It includes steps like application artifact creation, artifact signing, and deploy to development cluster. This in turn creates inputs for the CD pipeline.  | It runs only scans and checks that are needed for compliance testing. |
{: caption="Differences in the CI and CC pipelines" caption-side="bottom"}

## How can a user customize the pipeline?
{: #faq-ci-sec-pipeline-customize}
{: faq}
{: support}

A pipeline is customized by using custom scripts. Custom scripts are extension points in the pipeline where adopters, teams, and users can provide scripts to run custom tasks for their CI/CD strategies.

Custom scripts control the pipeline stages. You can use a configuration file `(pipeline-config.yaml)` to configure the behavior of the stages, script content, and the base artifact that runs the scripts. The scripts and configuration for pipeline stages are loaded from an application repository that are similar to `.travis.yml` or `Jenkinsfile` or a custom repository.

For more information, see [Customizing pipelines by using custom scripts](/docs/devsecops?topic=devsecops-cd-devsecops-pipelines-custom-customize).

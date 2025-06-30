---

copyright:
  years:  2024, 2024
lastupdated: "2024-12-17"

keywords: tool integrations, IBM Cloud Public, Other Tool, Custom Tool

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Configuring IBM Concert Integration
{: #devsecops-concert-integration}

The integration of IBM Concert with IBM DevSecOps offers a seamless way to manage secure development pipelines while harnessing Concert's strengths in orchestrating and automating complex workflows.
{: shortdesc}

IBM® Concert uses generative AI to help you understand the your organization's risk posture by looking at critical indicators, or dimensions, across your application landscape. It ingests data contained in imported SBOM files or automated ingestion jobs from third-party tools and services to map your application components, environments, and access points. For more details on IBM Concert, see [IBM Concert](https://www.ibm.com/docs/en/concert?topic=overview){: external}.
Meanwhile, IBM DevSecOps incorporates security practices into DevOps processes, ensuring secure code development and deployment without sacrificing speed or efficiency.

The process of integrating IBM DevSecOps CI/CD/CC pipelines with IBM Concert to automatically generate and upload the concert files are as follows:

1. **CycloneDX SBOMs** - generated in CI pipelines
1. **CVE-defined Vulnerability Files** - generated in CI/CC pipelines
1. **Concert-defined "Build" Inventory Files** - generated in CI pipelines
1. **Concert-defined "Deploy" Inventory Files** - generated in CI and CD pipelines
1. **Concert-defined "Application" Blueprint Files** - generated in CI pipelines

For more details on the mentioned files, see [Uploading files in the Concert UI](https://www.ibm.com/docs/en/concert?topic=concert-uploading-files-in-ui){: external}.

## Integrating IBM Concert in IBM DevSecOps pipeline
{: #concert-integration}

The integration of IBM Concert and IBM DevSecOps creates a cohesive workflow that combines Concert's robust automation and orchestration capabilities with DevSecOps' focus on embedding security into every stage of the CI/CD pipeline. This synergy simplifies development processes by automating security practices, optimizing task management, and providing actionable insights for better decision-making.

To enable IBM Concert integration in IBM DevSecOps pipelines, users must complete the following steps:

1. **Generating an API key** - Obtain an API key from the IBM Concert SaaS instance to allow DevSecOps pipelines to upload the generated concert files.
1. **Configuring Environment Properties** - Populate the mandatory environment properties that are related to IBM Concert integration within the CI, CD, and CC pipelines.
1. **Modifying application and development repositories** - In the CI/CD/CC pipelines, include the necessary code to generate the data and files that need to be uploaded to IBM Concert.

Once the IBM DevSecOps CI/CD/CC pipelines are executed, you can confirm the successful generation and upload of concert files by reviewing the task logs.

An example message that appears in the evaluate task log of finish stage for each CI/CD/CC pipeline for every file generated and uploaded:

```json
2024-12-20 08:17:39,415 - INFO - Read configuration file: /toolkit-data/config.yaml

2024-12-20 08:17:40,458 - INFO - Uploaded file /toolkit-data/20241220081619-compliance-app-concert-sps-ind-dev-app.json as data_type application_sbom

2024-12-20 08:17:40,459 - INFO - Upload response /toolkit-data/20241220081619-compliance-app-concert-sps-ind-dev-app.json: {'record_paths': ['0000-0000-0000-0000/roja/application_sbom/2024/12/20/134493617_20241220081619-compliance-app-concert-sps-ind-dev-app.json'], 'job_data': {'message': '202 Accepted'}} with metadata None
```

You can expect messages similar to the ones above in the evaluate task log of the finish stage for each CI/CD/CC pipeline, corresponding to every file generated and uploaded.

Users can also access the IBM Concert SaaS interface `(Landing Page > Administration > Event Log)` to monitor the status of file uploads and processing. After processing is complete, the uploaded data is reflected in various reports within the IBM Concert SaaS UI, including the **Arena View**.

## Generating an API key from IBM Concert SaaS instance
{: #generate-API}

IBM Concert API enables data ingestion by uploading SBOMs, vulnerability scans, compliance scans, certificate details, and other data to enhance application topology and insights.

To generate an API key for IBM Concert SaaS, complete the following steps:

1. Log in to the IBM Concert SaaS interface by using the functional ID credentials.
1. Click the **User** icon at the upper-right of the navigation header, and select the **API Key** option from the dropdown.
1. Click **Generate API Key**.

If an API key exists, you must revoke it before you generate a new one.
{: note}

1. Save the API key value in a secure location immediately.

Warning: The API key secret cannot be viewed again after the dialog box is closed.

1. In the same pop-up window that appears, you can find details on how to use the API key including the following:
    - **API Key Type** - C_API_KEY for SaaS Concert
    - **Instance ID** - Unique identifier for your concert instance
    - **Concert Endpoint URL** - Concert URL to make post API calls

    Record the three details as they are required to populate the pipeline environment properties.
    {: note}

## Integrating IBM Concert with toolchain
{: #integrate-toolchain}

IBM Concert aims to provide comprehensive insights into applications, referred to as "Application 360 Insights", by leveraging data uploaded to its platform. These insights are automatically generated when sufficient and relevant data is ingested, enabling organizations to gain a holistic view of their application's security, compliance, and operational status.

The core data model of IBM Concert comprises of three types of data in terms of:
1. Build Inventories - the Code Repo used to build a specific Container Image
1. Deploy Inventories - the Kubernetes cluster settings used to deploy a specific Container Image
1. Application Blueprints - in-scope Deployment Environments, business owner name & contact, application criticality, blueprints/selectors for in-scope “build”/”deploy” inventories, and so on.

ConcertDef data must be formatted in JSON for uploading to a specific Concert instance, either through the Console GUI or the API service endpoint. These files are typically categorized and named as “build,” “deploy,” and “application” SBOMs, corresponding to their respective purposes.

The ConcertDef JSON Schema provides a structured framework for validating and editing these files. Tools like check-jsonschema can be used to detect syntax errors, ensuring the JSON files conform to schema requirements. Additionally, modern text editors like VSCode support schema-assisted editing, offering features like real-time validation and auto-completion to simplify file creation and modification.

To streamline the usage of the ConcertDef Schema, three dedicated sub-schema specifications are available, tailored for each file type—build, deploy, and application—making it easier to validate and work with the corresponding data.

**Note:** By default, Concert Environments and Applications (new or existing) uploaded via Concert SBOMs in the concer enabled pipeline will be added to the BISO Team group. Users can assign additional Concert groups by specifying them as a comma-separated list in the concert-group environment property.

### Configuring environment properties
{: #configure-env-properties}

After implementing the required changes in the Application Repository (for CI pipelines) and Deployment Repository (for CD pipelines), the following environment properties need to be updated in the pipeline environment properties:

Mandatory Environmenet Properties

The following table properties are required for CI/CC/CD pipelines:

| Name | Type | Default Value | Description |
| ---  | ---- | ------------- | ----------- |
| concert-enabled | Boolen | 0 | Enablement flag for Concert support |
| concert-endpoint-url | Text String | - | URL for the Concert API service endpoint |
| concert-instance-id | Text String | - | Unique ID of the Concert instance |
| concert-api-key | Secure String | - | Secret API key generated via the Concert instance |
{: caption="Mandatory settings for the DevSecOps Concert Tool" caption-side="bottom"}

Optional Environment Properties

The following environment properties can be optionally used for CI pipelines: 

| Name | Type | Default Value | Description |
| ---  | ---- | ------------- | ----------- |
| concert-version | Text String | 1.0.2 | Enumeration: 1.0.2 |
| concert-toolkit-image | Text String | icr.io/cpopen/ibm-concert-toolkit:latest | Registry path and tag for the public Concert toolkit image |
| concert-application-name | Text String | ${APP_REPO_NAME} | ConcertDef application name; Set this property to the official name of your product as defined in SRT. Space in name should be replaced by “-”. Any other special characters are not supported |
| concert-application-version | Secure String | 1.0.0 | ConcertDef application version |
| concert-component-name | Text String | ${APP_REPO_NAME} | ConcertDef component name |
| concert-component-version | Text String | 1.0.0 |ConcertDef component version |
| build-target-environment | Text String | dev | CI Target deployment environment |
| concert-group | Text String | BISO Team | Comma seperated list of concert groups to be added as collabotor to application or environment |
{: caption="Optional CI pipeline settings" caption-side="bottom"}

The following environment properties can be optionally used for CD pipelines: 
| Name | Type | Default Value | Description |
| ---  | ---- | ------------- | ----------- |
| concert-version | Text String | 1.0.2 | Enumeration: 1.0.2 |
| concert-toolkit-image | Text String | icr.io/cpopen/ibm-concert-toolkit:latest | Registry path and tag for the public Concert toolkit image |
| concert-component-name | Text String | $ {APP_REPO_NAME} | ConcertDef component name |
| concert-group | Text String | BISO Team | Comma seperated list of concert groups to be added as collabotor to application or environment |
{: caption="Optional CD pipeline settings" caption-side="bottom"}

The following environment properties can be optionally used for CC pipelines: 

| Name | Type | Default Value | Description |
| ---  | ---- | ------------- | ----------- |
| concert-version | Text String | 1.0.2 | Enumeration: 1.0.2 |
| concert-toolkit-image | Text String | icr.io/cpopen/ibm-concert-toolkit:latest | Registry path and tag for the public Concert toolkit image |
{: caption="Optional CC pipeline settings" caption-side="bottom"}

## Customizing and building your own App
{: #custom-app}

To generate the concert deploy file in CI and CD pipelines, it is essential to populate specific key fields with accurate and relevant data. This requires updates to certain repositories to ensure that the pipelines have the necessary information for IBM Concert to process and generate the file correctly.

### CI pipelines
{: #custom-app-CI-pipeline}

* App repository

    **deploy.sh**

    ```json
    if [[ 1 == $(get_env concert-enabled 0) ]]; then

      # Discover API server URL
      #
      PROTOCOL=$(kubectl get endpoints -n default kubernetes -o yaml -o=jsonpath="{.subsets[0].ports[0].name}")
      IP=$(kubectl get endpoints -n default kubernetes -o yaml -o=jsonpath="{.subsets[0].addresses[0].ip}")
      PORT=$(kubectl get endpoints -n default kubernetes -o yaml -o=jsonpath="{.subsets[0].ports[0].port}")

      # Create a "deploy" JOSN file for Concert "deployS"
      #
      CONCERT_DEPLOY_DIR=$(get_env CONCERT_DEPLOY_DIR "${WORKSPACE}/concert/deploy")
      DEPLOY_OBJECT_NAME=deploy_$(date -u "+%Y%m%d%H%M%S")
      cat > ${CONCERT_DEPLOY_DIR}/${DEPLOY_OBJECT_NAME} << EOD
    {
      "type": "concert_deploy",
      "name": "${DEPLOY_OBJECT_NAME}",
      "runtime_type": "kubernetes",
      "api_server": "${PROTOCOL}://${IP}:${PORT}",
      "platform": "${DEPLOYMENT_PLATFORM}",
      "cluster_platform": "${K8_PLATFORM}",
      "cluster_id": "${IBMCLOUD_IKS_CLUSTER_ID}",
      "cluster_region": "${IBMCLOUD_IKS_REGION}",
      "cluster_name": "${IBMCLOUD_IKS_CLUSTER_NAME}",
      "cluster_namespace": "${IBMCLOUD_IKS_CLUSTER_NAMESPACE}",
      "image_uri": "$(load_artifact app-image name)@$(load_artifact app-image digest)",
      "app_url": "${APPURL}"
    }
    EOD
    fi
    ```
    {: codeblock}

    **deploy_setup.sh**

    ```json
    if [ -z "${DEPLOYMENT_FILE}" ]; then
    echo "deployment-file environment is not defined."
          if [ "${CLUSTER_TYPE}" == "OPENSHIFT" ]; then
            K8_PLATFORM="roks"
            DEPLOYMENT_PLATFORM="ibmcloud"
            DEPLOYMENT_FILE="deployment_os.yml"
          else
            K8_PLATFORM="iks"
            DEPLOYMENT_PLATFORM="ibmcloud"
            DEPLOYMENT_FILE="deployment_iks.yml"
          fi
        ```
        {: codeblock}

### CD pipelines
{: #custom-app-CD-pipeline}

* Deployment repository [GitHub URL](https://github.ibm.com/Vikash-Kumar50/hello-compliance-deployment-20241022174127544){: external}

    **deploy.sh**

    ```json
    if [[ 0 != $(get_env concert-enabled 0) ]]; then

        # Discover API server URL
        #
        PROTOCOL=$(kubectl get endpoints -n default kubernetes -o yaml -o=jsonpath="{.subsets[0].ports[0].name}")
        IP=$(kubectl get endpoints -n default kubernetes -o yaml -o=jsonpath="{.subsets[0].addresses[0].ip}")
        PORT=$(kubectl get endpoints -n default kubernetes -o yaml -o=jsonpath="{.subsets[0].ports[0].port}")

        # Create a "deploy" JOSN file for Concert "deployS"
        #
        echo "Running deploy metadata generator"
        CONCERT_DEPLOY_DIR=$(get_env CONCERT_DEPLOY_DIR "${WORKSPACE}/concert/deploy")
        mkdir -p ${CONCERT_DEPLOY_DIR}
        DEPLOY_OBJECT_NAME=deploy_$(date -u "+%Y%m%d%H%M%S")
        cat > ${CONCERT_DEPLOY_DIR}/${DEPLOY_OBJECT_NAME} << EOD
    {
      "type": "concert_deploy",
      "name": "${DEPLOY_OBJECT_NAME}",
      "runtime_type": "kubernetes",
      "api_server": "${PROTOCOL}://${IP}:${PORT}",
      "platform": "${DEPLOYMENT_PLATFORM}",
      "cluster_platform": "${K8_PLATFORM}",
      "cluster_id": "${IBMCLOUD_IKS_CLUSTER_ID}",
      "cluster_region": "${IBMCLOUD_IKS_REGION}",
      "cluster_name": "${IBMCLOUD_IKS_CLUSTER_NAME}",
      "cluster_namespace": "${IBMCLOUD_IKS_CLUSTER_NAMESPACE}",
      "image_uri": "${IMAGE}",
      "app_url": "${APPURL}"
    }
    EOD

    fi
    ```
    {: codeblock}

    **deploy_setup.sh**

    ```json
    export IBMCLOUD_IKS_CLUSTER_ID
    ………
    ………
    IBMCLOUD_IKS_CLUSTER_ID=$(jq -r '.id' "${IBMCLOUD_IKS_CLUSTER_NAME}.json")
    ………
    ………
    if [ "${CLUSTER_TYPE}" == "OPENSHIFT" ]; then
      K8_PLATFORM="roks"
      DEPLOYMENT_PLATFORM="ibmcloud"
      DEPLOYMENT_TYPE_FILE="deployment_os"
    else
      K8_PLATFORM="iks"
      DEPLOYMENT_PLATFORM="ibmcloud"
      DEPLOYMENT_TYPE_FILE="deployment_iks"
    fi
    ```
    {: codeblock}

## Sample Custom Finish Script: Save Generated Concert Files to Cloud Object Storage/S3
{: #sample-script}

All concert files that are generated during the pipeline run are stored in a pipeline-specific directory, accessible through the command $(get_env CONCERT_DATA_PATH). The following is an example of a custom "finish" script from the .pipeline-config.yaml file, which saves these generated files to a Cloud Object Storage/S3 bucket.

```json
finish:
  image: icr.io/continuous-delivery/pipeline/pipeline-base-ubi:3.50
  dind: true
  abort_on_failure: false
  image_pull_policy: IfNotPresent
  script: |
    #!/usr/bin/env bash
if [[ "pr" == ${PIPELINE_NAMESPACE} ]] \
        || [[ "Failed" == ${PIPELINE_STATUS} ]] \
        || [[ 0 == $(get_env concert-enabled 0) ]]; then
      exit
fi

if [[ 0 != $(get_env concert-if-cos 0) ]]; then
      if [[ "cc" == ${PIPELINE_NAMESPACE} ]]; then
        export APP_REPO=$(get_env one-pipeline-config-repo)
        export APP_REPO_NAME=${APP_REPO##*/}
      elif [[ "cd" == ${PIPELINE_NAMESPACE} ]]; then
        export APP_REPO=${ONE_PIPELINE_REPO%-*}
        export APP_REPO_NAME=${APP_REPO##*/}
      fi

      CONCERT_DATA_PATH=$(get_env CONCERT_DATA_PATH)
      COS_NAMESPACE=$(get_env cos-namespace "concert")
      COMPONENT_NAME=$(get_env concert-component-name ${APP_REPO_NAME})
      ENVIRONMENT_TARGET=$(get_env target-environment "qa")

      while read -r filename; do
        if [[ -s ${CONCERT_DATA_PATH}/${filename} ]]; then
          cocoa artifact upload \
            --backend="cos" \
            --namespace="${COS_NAMESPACE}" \
            --pipeline-run-id="${COMPONENT_NAME}/${ENVIRONMENT_TARGET}" \
            --upload-path "${filename}" \
            ${CONCERT_DATA_PATH}/${filename}
        fi
      done < <(ls -1 ${CONCERT_DATA_PATH})
fi
```
{: codeblock}

## Verifying file generation and upload in DevSecOps CI/CD/CC Pipelines
{: #validate-file-generation}

After enabling IBM Concert integration and running the DevSecOps CI/CD/CC pipelines, you can verify the successful generation and upload of concert files to the IBM Concert SaaS UI. To do so, check the pipeline logs for the evaluate task in the Finish stage, focusing on the final steps of the task execution.

Adopters can verify the status of file uploads and processing in the IBM Concert SaaS UI by following these steps:
1. Log in to the IBM Concert SaaS instance.

1. Go to **Administrator** > **Event Logs**.

1. Review the event logs to check the status of the uploaded files.

### Viewing reports based on uploaded files in IBM Concert
{: #viewing-reports}

After the successful processing of IBM Concert files, adopters can access the data from the uploaded files through various reports available in the IBM Concert SaaS UI, like the **Arena View**, **Dimensions**, and **Inventory**.

### BISO Team concert group as collaborator to Concert Application and Environments
{: #default-concert-access-group}

It is mandatory to add `BISO Team` concert group as collaborator to both existing and newly created concert `applications` and `environments` to ensure visibility for the BISO Team. To achieve this, addition of the `BISO Team` group has been automated in pipelines as a part SPS-Concert integration. 

Users **do not need to make any changes** to their pipelines to enable this automation. However, they can assign additional Concert groups by specifying them as a comma-separated list in the `concert-group` environment property.

Example of detailed log for automated addition of BISO Team concert group to an application:
```
Adding group BISO Team to application compliance-app-concert-sps-ind-dev-vk as collaborator with VIEW permission.
Fetching application ID for name: compliance-app-concert-sps-ind-dev-vk
Entity type application with name as compliance-app-concert-sps-ind-dev-vk having id 190cce88-0b95-4351-9abc-db3a512376d1 found in the concert entity list.
Validating existence of group BISO Team in concert group list and getting its type.
Group BISO Team exists in concert group list and its group type is 'group' which is expected group type.
Checking if group BISO Team is already added as collabotor to application compliance-app-concert-sps-ind-dev-vk with id 190cce88-0b95-4351-9abc-db3a512376d1.
Validated that group BISO Team is not added as collaborator to compliance-app-concert-sps-ind-dev-vk with id 190cce88-0b95-4351-9abc-db3a512376d1.
Adding group BISO Team to application compliance-app-concert-sps-ind-dev-vk with id 190cce88-0b95-4351-9abc-db3a512376d1 as collaborator.
Group BISO Team has been successfully added as collaborator to application compliance-app-concert-sps-ind-dev-vk with id 190cce88-0b95-4351-9abc-db3a512376d1.
```
Group BISO Team will be added to an application or environment only if it has not already been added. An example of such a use case shown below:
```
Adding group BISO Team to environment SPS-Concert as collaborator with VIEW permission.
Fetching environment ID for name: SPS-Concert
Entity type environment with name as SPS-Concert having id 2d7a321a-eb2b-439a-96f1-fe397592e25b found in the concert entity list.
Validating existence of group BISO Team in concert group list and getting its type.
Group BISO Team exists in concert group list and its group type is 'group' which is expected group type.
Checking if group BISO Team is already added as collabotor to environment SPS-Concert with id 2d7a321a-eb2b-439a-96f1-fe397592e25b.
Skipping addition of group BISO Team as it is already added as collaborator to environment SPS-Concert with id 2d7a321a-eb2b-439a-96f1-fe397592e25b.
```


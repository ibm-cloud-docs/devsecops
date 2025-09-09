---

copyright:
  years: 2022, 2025
lastupdated: "2025-09-09"

keywords: DevSecOps

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Endpoints accessed from DevSecOps pipelines
{: #cd-devsecops-pipeline-hosts}

The DevSecOps pipelines interact with various IBM Cloud services, either directly through pipeline workers or via scripts used for DevSecOps operations. For instance, pipeline workers may access the IAM service to verify permissions, while pipeline scripts may connect to the Code Risk Analyzer service to perform a vulnerability scan.

When running pipeline workers behind a firewall, it's essential to configure your network settings to allow egress access to these IBM Cloud endpoints. This is necessary for successful pipeline execution and completion of required tasks.

{: shortdesc}

| Host | Component | Target Component/Service |Description |Required/Optional|
|-------------------------------------|------------------------------------------------------------------------------------------------------------------------------|---|---|---|
| `https://devops-api.private.{REGION}.devops.cloud.ibm.com ` | Private Workers | Continuous Delivery | Endpoint for Private Workers to fetch the SCM (Source Code Management) Token (for instance: GitHub Token etc.) in the absence of a user provided access token. | Required |
| `https://private.iam.cloud.ibm.com ` | Private Workers | Identity Access Management | Endpoint for Pipeline Workers to access permissions for perform SCM Operations like: Repository Cloning, Set/Check Pull Request Status. | Required |
| `https://private-worker-service.private.{REGION}.devops.cloud.ibm.com ` | Private Workers | Continuous Delivery | Endpoint for Private Workers to send start and finish notifications. Required only when Slack Integration is configured for Pipelines. | Optional |
| `https://s3.direct.{COS_REGION}.cloud-object-storage.appdomain.cloud ` | DevSecOps Continuous Delivery Pipelines | Cloud Object Store | Endpoints for DevSecOps CD Pipelines to interact with [COS (Cloud Object Store)](/docs/cloud-object-storage?topic=cloud-object-storage-endpoints#endpoints-region) Evidence Locker.  | Required |
| `https://{instance_ID}.private.{SM_REGION}.secrets-manager.appdomain.cloud ` | DevSecOps Continuous Delivery Pipelines | Secrets Manager | Endpoint for DevSecOps CD Pipeline to retreive secrets from the [Secret Manager](/docs/secrets-manager?topic=secrets-manager-endpoints) Instance | Required |
| `https://private.cloud.ibm.com` | DevSecOps Continuous Delivery Pipelines | IBM Cloud CLI (Command Line Interface) | Endpoint for the DevSecOps CD Pipeline to interact with the IBM Cloud CLI using private endpoint. | Required |
| `https://private.{EN_REGION}.event-notifications.cloud.ibm.com/event-notifications/v1/instances/`  | Event Notification | DevSecOps Continuous Delivery Pipelines | Endpoint for DevSecOps CD Pipeline to send pipeline events to be sent to Event Notification Instance. Required only when Event Notification Integration is configured for DevSecOps CD Pipelines.| Optional |
| `https://private.icr.io/va/api/v4 ` | DevSecOps Continuous Delivery Pipelines | IBM Cloud Container Registry | Endpoint for DevSecOps CD Pipeline to interact with [Vulnerability Advisor](https://cloud.ibm.com/apidocs/vulnerability-advisor). Required only when pipeline run Vulnerability Advisor Scans on the docker images stored in ICR (IBM Cloud Container Registry) | Optional |
| `https://otcbroker.{REGION}.devopsinsights.cloud.ibm.com` | DevSecOps Continuous Delivery Pipelines | Continuous Delivery | Endpoint for DevSecOps CD Pipeline to interact IBM Cloud CRA Plugin Service. Required only when pipeline run [CRA (Code Risk Analyser)](/docs/ContinuousDelivery?topic=ContinuousDelivery-cra-cli-plugin) Scans on the source code. | Optional | 
| `https://vcurator.{REGION}.devopsinsights.cloud.ibm.com` | DevSecOps Continuous Delivery Pipelines | Continuous Delivery | Endpoint for DevSecOps CD Pipeline to interact with the IBM Cloud CRA service. Required only when a pipeline runs CRA (Code Risk Analyser) scans on the source code. | Optional |
| `https://gitsecure.{REGION}.devopsinsights.cloud.ibm.com` | DevSecOps Continuous Delivery Pipelines | Continuous Delivery | Endpoint for DevSecOps CD Pipeline to interact with the IBM Cloud CRA service. Required only when a pipeline runs CRA (Code Risk Analyser) scans on the source code. | Optional |
| `https://dlms.{REGION}.devopsinsights.cloud.ibm.com` | DevSecOps Continuous Delivery Pipelines | Continuous Delivery | Endpoint for DevSecOps CD Pipeline to interact with the IBM Cloud CRA service. Required only when pipeline runs CRA (Code Risk Analyser) scans on the source code. | Optional |
| `https://gateservice.{REGION}.devopsinsights.cloud.ibm.com` | DevSecOps Continuous Delivery Pipelines | Continuous Delivery | Endpoint for DevSecOps CD Pipeline to interact with the IBM Cloud CRA service. Required only when a pipeline runs CRA (Code Risk Analyser) scans on the source code.| Optional |

{: caption="List of hosts accessed by the DevSecOps Pipelines" caption-side="top"}

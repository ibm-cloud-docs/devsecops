---

copyright:
  years:  2021, 2022
lastupdated: "2022-09-05"

keywords: IAM access for toolchain, permissions for toolchain, identity and access management for toolchain, roles for toolchain, actions for toolchain, assigning access for toolchain

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Managing IAM access for cloud resources accessed by the DevSecOps Toolchains
{: #iam-permissions}

{{site.data.keyword.cloud}} Identity and Access Management (IAM) enables administrators to assign specific roles such as Viewer, Editor, Operator, and Administrator to users and service identities. This defines their access levels to resources like Continuous Delivery, Secrets Manager, Container Registry, Cloud Object Storage, as well as deployment targets such as Kubernetes clusters and Code Engine.

In IBM Cloud DevSecOps pipelines, different sets of IAM permissions are required based on the actor's role. Broadly, roles in IBM Cloud DevSecOps pipelines can be categorized into two main types:

- **Pipeline Administrators**
These roles are responsible for managing and configuring the DevSecOps pipelines, toolchains, and associated resources. 
A Pipeline Administrator needs broad permissions to configure, manage, and troubleshoot pipelines, including Editor or Administrator roles for resources like Toolchain, Continuous Delivery, Container Registry, and Secrets Manager. 
This allows them to configure pipelines, configure toolchain integrations, integrate cloud services, integrate security tools.

- **Pipeline Runners**
These roles are responsible for executing the DevSecOps pipelines, toolchains, and associated resources.
A Pipeline Runner, which executes pipelines, requires limited permissions focused on running builds, deploying applications, and accessing necessary resources. 
The role provides limited permissions like Operator, Writer, or Reader, allowing them to interact with the necessary services (e.g., Kubernetes, Code Engine, Cloud Object Storage) without modifying critical configurations. 

The access to DevSecOps pipelines and resources can be granted in two primary approaches. 
  - Access Group based approach
  - Service ID based approach

Both methods support secure access management in IBM Cloud, with API keys mapped to access groups being ideal for centralized role management, while Service ID API keys offer a more flexible and secure way to authenticate automated DevSecOps workflows.

## Access Group based approach

This approach involves assigning users to access groups, which are services with predefined roles and permissions. 
By using access groups, administrators can centrally manage permissions, ensuring consistency and scalability across multiple users. 
This method simplifies access management, as any changes to an access group automatically apply to all users.
The API keys created by users within the access group automatically inherit the assigned permissions.

Here is a table outlining the different sets of permissions required for **Pipeline Administrator** role: 

| **Resource / Service**        | **IAM Permissions**                      | **Notes**                                |
|-----------------------------  |------------------------------------------|------------------------------------------|
| **IAM Identity Service**      | User API key creator, Service ID creator, Operator |                                |
| **User Management**           | Editor                                   |                                          |
| **IAM Access Groups Service** | Editor                                   |                                          |
| **Resource Group**            | Viewer                                   |                                          |
| **IBM Cloud Toolchain**       | Editor                                   |                                          |
| **Continuous Delivery**       | Editor                                   |                                          |
| **Secrets Manager**           | Manager, Administrator                   | **Administrator** role is required to create new Secret Manager Instance and create authorization. **Manager** role is required to create secret groups in Secret Manager Instance.                     |
| **Cloud Object Storage**      | Writer, Editor                           |                                          |
| **Container Registry**        | Manager                                  | **Manager** role is required to create namespace in Container Registry |

Here is a table outlining the different sets of permissions required for **Pipeline Runner** role:

| **Resource / Service**      | **IAM Permissions**                      | **Notes**                                |
|-----------------------------|------------------------------------------|------------------------------------------|
| **Resource Group**          | Viewer                                   |                                          | 
| **IBM Cloud Toolchain**     | Pipeline Runner, Operator                | **Operator** role is required to launch the sub-pipelines                                                                                                       |
| **Secrets Manager**         | Reader                                   | **Reader** role is required to view secrets metadata                                                                                                            |
| **Cloud Object Storage**    | Object Writer, Reader                    |                                          |
| **Container Registry**      | Reader, Writer                           | **Manager** role is required to create namespace in Container Registry, if one does not exist already |


Based on the deployment target as IBM Cloud Kubernetes Service (IKS) or IBM Cloud Code Engine, different IAM permissions required for Pipeline Administrators and Pipeline Runners. 

Here is a table outlining the different sets of permissions required for **Pipeline Administrator** role: 

| **Resource / Service**      | **IAM Permissions**                      | **Notes**                                |
|-----------------------------|------------------------------------------|------------------------------------------|
| **IBM Kubernetes Service**  | Manager, Editor                          |                                          |
| **Code Engine**             | Manager, Editor                          |                                          |

Here is a table outlining the different sets of permissions required for **Pipeline Runner** role:

| **Resource / Service**      |**IAM Permissions**                       | **Notes**                                |
|-----------------------------|------------------------------------------|------------------------------------------|
| **IBM Kubernetes Service**  | Writer, Operator                         |                                          |
| **Code Engine**             | Writer, Operator                         |                                          |
 

## Service ID based approach

Service IDs are non-human identities used to authenticate and authorize automated processes, such as DevSecOps pipelines. 
Service ID API keys grant specific permissions to pipelines or services without tying them to an individual user account. 
This approach enhances security by ensuring that pipelines operate with only the required permissions, reducing the risk of unauthorized access while enabling seamless integration with IBM Cloud services.

Here is a table outlining the different sets of permissions required for **Pipeline Administrator** role: 

| **Resource / Service**        | **IAM Permissions**                      | **Notes**                                |
|-----------------------------  |------------------------------------------|------------------------------------------|
| **IAM Identity Service**      | User API key creator, Service ID creator, Operator |                                |
| **User Management**           | Editor                                   |                                          |
| **IAM Access Groups Service** | Editor                                   |                                          |
| **Resource Group**            | Administrator                            |                                          |
| **IBM Cloud Toolchain**       | Administrator                            |                                          |
| **Continuous Delivery**       | Editor                                   |                                          |
| **Secrets Manager**           | Manager, Administrator                   | **Administrator** role is required to create new Secret Manager Instance and create authorization. **Manager** role is required to create secret groups in Secret Manager Instance.                     |
| **Cloud Object Storage**      | Writer, Administrator                    |                                          |
| **Container Registry**        | Manager                                  | **Manager** role is required to create namespace in Container Registry |

Here is a table outlining the different sets of permissions required for **Pipeline Runner** role:

| **Resource / Service**      | **IAM Permissions**                      | **Notes**                                |
|-----------------------------|------------------------------------------|------------------------------------------|
| **Resource Group**          | Viewer                                   |                                          | 
| **IBM Cloud Toolchain**     | PipelineRunner, Operator                 | **Operator** role is required to launch the sub-pipelines                                                                                                       |
| **Secrets Manager**         | Reader                                   | **Reader** role is required to view secrets metadata                                                                                                            |
| **Cloud Object Storage**    | Object Writer, Reader                    |                                          |
| **Container Registry**      | Reader, Writer                           | **Manager** role is required to create namespace in Container Registry, if one does not exist already |

Here is a table outlining the different IAM permissions required for Pipeline Administrators and Pipeline Runners, based on the deployment target as IBM Cloud Kubernetes Service (IKS) or IBM Cloud Code Engine:

Here is a table outlining the different sets of permissions required for **Pipeline Administrator** role: 

| **Resource / Service**      | **IAM Permissions**                      | **Notes**                                |
|-----------------------------|------------------------------------------|------------------------------------------|
| **IBM Kubernetes Service**  | Manager, Administrator                   |                                          |
| **Code Engine**             | Manager, Administrator                   |                                          |

Here is a table outlining the different sets of permissions required for **Pipeline Runner** role:

| **Resource / Service**      |**IAM Permissions**                       | **Notes**                                |
|-----------------------------|------------------------------------------|------------------------------------------|
| **IBM Kubernetes Service**  | Writer, Operator                         |                                          |
| **Code Engine**             | Writer, Operator                         |                                          |

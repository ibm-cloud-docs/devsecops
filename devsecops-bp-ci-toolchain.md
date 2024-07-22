---

copyright:
  years: 2022, 2024
lastupdated: "2024-02-22"

keywords: DevSecOps, ibm cloud, best practices, ci toolchain, continuous integration,

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Best practices in continuous integration toolchain
{: #practices-ci-toolchain}

The implementation of continuous integration (CI) toolchain DevSecOps follows these practices.
{: shortdesc}

* Runs a static code scanner on the application repositories to detect secrets in the application source code and vulnerable packages that are used as application dependencies.
* Builds a container image on every Git commit, setting a tag based on build number, timestamp, and commit ID for traceability.
* Tests the Dockerfile before the image creation.
* Stores the built image in a private image registry.
* Automatically configures access permissions for target cluster deployment by using API tokens to revoke.
* Scans the container image for security vulnerabilities.
* Adds a Docker signature upon successful completion.
* Automatically inserts the built-image tag into the deployment manifest.
* Uses an explicit namespace, in the cluster, to insulate each deployment (`kubectl delete namespace` command).
* Automatically builds, validates, and deploys any code that is merged into the target Git repository branch to the Kubernetes cluster.

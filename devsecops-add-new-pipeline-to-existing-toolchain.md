---

copyright:
  years: 2024, 2024
lastupdated: "2024-10-09"

keywords: DevSecOps, IBM Cloud, public key, private key, GPG

subcollection: devsecops

---

{{site.data.keyword.attribute-definition-list}}

# Adding a pipeline to an existing toolchain
{: #devsecops-add-pipeline}

 Add a pipeline to an existing toolchain by creating a new Delivery Pipeline tool integration.
{: shortdesc}

To add a pipeline to an existing toolchain, complete these steps:

1. From the Toolchain Overview page, click **Add +** to add a tool integration for your toolchain.

   ![Toolchain overview](images/toolchain-overview.png){: caption="Toolchain overview" caption-side="bottom"}

2. Search and click **Delivery Pipeline** tool integration tile.

3. Choose between `Classic pipeline` that is defined through the UI, or the new Tekton-based 'pipeline-as-code' that is defined in a Git repository.

   ![Set up your pipeline](images/setup-new-pipeline.png){: caption=" Drop-down menu to choose the pipeline" caption-side="bottom"}

   Tekton pipelines are available only with Private Workers.
   {:  note}

4. Name your pipeline, choose the pipeline configuration method, and then select **Create Integration**.

5. After you configure and set up your pipeline, click **Create Integration**.

For more information about setting up a Tekton-based delivery pipeline, see [Creating a Delivery Pipeline for Tekton](/docs/ContinuousDelivery?topic=ContinuousDelivery-tekton-pipelines&interface=ui#configure_tekton_pipeline).

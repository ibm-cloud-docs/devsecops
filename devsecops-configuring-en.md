---
copyright:
  years: 2024, 2024
lastupdated: "2024-11-06"

keywords: DevSecOps, IBM Cloud, compliance

subcollection: devsecops
---

{{site.data.keyword.attribute-definition-list}}

# {{site.data.keyword.en_short}} for DevSecOps pipelines
{: #cd-devsecops-configure-en}

{{site.data.keyword.en_full_notm}} is a service for routing event notifications, alerting you to critical events in your {{site.data.keyword.cloud}} account or triggering automated actions via webhooks. You can filter and direct event notifications from {{site.data.keyword.cloud_notm}} services to email, SMS, Webhooks, and Push Notifications.
{: shortdesc}

For more information, see [Getting started with {{site.data.keyword.en_short}}](/docs/event-notifications?topic=event-notifications-getting-started).

## Adding an {{site.data.keyword.en_short}} instance to toolchains
{: #devsecops-en-add-to-toolchain}

After you create an {{site.data.keyword.en_short}} instance, complete the following steps to add it to a toolchain:

1. From the {{site.data.keyword.cloud_notm}} console, click the **Menu** icon > **Platform Automation** > **Toolchains**.
2. On the Toolchains page, select the toolchain to view its Overview page. Alternatively, from your app's Overview page, access the Continuous Delivery card, and click **View Toolchain**, then click **Overview**.
   a. Click **Add Tool**.
   b. In the Tool Integrations section, select **{{site.data.keyword.en_short}}**.
3. Enter the name for this tool integration as it appears on the {{site.data.keyword.en_short}} card in your toolchain. This name serves as the identifier for the tool integration within your toolchain.
4. Select the instance to connect to the toolchain.
5. Click **Create Integration** to add the {{site.data.keyword.en_short}} tool integration to your toolchain.
6. From the Overview page of your toolchain > {{site.data.keyword.cloud_notm}} Tools section, click **{{site.data.keyword.en_short}}**.

For more information, see [Enabling notifications](/docs/ContinuousDelivery?topic=ContinuousDelivery-event-notifications-cd&interface=ui#event-notifications-enable-cd).

## Configuring the {{site.data.keyword.en_short}} instance
{: #devsecops-env-configure}

### Including environment properties
{: #devsecops-en-configure-envprop}

After adding the {{site.data.keyword.en_short}} instance to your toolchain, incorporate an environment property named `event-notifications` and assign the value to `1`, across your PR, CI, CD, and CC toolchains.

### Configuring sources
{: #devsecops-en-configure-source}

1. Go to your {{site.data.keyword.en_short}} instance.
1. From the Sources section, select and enable the toolchains from which you want to receive messages.

### Configuring destinations
{: #devsecops-en-configure-destinations}

From the Destinations section, click **Add** and enter the required information.

### Configuring topics
{: #devsecops-en-configure-topics}

1. From the Topics section, click **Create**.
1. Enter a name and select the desired source. You can select multiple sources as needed.

### Configuring subscriptions
{: #devsecops-en-configure-sub}

1. From the Subscriptions section, click **Create**.
1. Enter a name and select the topic and destination.

## Sending custom messages to event notifications
{: #cd-devsecops-send-custom-message-en}

To send custom messages from a pipeline separate from one-pipeline standard messages, call the `"${COMMONS_PATH}/communications/post_message` script , for example:

```
echo "Here is the custom message" | "${COMMONS_PATH}/communications/post_message
```

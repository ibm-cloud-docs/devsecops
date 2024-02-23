---
copyright:
  years: 2024, 2024
lastupdated: "2024-02-23"

keywords: DevSecOps, IBM Cloud, compliance

subcollection: devsecops
---

{{site.data.keyword.attribute-definition-list}}

# Setting Up Event Notifications for DevSecOps Pipelines
{: #cd-devsecops-configure-en}

IBM Cloud® Event Notifications is a service for routing event notifications, alerting you to critical events in your IBM Cloud account or triggering automated actions via webhooks. You can filter and direct event notifications from IBM Cloud services to email, SMS, Webhooks, and Push Notifications.
{: shortdesc}

For detailed instructions, visit [IBM/event-notifications](https://cloud.ibm.com/docs/event-notifications){: external}.


## Provisioning an Event Notifications instance
{: #devsecops-en-creating}

If you don't have an event notification instance in your account, refer [Creating an Event Notifications Service Instance](https://cloud.ibm.com/docs/event-notifications?topic=event-notifications-en-create-en-instance).


## Adding an Event Notifications instance to toolchains
{: #devsecops-en-add-to-toolchain}

1. To intergrate event notifification to an existing toolchain, go to the IBM Cloud console, click the **menu** icon, and select **DevOps**. On the Toolchains page, select the toolchain to view its Overview page. Alternatively, from your app's Overview page, access the Continuous Delivery card, and click **View Toolchain**, then click **Overview**.
   a. Click **Add Tool**.
   b. In the Tool Integrations section, select **Event Notifications**.

2. Enter the name for this tool integration as it appears on the Event Notifications card in your toolchain. This name serves as the identifier for the tool integration within your toolchain.
3. Select the Event Notifications instance to connect to the toolchain.
4. Click **Create Integration** to add the Event Notifications tool integration to your toolchain.
5. On the Overview page of your toolchain, under the IBM Cloud Tools section, locate, and click **Event Notifications**.

For further guidance on adding event notification integration to your toolchain, refer to [Enabling Notifications](https://cloud.ibm.com/docs/ContinuousDelivery?topic=ContinuousDelivery-event-notifications-cd&interface=ui#event-notifications-enable-cd).

## Configuring the Event Notifications Instance
{: #devsecops-en-configure}

### Configuring Sources
{: #devsecops-en-configure-source}

1. Go to your Event Notification instance.
2. Go to the **Sources** section..
3. Select and enable the toolchains from which you wish to receive messages.

### Configuring Destinations
{: #devsecops-en-configure-destinations}

1. Go to your Event Notification instance.
1. Go to the **Destinations** section.
1. Click the `Add` button, provide a name, and enter the required destination details.

### Configuring Topics
{: #devsecops-en-configure-topics}

1. Go to your Event Notification instance.
1. Go to the **Topics** section.
1.  Click the `Create` button, provide a name, and select the desired source. You can select multiple sources as needed.

### Configuring Subscriptions
{: #devsecops-en-configure-sub}

1. Go to your Event Notification instance.
1. Go to the **Subscriptions** section.
1. Click the `Create` button, provide a name, and select the topic and destination.

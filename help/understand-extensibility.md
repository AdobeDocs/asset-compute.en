---
title: Understand about extending [!DNL Asset Compute Service].
description: When and how to extend the [!DNL Asset Compute Service] functionality to do custom asset processing.
---

# Introduction to extensibility {#introduction-to-extensibilty}

Many rendition requirements like converting to formats and resizing images are addressed by [Processing Profiles in [!DNL Experience Manager] as a Cloud Service](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/asset-microservices-overview.html). More complex business requirements may need a custom-created solution that suits an organization's needs. [!DNL Asset Compute Service] can be extended by creating custom workers that are invoked from Processing Profiles in [!DNL Experience Manager] and that address the need for these [supported use cases](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html).

>[!NOTE]
>
>[!DNL Asset Compute Service] is available only for use with [!DNL Experience Manager] as a Cloud Service.

Extending [!DNL Asset Compute Service] with custom workers is made simple through the [worker SDK](https://github.com/adobe/asset-compute-sdk) and Project Firefly developer tooling. This allows developers to focus on business logic. Writing custom workers is not more complex than writing a plain serverless Adobe I/O Runtime action, which itself is nothing more than a single Node.js JavaScript function. The [basic worker example](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-basic/worker-basic.js) showcases that.

## Prerequisites and provisioning requirements {#prerequisites-and-provisioning}

Ensure you meet the following prerequisites:

* Project Firefly tools are installed on your machine.
* An [!DNL Experience Cloud] organization. More information [here](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/setup.md#acquire-access-and-credentials).
* The Experience Organization must have [!DNL Experience Manager] as a Cloud Service enabled.
* [!DNL Adobe Experience Cloud] organization is part of the [!DNL Project Firefly] developer preview program. See [how to apply for access](https://github.com/AdobeDocs/project-firefly/blob/master/overview/getting_access.md).
* Ensure a developer role or administrator permissions in the organization for the developer.
* Ensure that [Adobe I/O CLI](https://github.com/adobe/aio-cli) is installed locally.

<!-- TBD for later:

* What all accesses and licenses are required?
* What all permissions are required to create, debug, and deploy custom workers?
* How do developers get access and provision the required apps?
* What is repository management?
* Anything on security and data transfer?
* What about handling personal or sensitive information?
* Custom worker SLA is dependent on SLAs of various services it depends on.
* Document how the devs can get to know the KPIs of their custom workers. The KPIs are dependent on the performance at Adobe's side, amongst other things.
-->

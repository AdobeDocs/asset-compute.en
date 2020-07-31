---
title: Introduction to the [!DNL Asset Compute Service].
description: [!DNL Asset Compute Service] is a cloud-native asset processing service that reduces complexity and improves scalability.
---

# Overview of [!DNL Asset Compute Service] {#overview}

[!DNL Asset Compute Service] is a scalable and extensible service of [!DNL Adobe Experience Cloud] to process digital assets. It can transform image, video, document and other file formats into different renditions including thumbnails, extracted text and metadata, and archives.

Developers can plugin custom asset workers to address custom use cases. The service works on the [!DNL Adobe I/O] runtime. It is extendable through [!DNL Project Firefly] headless apps written in Node.js. These can do custom operations such as calling external APIs to perform image operations or leverage [!DNL Adobe Sensei] support.

[!DNL Project Firefly] is a framework to build and deploy custom web applications on [!DNL Adobe I/O] runtime to extend Adobe Experience Cloud solutions. To create custom applications, the developers can leverage [!DNL React Spectrum] (Adobe’s UI toolkit), create microservices, create custom events, and orchestrate APIs. See [documentation of Project Firefly](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html).

>[!NOTE]
>
>Currently, the [!DNL Asset Compute Service] can only be used in [!DNL Experience Manager] as a Cloud Service. Administrators create processing profiles that can invoke the [!DNL Asset Compute Service] to pass assets for processing. See [use asset microservices and processing profiles](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html).

## Supported use cases of [!DNL Asset Compute Service] {#possible-use-cases-benefits}

[!DNL Asset Compute Service] supports a few common business use cases like basic image processing; Adobe application specific conversions; and custom workers creation that orchestrate complex business requirements. 

You can use [!DNL Asset Compute] web service to generate thumbnails for different file types, high-quality image renderings for the [supported file formats](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/file-format-support.html). See [use cases supported via custom configuration](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html#custom-config).

>[!NOTE]
>
>The service does not provide asset storage. It must be provided by consumers that provide references to source and rendition file locations in cloud storage.

<!-- TBD: Should this be mentioned in the docs?

|Asset Compute Service does not do this|Expectations from implementing client|
|---|---|
| Binary uploads or API-based asset ingestion. | Use other methods to ingest assets. |
| Store binaries or any persisted data across processing requests.| Each request is independent so treat it as a standalone request by sharing binary and processing instructions. |
| Store any configurations such as processing rules or settings for a user or an organization's account. | Add processing request to each request/instruction. |
| Direct event handling of asset creation events from storage systems and processing completed notifications, and errors. | Use Adobe I/O Events and other methods. |

-->

>[!MORELIKETHIS]
>
>* [Overview of asset processing with asset microservices in Adobe Experience Manager as a Cloud Service](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/asset-microservices-overview.html).
>* [Documentation of Project Firefly](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html).
>* [File formats supported for processing](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/file-format-support.html).
>* [Release notes of the Asset Compute Service](release-notes.md)

<!-- **TBD:**
* Clarify the service can only be used within AEM as Cloud Service. The docs provided as context for custom worker developers. Not to be used as a standalone service.
  ** and API as that will play a role in custom workers (accepting standard params, invoking Nui itself in the future, etc. (this is an outlook))

* link to aem as cloud service docs on asset ingestion and customization with processing profiles.
-->

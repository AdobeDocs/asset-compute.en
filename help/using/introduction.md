---
title: Introduction to the [!DNL Asset Compute Service]
description: "[!DNL Asset Compute Service] is a cloud-native asset processing service that reduces complexity and improves scalability."
exl-id: f8c89f65-5a94-44f3-aaac-4612ae291101
---
# Overview of [!DNL Asset Compute Service] {#overview}

[!DNL Asset Compute Service] is a scalable and extensible service of [!DNL Adobe Experience Cloud] to process digital assets. It can transform image, video, document, and other file formats into different renditions including thumbnails, extracted text and metadata, and archives.

Developers have the ability to plugin custom asset applications (also called custom workers) to address custom use cases. The service works on the Adobe [!DNL I/O Runtime]. It is extendable through [!DNL Adobe Developer App Builder] headless apps written in Node.js. They can do custom operations such as calling external APIs to perform image operations or leverage [!DNL Adobe Sensei] support.

[!DNL Adobe Developer App Builder] is a framework to build and deploy custom web applications on Adobe [!DNL I/O Runtime] to extend Adobe Experience Cloud solutions. To create custom applications, the developers can leverage [!DNL React Spectrum] (Adobe's UI toolkit), create microservices, create custom events, and orchestrate APIs. See [documentation of Adobe Developer App Builder](https://developer.adobe.com/app-builder/docs/overview/).

>[!NOTE]
>
>Currently, the [!DNL Asset Compute Service] can be used only via [!DNL Experience Manager] as a [!DNL Cloud Service]. Administrators create processing profiles that can call the [!DNL Asset Compute Service] to pass assets for processing. See [using asset microservices and processing profiles](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/assets/manage/asset-microservices-configure-and-use).

## Supported use cases of [!DNL Asset Compute Service] {#possible-use-cases-benefits}

[!DNL Asset Compute Service] supports a few common business use cases like basic image processing; Adobe application specific conversions; and custom applications creation that orchestrate complex business requirements.

You can use the [!DNL Asset Compute] web service to generate thumbnails for different file types, high-quality image renderings for the [supported file formats](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/assets/file-format-support). See [use cases supported via custom configuration](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/assets/manage/asset-microservices-configure-and-use).

>[!NOTE]
>
>The service does not provide asset storage. Users provide it and provide references to source and rendition file locations in cloud storage.

<!-- TBD: Should this be mentioned in the docs?

|Asset Compute Service does not do this|Expectations from implementing client|
|---|---|
| Binary uploads or API-based asset ingestion. | Use other methods to ingest assets. |
| Store binaries or any persisted data across processing requests.| Each request is independent so treat it as a standalone request by sharing binary and processing instructions. |
| Store any configurations such as processing rules or settings for a user or an organization's account. | Add processing request to each request/instruction. |
| Direct event handling of asset creation events from storage systems and processing completed notifications, and errors. | Use [!DNL Adobe I/O] Events and other methods. |

-->

>[!MORELIKETHIS]
>
>* [Overview of asset processing with asset microservices in [!DNL Adobe Experience Manager] as a [!DNL Cloud Service]](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/assets/asset-microservices-overview).
>* [Documentation of Adobe Developer App Builder](https://developer.adobe.com/app-builder/docs/overview).
>* [Supported file formats for processing](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/assets/file-format-support).

<!-- **TBD:**
* Clarify the service can only be used within AEM as Cloud Service. The docs provided as context for custom application developers. Not to be used as a standalone service.
  ** and API as that plays a role in custom applications (accepting standard params, invoking Nui itself in the future, etc. (this is an outlook))

* link to aem as cloud service docs on asset ingestion and customization with processing profiles.
-->

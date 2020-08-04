---
title: Release notes of [!DNL Asset Compute Service].
description: New features, enhancements, and known issues in [!DNL Asset Compute Service].
---

# Release notes of [!DNL Asset Compute Service] {#release-notes}

The latest release of [!DNL Asset Compute Service] is released on Jul 30, 2020.

<!--

To test your custom worker with the [developer tool](https://github.com/adobe/asset-compute-devtool), you need access to a [cloud storage container](https://github.com/adobe/asset-compute-devtool#prerequisites). Currently, Adobe supports Azure Blob Storage and AWS S3.

>[!NOTE]
>
>Cloud storage access is only required for using the developer tool. You can still create, test and deploy custom workers with out using the developer tool.
-->

## What is new {#what-is-new}

This is the first release of [!DNL Asset Compute Service]. It is a scalable and extensible service of [!DNL Adobe Experience Cloud] to process digital assets. It can transform image, video, document, and other file formats into different renditions including thumbnails, extracted text and metadata, and archives.

Currently, the [!DNL Asset Compute Service] can only be used in [!DNL Experience Manager] as a Cloud Service.

## Limitations and known issues {#known-limitations}

To test your custom worker with the [developer tool](https://github.com/adobe/asset-compute-devtool), you need access to a [cloud storage container](https://github.com/adobe/asset-compute-devtool#prerequisites).

* The cloud storage (different than the [!DNL Experience Manager] blob store) access is needed only for the developer tool. You can still create, test, and deploy custom workers without the developer tool.
* It can be a shared container used by multiple developers across different projects.

## Contribute {#contribute-open-source}

[!DNL Asset Compute Service] extensibility is developed under an open development model on [github.com/adobe](https://github.com/adobe) that welcomes contributions from extension developers. All the components that are relevant to developing, building, and testing custom workers are open source. See [how and where to contribute to Compute Service](contribute-to-compute-service.md).

<!-- **TBD:**
* Are we versioning the releases?
* Is there any compatibility information to be added? With Project Firefly versions, or AEMaaCS releases, or other offerings/integrations such as InDesign Server?
-->

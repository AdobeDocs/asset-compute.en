---
title: Architecture of [!DNL Asset Compute Service]
description: How [!DNL Asset Compute Service] API, applications, and SDK work together to provide a cloud-native asset processing service.
exl-id: 658ee4b7-5eb1-4109-b263-1b7d705e49d6
---
# Architecture of [!DNL Asset Compute Service] {#overview}

The [!DNL Asset Compute Service] is built on top of the serverless Adobe [!DNL `I/O Runtime`] platform. It provides Adobe Sensei content services support for assets. The invoking client (only [!DNL Experience Manager] as a [!DNL Cloud Service] is supported) is provided with the Adobe Sensei-generated information that it sought for the asset. The information returned is in JSON format.

[!DNL Asset Compute Service] is extendable by creating custom applications based on [!DNL Adobe Developer App Builder]. These custom applications are [!DNL Project Adobe Developer App Builder] headless apps and do tasks such as add custom conversion tools or call external APIs to perform image operations.

[!DNL Project Adobe Developer App Builder] is a framework to build and deploy custom web applications on the Adobe [!DNL `I/O Runtime`]. To create custom applications, the developers can leverage [!DNL React Spectrum] (Adobe's UI toolkit), create microservices, create custom events, and orchestrate APIs. See [documentation of Adobe Developer App Builder](https://developer.adobe.com/app-builder/docs/overview).

The foundation on which the architecture is based includes:

* The modularity of applications&ndash;only containing what is needed for a given task&ndash;allows to decouple applications from each other and keep them lightweight.

* The serverless concept of [!DNL Adobe I/O] Runtime yields numerous benefits: asynchronous, highly scalable, isolated, job-based processing, which is a perfect fit for asset processing.

* Binary cloud storage provides the necessary features for storing and accessing asset files and renditions individually, without requiring full access permissions to the storage, using pre-signed URL references. Transfer acceleration, CDN caching, and co-locating compute applications with cloud storage allow for optimal low latency content access. Both AWS and Azure clouds are supported.

![Architecture of Asset Compute Service](assets/architecture-diagram.png)

*Figure: Architecture of [!DNL Asset Compute Service] and how it integrates with [!DNL Experience Manager], storage, and processing application.*

The architecture consists of the following parts:

* **An API and orchestration layer** receives requests (in JSON format) which instruct the service to transform a source asset into multiple renditions. The requests are asynchronous and return with an activation ID that is job ID. Instructions are purely declarative, and for all standard processing work (for example, thumbnail generation, text extraction) consumers only specify the desired result, but not the applications that handle certain renditions. Generic API features such as authentication, analytics, rate limiting, are handled using the Adobe API Gateway in front of the service and manages all requests going to [!DNL Adobe I/O] Runtime. The application routing is done dynamically by the orchestration layer. Clients define custom applications for particular renditions, which come with their own set of unique parameters. Application execution can be fully parallelized as they are separate serverless functions in Adobe [!DNL `I/O Runtime`].

* **Applications to process assets** that specialize in certain types of file formats or target renditions. Conceptually, an application is like the UNIX&reg; pipe concept: an input file gets transformed into one or more output files.

* **A [common application library](https://github.com/adobe/asset-compute-sdk)** handles common tasks. For example, downloading the source file, uploading the renditions, error reporting, event sending, and monitoring. This design ensures that application development remains straightforward, adhering to the serverless concept, with interactions limited to the local filesystem.

<!-- TBD:

* About the YAML file?
* minimize description to custom applications
* remove all internal stuff (e.g. Photoshop application, API Gateway) from text and diagram
* update diagram to focus on 3rd party custom applications ONLY
* Explain important transactions/handshakes?
* Flow of assets/control? See the illustration on the Nui diagrams wiki.
* Illustrations. See the SVG shared by Alex.
* Exceptions? Limitations? Call-outs? Gotchas?
* Do we want to add what basic processing is not available currently, that is expected by existing AEM customers?
-->

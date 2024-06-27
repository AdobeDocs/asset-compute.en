---
title: "[!DNL Asset Compute Service] HTTP API"
description: "[!DNL Asset Compute Service] HTTP API to create custom applications."
exl-id: 4b63fdf9-9c0d-4af7-839d-a95e07509750
---
# [!DNL Asset Compute Service] HTTP API {#asset-compute-http-api}

The use of the API is limited to development purposes. The API is provided as a context when developing custom applications. [!DNL Adobe Experience Manager] as a [!DNL Cloud Service] uses the API to pass the processing information to a custom application. For more information, see [Use asset microservices and Processing Profiles](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/assets/manage/asset-microservices-configure-and-use).

>[!NOTE]
>
>[!DNL Asset Compute Service] is available only for use with [!DNL Experience Manager] as a [!DNL Cloud Service].

Any client of the [!DNL Asset Compute Service] HTTP API must follow this high-level flow:

1. A client is provisioned as an [!DNL Adobe Developer Console] project in an IMS organization. Each separate client (system or environment) requires its own separate project to separate the event data flow.

1. A client generates an access token for the technical account using the [JWT (Service Account) Authentication](https://developer.adobe.com/developer-console/docs/guides/).

1. A client calls [`/register`](#register) only once to retrieve the journal URL.

1. A client calls [`/process`](#process-request) for each asset for which it wants to generate renditions. The call is asynchronous.

1. A client regularly polls the journal to [receive events](#asynchronous-events). It receives events for each requested rendition when the rendition is successfully processed (`rendition_created` event type) or if there is an error (`rendition_failed` event type).

The [adobe-asset-compute-client](https://github.com/adobe/asset-compute-client) module makes it easy to use the API in Node.js code.

## Authentication and authorization {#authentication-and-authorization}

All APIs require access token authentication. The requests must set the following headers:

1. `Authorization` header with bearer token, which is the technical account token, received via [JWT exchange](https://developer.adobe.com/developer-console/docs/guides/) from Adobe Developer Console project. The [scopes](#scopes) are documented below.

<!-- TBD: Change the existing URL to a new path when a new path for docs is available. The current path contains master word that is not an inclusive term. Logged ticket in Adobe I/O's GitHub repo to get a new URL.
-->

1. `x-gw-ims-org-id` header with the IMS organization ID.

1. `x-api-key` with the client ID from the [!DNL Adobe Developers Console] project.

### Scopes {#scopes}

Ensure the following scopes for the access token:

* `openid`
* `AdobeID`
* `asset_compute`
* `read_organizations`
* `event_receiver`
* `event_receiver_api`
* `adobeio_api`
* `additional_info.roles`
* `additional_info.projectedProductContext`

These scopes require the [!DNL Adobe Developer Console] project to be subscribed to `Asset Compute`, `I/O Events`, and `I/O Management API` services. The breakdown of individual scopes is:

* Basic
  * scopes: `openid,AdobeID`

* Asset Compute
  * metascope: `asset_compute_meta`
  * scopes: `asset_compute,read_organizations`

* Adobe [!DNL `I/O Events`]
  * metascope: `event_receiver_api`
  * scopes: `event_receiver,event_receiver_api`

* Adobe [!DNL `I/O Management API`]
  * metascope: `ent_adobeio_sdk`
  * scopes: `adobeio_api,additional_info.roles,additional_info.projectedProductContext`

## Registration {#register}

Each client of the [!DNL Asset Compute service] - a unique [!DNL Adobe Developer Console] project subscribed to the service - must [register](#register-request) before making processing requests. The registration step returns the unique event journal that is required to retrieve the asynchronous events from rendition processing.

At the end of its lifecycle, a client can [unregister](#unregister-request).

### Register request {#register-request}

This API call sets up an [!DNL Asset Compute] client and provides the event journal URL. This process is an idempotent operation and only needs to be called once for each client. It can be called again to retrieve the journal URL.

| Parameter                | Value                                                |
|--------------------------|------------------------------------------------------|
| Method                   | `POST`                                               |
| Path                     | `/register`                                          |
| Header `Authorization`   | All [authorization related headers](#authentication-and-authorization). |
| Header `x-request-id`    | Optional, set by clients for a unique end-to-end identifier of the processing requests across systems. |
| Request body | Must be empty. |

### Register response {#register-response}

| Parameter             | Value                                                |
|-----------------------|------------------------------------------------------|
| MIME type             | `application/json`                                   |
| Header `X-Request-Id` | Either the same as the `X-Request-Id` request header or a uniquely generated one. Use for identifying requests across systems, or support requests, or both. |
| Response body         | A JSON object with `journal`, `ok`, or `requestId` fields. |

The HTTP status codes are:

* **200 Success**: When the request is successful. The `journal` URL receives notifications about the outcomes of the asynchronous processing initiated by way of `/process`. It alerts of `rendition_created` events upon successful completion, or `rendition_failed` events if the process fails.

  ```json
  {
      "ok": true,
      "journal": "https://api.adobe.io/events/organizations/xxxxx/integrations/xxxx/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "requestId": "1234567890"
  }
  ```

* **401 Unauthorized**: occurs when the request does not have valid [authentication](#authentication-and-authorization). An example might be an invalid access token or invalid API key.

* **403 Forbidden**: occurs when the request does not have valid [authorization](#authentication-and-authorization). An example might be a valid access token, but the Adobe Developer Console project (technical account) is not subscribed to all required services.

* **429 Too many requests**: occurs when this client or otherwise overloads the system. Clients should retry with an [exponential backoff](https://en.wikipedia.org/wiki/Exponential_backoff). The body is empty.
* **4xx error**: When there was any other client error and registration failed. Usually a JSON response such as this is returned, although that is not guaranteed for all errors:

  ```json
  {
      "ok": false,
      "requestId": "1234567890",
      "message": "error message"
  }
  ```

* **5xx error**: occurs when there was any other server side error and registration failed. Usually a JSON response such as this is returned, although that is not guaranteed for all errors:

  ```json
  {
      "ok": false,
      "requestId": "1234567890",
      "message": "error message"
  }
  ```

### Unregister request {#unregister-request}

This API call unregisters an [!DNL Asset Compute] client. After this unregistration occurs, it is no longer possible to call `/process`. Using the API call for an unregistered client or a yet-to-be registered client returns a `404` error.

| Parameter                | Value                                                |
|--------------------------|------------------------------------------------------|
| Method | `POST`          |
| Path   | `/unregister`   |
| Header `Authorization`   | All [authorization related headers](#authentication-and-authorization). |
| Header `x-request-id`    | Optional. Clients can set it for a unique end-to-end identifier of the processing requests across systems. |
| Request body | Empty. |

### Unregister response {#unregister-response}

| Parameter             | Value                                                |
|-----------------------|------------------------------------------------------|
| MIME type             | `application/json`                                   |
| Header `X-Request-Id` | Either the same as the `X-Request-Id` request header or a uniquely generated one. Use for identifying requests across systems or support requests. |
| Response body         | A JSON object with `ok` and `requestId` fields. |

The status codes are:

* **200 Success**: occurs when the registration and journal is found and removed.

  ```json
  {
      "ok": true,
      "requestId": "1234567890"
  }
  ```

* **401 Unauthorized**: occurs when the request does not have valid [authentication](#authentication-and-authorization). An example might be an invalid access token or invalid API key.

* **403 Forbidden**: occurs when the request does not have valid [authorization](#authentication-and-authorization). An example might be a valid access token, but the Adobe Developer Console project (technical account) is not subscribed to all required services.

* **404 Not found**: This status appears when the provided credentials are unregistered or invalid.

  ```json
  {
      "ok": true,
      "requestId": "1234567890"
  }
  ```

* **429 Too many requests**: occurs when the system is overloaded. Clients should retry with an [exponential backoff](https://en.wikipedia.org/wiki/Exponential_backoff). The body is empty.

* **4xx error**: occurs when there was any other client error and unregister failed. Usually a JSON response such as this is returned, although that is not guaranteed for all errors:

  ```json
  {
      "ok": false,
      "requestId": "1234567890",
      "message": "error message"
  }
  ```

* **5xx error**: occurs when there was any other server side error and registration failed. Usually a JSON response such as this is returned, although that is not guaranteed for all errors:

  ```json
  {
      "ok": false,
      "requestId": "1234567890",
      "message": "error message"
  }
  ```

## Process request {#process-request}

The `process` operation submits a job that transforms a source asset into multiple renditions, based on the instructions in the request. Notifications about successful completion (event type `rendition_created`) or any errors (event type `rendition_failed`) are sent to an Event journal that must be retrieved using [`/register`](#register) once before making any number of `/process` requests. Incorrectly formed requests immediately fail with a 400 error code.

Binaries are referenced using URLs, such as Amazon AWS S3 pre-signed URLs or Azure Blob Storage SAS URLs. Used for both reading the `source` asset (`GET` URLs) and writing the renditions (`PUT` URLs). The client is responsible for generating these pre-signed URLs.

| Parameter                | Value                                                |
|--------------------------|------------------------------------------------------|
| Method    | `POST`       |
| Path      | `/process`   |
| MIME type | `application/json` |
| Header `Authorization`   | All [authorization related headers](#authentication-and-authorization). |
| Header `x-request-id`    | Optional. Clients can set a unique end-to-end identifier to track processing requests across systems. |
| Request body | It must be in the process request JSON format as described below. It provides instructions on what asset to process and what renditions to generate. |

### Process request JSON {#process-request-json}

The request body of `/process` is a JSON object with this high-level schema:

```json
{
    "source": "",
    "renditions" : []
}
```

The available fields are:

| Name         | Type     | Description | Example |
|--------------|----------|-------------|---------|
| `source`     | `string` | URL of the source asset that is processed. Optional, based on requested rendition format (for example, `fmt=zip`). | `"http://example.com/image.jpg"` |
| `source`     | `object` | Describing the source asset that is processed. See description of [Source object fields](#source-object-fields) below. Optional based on requested rendition format (for example, `fmt=zip`). | `{"url": "http://example.com/image.jpg", "mimeType": "image/jpeg" }` |
| `renditions` | `array`  | Renditions to generate from the source file. Each rendition object supports a [rendition instruction](#rendition-instructions). Required. | `[{ "target": "https://....", "fmt": "png" }]` |

The `source` can either be a `<string>` that is seen as a URL or it can be an `<object>` with an additional field. The following variants are similar:

```json
"source": "http://example.com/image.jpg"
```

```json
"source": {
    "url": "http://example.com/image.jpg"
}
```

### Source object fields {#source-object-fields}

| Name      | Type     | Description | Example |
|-----------|----------|-------------|---------|
| `url`     | `string` | URL of the source asset to process. Required. | `"http://example.com/image.jpg"` |
| `name`    | `string` | Source asset file name. A file extension in the name might be used if no MIME type is detected. It has priority over the file name specified in the URL path. And, it has priority over the file name in the `content-disposition` header of the binary resource. Defaults to "file." | `"image.jpg"` |
| `size`    | `number` | Source asset file size in bytes. Takes precedence over `content-length` header of the binary resource. | `10234` |
| `mimetype`| `string` | Source asset file MIME type. Takes precedence over the `content-type` header of the binary resource. | `"image/jpeg"` |

### A complete `process` request example {#complete-process-request-example}

```json
{
    "source": "https://www.adobe.com/content/dam/acom/en/lobby/lobby-bg-bts2017-logged-out-1440x860.jpg",
    "renditions" : [{
            "name": "image.48x48.png",
            "target": "https://some-presigned-put-url-for-image.48x48.png",
            "fmt": "png",
            "width": 48,
            "height": 48
        },{
            "name": "image.200x200.jpg",
            "target": "https://some-presigned-put-url-for-image.200x200.jpg",
            "fmt": "jpg",
            "width": 200,
            "height": 200
        },{
            "name": "cqdam.xmp.xml",
            "target": "https://some-presigned-put-url-for-cqdam.xmp.xml",
            "fmt": "xmp"
        },{
            "name": "cqdam.text.txt",
            "target": "https://some-presigned-put-url-for-cqdam.text.txt",
            "fmt": "text"
    }]
}
```

## Process response {#process-response}

The `/process` request immediately returns with a success or a failure based on the basic request validation. Actual asset processing happens asynchronously.

| Parameter             | Value                                                |
|-----------------------|------------------------------------------------------|
| MIME type             | `application/json`                                   |
| Header `X-Request-Id` | Either the same as the `X-Request-Id` request header or a uniquely generated one. Use for identifying requests across systems or support requests. |
| Response body         | A JSON object with `ok` and `requestId` fields. |

Status codes:

* **200 Success**: If the request was successfully submitted. Response JSON includes `"ok": true`:

  ```json
  {
      "ok": true,
      "requestId": "1234567890"
  }
  ```

* **400 Invalid request**: If the request is improperly structured, for instance, if it lacks required fields in the JSON payload. Response JSON includes `"ok": false`:

  ```json
  {
      "ok": false,
      "requestId": "1234567890",
      "message": "error message"
  }
  ```

* **401 Unauthorized**: When the request does not have valid [authentication](#authentication-and-authorization). An example might be an invalid access token or invalid API key.
* **403 Forbidden**: When the request does not have valid [authorization](#authentication-and-authorization). An example might be a valid access token, but the Adobe Developer Console project (technical account) is not subscribed to all required services.
* **429 Too many requests**: Occurs when the system is overwhelmed, either due to this particular client or from overall demand. The clients can retry with an [exponential backoff](https://en.wikipedia.org/wiki/Exponential_backoff). The body is empty.
* **4xx error**: When there was any other client error. Usually a JSON response such as this is returned, although that is not guaranteed for all errors:

  ```json
  {
      "ok": false,
      "requestId": "1234567890",
      "message": "error message"
  }
  ```

* **5xx error**: When there was any other server side error. Usually a JSON response such as this is returned, although that is not guaranteed for all errors:

  ```json
  {
      "ok": false,
      "requestId": "1234567890",
      "message": "error message"
  }
  ```

Most clients are likely inclined to retry the same request with [exponential backoff](https://en.wikipedia.org/wiki/Exponential_backoff) on any error *except* configuration issues such as 401 or 403, or invalid requests like 400. Apart from regular rate limiting by way of 429 responses, a temporary service outage or limitation might result in 5xx errors. It would then be advisable to retry after a period of time.

All JSON responses (if present) include the `requestId`, which is the same value as the `X-Request-Id` header. Adobe recommends reading from the header because it is always present. The `requestId` is also returned in all events related to processing requests as `requestId`. Clients must not make any assumption about the format of this string. It is an opaque string identifier.

## Opt in to post-processing {#opt-in-to-post-processing}

The [Asset Compute SDK](https://github.com/adobe/asset-compute-sdk) supports a set of basic image post-processing options. Custom workers can explicitly opt in to post-processing by setting the field `postProcess` on the rendition object to `true`.

The supported use cases are:

* Crop is a rendition to a rectangle whose limits by crop.w, crop.h, crop.x, and crop.y are defined. The cropping details are specified in the rendition object's `instructions.crop` field.
* Resize images using width, height, or both. The `instructions.width` and `instructions.height` defines it in the rendition object. To resize using only width or height, set only one value. Compute Service conserves the aspect ratio.
* Set the quality for a JPEG image. The `instructions.quality` defines it in the rendition object. A quality level of 100 represents the highest quality, while lower numbers signify a decrease in quality.
* Create interlaced images. The `instructions.interlace` defines it in the rendition object.
* Set DPI to adjust the rendered size for desktop publishing purposes by adjusting the scale applied to the pixels. The `instructions.dpi` defines it in the rendition object to change dpi resolution. However, to resize the image so that it is the same size at a different resolution, use the `convertToDpi` instructions.
* Resize the image such that its rendered width or height remains the same as the original at the specified target resolution (DPI). The `instructions.convertToDpi` defines it in the rendition object.

## Watermark assets {#add-watermark}

The [Asset Compute SDK](https://github.com/adobe/asset-compute-sdk) supports adding a watermark to PNG, JPEG, TIFF, and GIF image files. The watermark is added following the rendition instructions in the `watermark` object on the rendition.

Watermarking is done during rendition post-processing. To watermark assets, the custom worker [opts into post-processing](#opt-in-to-post-processing) by setting the field `postProcess` on the rendition object to `true`. If the worker does not opt in, then watermarking is not applied, even if the watermark object is set on the rendition object in the request.

## Rendition instructions {#rendition-instructions}

The following are the available options for the `renditions` array in [`/process`](#process-request).

### Common fields {#common-fields}

| Name              | Type     | Description | Example |
|-------------------|----------|-------------|---------|
| `fmt`             | `string` | The renditions target format can also be `text` for text extraction and `xmp` for extracting XMP metadata as xml. See [supported formats](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/assets/file-format-support) | `png` |
| `worker`          | `string` | URL of a [custom application](develop-custom-application.md). Must be an `https://` URL. If this field is present, a custom application creates the rendition. Any other set rendition field is then used in the custom application. | `"https://1234.adobeioruntime.net`<br>`/api/v1/web`<br>`/example-custom-worker-master/worker"` |
| `target` | `string` | The URL to which the generated rendition should be uploaded using HTTP PUT. | `http://w.com/img.jpg` |
| `target`          | `object` | Multipart pre-signed URL upload information for the generated rendition. This information is for [AEM / Oak Direct Binary Upload](https://jackrabbit.apache.org/oak/docs/features/direct-binary-access.html) with this [multipart upload behavior](https://jackrabbit.apache.org/oak/docs/apidocs/org/apache/jackrabbit/api/binary/BinaryUpload.html).<br>Fields:<ul><li>`urls`: array of strings, one for each pre-signed part URL</li><li>`minPartSize`: the minimum size to use for one part = url</li><li>`maxPartSize`: the maximum size to use for one part = url</li></ul> | `{ "urls": [ "https://part1...", "https://part2..." ], "minPartSize": 10000, "maxPartSize": 100000 }` |
| `userData`        | `object` | Optional. The client controls the reserved space and passes it through as is to rendition events. Lets a client add custom information to identify rendition events. It must not be modified or relied upon in custom applications, as clients are free to change it at any time. | `{ ... }` |

### Rendition specific fields {#rendition-specific-fields}

For a list of currently supported file formats, see [supported file formats](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/assets/file-format-support).

| Name              | Type     | Description | Example |
|-------------------|----------|-------------|---------|
| `*`               | `*`      | Advanced, custom fields can be added that a [custom application](develop-custom-application.md) understands. | |
| `embedBinaryLimit`| `number` in bytes | When the file size of the rendition is less than the specified value, it is included in the event sent after its creation is finished. The maximum size allowed for embedding is 32 KB (32 x 1024 bytes). If a rendition is larger in size than the `embedBinaryLimit` limit, it is put at a location in cloud storage and is not embedded in the event. | `3276` |
| `width`           | `number` | Width in pixels. only for image renditions. | `200` |
| `height`          | `number` | Height in pixels. only for image renditions. | `200` |
|                   |          |  The aspect ratio is always maintained if: <ul> <li> Both `width` and `height` are specified, then image fits in the size while maintaining the aspect ratio </li><li> If just `width` or `height` is specified, the resulting image uses the corresponding dimension while keeping the aspect ratio</li><li> If `width` or `height` is not specified, the original image pixel size is used. It depends on the source type. For some formats, such as PDF files, a default size is used. There can be a maximum size limit.</li></ul> | |
| `quality`         | `number` | Specify jpeg quality in the range of `1` to `100`. Applicable only for image renditions. | `90` |
| `xmp`             | `string` | Used only by XMP metadata writeback, it is base64 encoded XMP to write back to the specified rendition. | |
| `interlace`       | `bool`   | Create interlaced PNG or GIF or progressive JPEG by setting it to `true`. It has no effect on other file formats. | |
| `jpegSize`        | `number` | Approximate size of JPEG file in bytes. It overrides any `quality` setting. It has no effect on other formats. | |
| `dpi`             | `number` or `object` | Set x and y DPI. For simplicity, it can also be set to a single number, which is used for both x and y. It has no effect on the image itself. | `96` or `{ xdpi: 96, ydpi: 96 }` |
| `convertToDpi`    | `number` or `object` | x and y DPI re-sample values while maintaining physical size. For simplicity, it can also be set to a single number, which is used for both x and y. | `96` or `{ xdpi: 96, ydpi: 96 }` |
| `files`           | `array`  | List of files to include in the ZIP archive (`fmt=zip`). Each entry can either be a URL string or an object with the fields:<ul><li>`url`: URL to download file</li><li>`path`: Store file under this path in the ZIP</li></ul> | `[{ "url": "https://host/asset.jpg", "path": "folder/location/asset.jpg" }]` |
| `duplicate`       | `string` | Duplicate handling for ZIP archives (`fmt=zip`). By default multiple files stored under the same path in the ZIP generates an error. Setting `duplicate` to `ignore` results in only the first asset to be stored and the rest to be ignored. | `ignore` |
| `watermark`       | `object` | Contains instructions about the [watermark](#watermark-specific-fields). |  |

### Watermark-specific fields {#watermark-specific-fields}

PNG format is used as a watermark.

| Name              | Type     | Description | Example |
|-------------------|----------|-------------|---------|
| `scale`           | `number` | Scale of the watermark, between `0.0` and `1.0`. `1.0` means that the watermark has its original scale (1:1) and the lower values reduce the watermark size. | A value of `0.5` means half of original size. |
|`image`| `url` | URL to the PNG file to use to watermark. | |

## Asynchronous events {#asynchronous-events}

When processing of a rendition is finished or when an error occurs, an event is sent to an Adobe [!DNL `I/O Events Journal`]. Clients must listen to the journal URL provided through [`/register`](#register). The journal response includes an `event` array consisting of one object for each event, of which the `event` field includes the actual event payload.

The Adobe [!DNL `I/O Events`] type for all events of the [!DNL Asset Compute Service] is `asset_compute`. The journal is automatically subscribed to this event type only and there is no further requirement to filter based on the [!DNL Adobe Developer] Event type. The service specific event types are available in the `type` property of the event.

### Event types {#event-types}

| Event               | Description |
|---------------------|-------------|
| `rendition_created` | Sent for each successfully processed and uploaded rendition. |
| `rendition_failed`  | Sent for each rendition that failed to process or upload. |

### Event attributes {#event-attributes}

| Attribute   | Type     | Event         | Description |
|-------------|----------|---------------|-------------|
| `date`      | `string` | `*`           | Timestamp when the event was sent in simplified extended [ISO-8601](https://en.wikipedia.org/wiki/ISO_8601) format, as defined by JavaScript [Date.toISOString()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/toISOString). |
| `requestId` | `string` | `*`           | The request id of the original request to `/process`, same as `X-Request-Id` header. |
| `source`    | `object` | `*`           | The `source` of the `/process` request. |
| `userData`  | `object` | `*`           | The `userData` of the rendition from the `/process` request if set. |
| `rendition` | `object` | `rendition_*` | The corresponding rendition object passed in `/process`. |
| `metadata`  | `object` | `rendition_created` | The [metadata](#metadata) properties of the rendition. |
| `errorReason`  | `string` | `rendition_failed` | Rendition failure [reason](#error-reasons) if any. |
| `errorMessage` | `string` | `rendition_failed` | The text giving more detail about the rendition failure if any. |

### Metadata {#metadata}

| Property  | Description |
|--------|-------------|
| `repo:size` | The size of the rendition in bytes. |
| `repo:sha1` | The sha1 digest of the rendition. |
| `dc:format` | The MIME type of the rendition. |
| `repo:encoding` | The charset encoding of the rendition in case it is a text-based format. |
| `tiff:ImageWidth`  | The width of the rendition in pixels. Only present for image renditions. |
| `tiff:ImageLength` | The length of the rendition in pixels. Only present for image renditions. |

### Error reasons {#error-reasons}

| Reason  | Description |
|---------|-------------|
| `RenditionFormatUnsupported` | The requested rendition format is unsupported for the given source. |
| `SourceUnsupported` | The specific source is unsupported even though the type is supported. |
| `SourceCorrupt`     | The source data is corrupt. It includes empty files. |
| `RenditionTooLarge` | The rendition could not be uploaded using the pre-signed URLs provided in `target`. The actual rendition size is available as metadata in `repo:size` and is used by the client to re-process this rendition with the right number of pre-signed URLs. |
| `GenericError`      | Any other unexpected error. |

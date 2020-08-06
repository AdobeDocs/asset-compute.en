---
title: Deploy [!DNL Asset Compute Service] custom application.
description: Deploy [!DNL Asset Compute Service] custom application.
---

# Deploy a custom application {#deploy-custom-application}

To deploy your application, use [aio app deploy](https://github.com/adobe/aio-cli#aio-appdeploy) command. In the terminal, the command displays a URL to access the custom application. The URL is of the format `https://[namespace].adobeio-static.net/api/v1/web/[appname]-[appversion]/[workername]`.

To get the same URL without redeploying the application, use [`aio app get-url`](https://github.com/adobe/aio-cli#aio-appget-url-action) command.

Use the URL in a [Processing Profile in Experience Manager as a Cloud Service](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/assets/manage/asset-microservices-configure-and-use.html) to integrate your application with [!DNL Experience Manager] as a Cloud Service.

Ensure that your Firefly project and workspace correspond to the [!DNL Experience Manager] as a Cloud Service environment where you want to use your action. It has different environments for development, staging, and production. You can verify the environment by checking `AIO_runtime_*` credentials that are defined inside your ENV file in the root of your Firefly application. For example, to deploy to a `Stage` workspace, the `AIO_runtime_namespace` is of the format `xxxxxx_xxxxxxxxx_stage`. To integrate with [!DNL Experience Manager] as a Cloud Service Production environment, use application URLs from your Firefly `Production` workspace.

>[!CAUTION]
>
>Do not use a personal workspace on critical [!DNL Experience Manager] environments.

>[!MORELIKETHIS]
>
>* [Understand and manage environments in Experience Manager as a Cloud Service](https://docs.adobe.com/content/help/en/experience-manager-cloud-service/implementing/using-cloud-manager/manage-environments.html).

---
title: Set the development environment required for [!DNL Asset Compute Service]
description: Developer environment setup for [!DNL Asset Compute Service] to start creating and testing custom code.
exl-id: 91c12889-01d8-4757-9bdd-f73c491cd9d5
---
# Setup a developer environment {#create-dev-environment}

To create a setup that allows you to develop for [!DNL Asset Compute Service], follow these requirements and instructions.

1. [Acquire access and credentials](https://www.adobe.io/project-firefly/docs/getting_started/#acquire-access-and-credentials) for [!DNL Project Firefly].

1. [Set up the local environment](https://www.adobe.io/project-firefly/docs/getting_started/#local-environment-set-up) and the required tools.

1. Some more tools that help you get started developing smoothly are:

   * [Git](https://git-scm.com/)
   * [Docker Desktop](https://www.docker.com/get-started)
   * [NodeJS](https://nodejs.org) (v12 to v14 LTS, odd versions are not recommended) and [NPM](https://www.npmjs.com). User of OSX HomeBrew can do `brew install node` to install both. Otherwise, download it from the [NodeJS download page](https://nodejs.org/en/)
   * An IDE that is good for NodeJS, we recommend [Visual Studio Code (VS Code)](https://code.visualstudio.com) as it is the supported IDE for the debugger. You can use any other IDE as a code editor, but advanced usage (e.g. debugger) is not yet supported
   * Install the latest[[!DNL Adobe I/O] CLI](https://github.com/adobe/aio-cli) (`aio`)
   <!-- - install using `npm install -g @adobe/aio-cli@7.1.0` -->

1. Make sure to meet the [prerequisites](/help/understand-extensibility.md#prerequisites-and-provisioning)

<!--
>[!NOTE]
>
>For now, use [!DNL Adobe I/O] CLI v7.1.0 of and do not use [!DNL Adobe I/O] CLI v8.
-->

## Setup a App Builder project {#create-App-Builder-project}

1. Ensure system administrator or developer role in the [!DNL Experience Cloud] organization. This is set up by a system admin in the [Admin Console](https://adminconsole.adobe.com/overview).

1. Log onto the [Adobe Developer Console](https://console.adobe.io/). Ensure you are part of the same [!DNL Experience Cloud] organization as the [!DNL Experience Manager] as a [!DNL Cloud Service] integration. For more information about Adobe Developer Console, see [Console documentation](https://www.adobe.io/apis/experienceplatform/console/docs.html).

1. [Create a Firefly project](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html#!AdobeDocs/project-firefly/master/getting_started/first_app.md). Click **[!UICONTROL Create new project]** > **[!UICONTROL Project from template]**. Select App Builder. It creates a new App Builder Project with two workspaces: `Production` and `Stage`. Add additional workspaces, for example `Development`, as required.

1. In the App Builder Project, select a workspace and subscribe to the services needed for Asset Compute. Click **Add to Project** > **API** and add `Asset Compute`, `IO Events`, and `IO Events Management` services. When adding the first API, it prompts to create a private key. Save this information on your machine as you need this key to test your custom application with the developer tool.

## Next step {#next-step}

Now that your environment is set up, you are ready to [create a custom application](develop-custom-application.md).

<!-- More ideas:
 
* Any steps in the beginning that lead to gotchas later should be called out for caution? For example,
  * don't change some defaults initially
  * know risks when deviating from standard path
  * naming conventions to follow
  * Retrieve and format credentials (YAML file details)

TBD: When aio-cli v8 bugs are resolved, update the AIO CLI install command to remove v7.x reference and instruct users to use the latest version. See CQDOC-18346.

-->

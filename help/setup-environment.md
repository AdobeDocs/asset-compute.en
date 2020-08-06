---
title: Set the development environment required for [!DNL Asset Compute Service].
description: Developer environment setup for [!DNL Asset Compute Service] to start creating and testing custom code.
---

# Setup a developer environment {#create-dev-environment}

To create a setup that allows you to develop for [!DNL Asset Compute Service], follow these requirements and instructions.

1. [Acquire access and credentials](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/setup.md#acquire-access-and-credentials) for Project Firefly.

1. [Set up the local environment](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/setup.md#local-environment-set-up) and the required tools.

1. Some more tools that help you get started developing smoothly are:

   * [Git](https://git-scm.com/).
   * [Docker Desktop](https://www.docker.com/get-started).
   * [NodeJS](https://nodejs.org) (v10 to v12 LTS, odd versions are not recommended) and [NPM](https://www.npmjs.com). User of OSX HomeBrew can do `brew install node` to install both. Otherwise, download it from the [NodeJS download page](https://nodejs.org/en/).
   * An IDE that is good for NodeJS, we recommend [Visual Studio Code (VS Code)](https://code.visualstudio.com) as it is the supported IDE for the debugger. You can use any other IDE as a code editor, but advanced usage (e.g. debugger) is not yet supported.
   * [AIO CLI](https://github.com/adobe/aio-cli) (`aio`) - install using `npm install -g @adobe/aio-cli`.

1. Make sure to meet the [prerequisites](/help/understand-extensibility.md#prerequisites-and-provisioning).

## Setup a Firefly project {#create-firefly-project}

1. Be granted System Admin or Developer Role access in the Experience Organization. This can be set by a System Admin in the [Admin Console](https://adminconsole.adobe.com/overview).

1. Log onto the [Adobe Developer Console](https://console.adobe.io/). Ensure you are part of the same Adobe Experience Cloud Organization as the AEM as a Cloud Service integration. For more information about Adobe Developer Console, see [Console documentation](https://www.adobe.io/apis/experienceplatform/console/docs.html).

1. [Create a Firefly project](https://www.adobe.io/apis/experienceplatform/project-firefly/docs.html#!AdobeDocs/project-firefly/master/getting_started/first_app.md). Click **[!UICONTROL Create new project]** > **[!UICONTROL Project from template]**. Select Firefly. It creates a new Firefly Project with two workspaces: `Production` and `Stage`. Add additional workspaces, for example `Development`, as required.

1. In the Firefly Project, select a workspace and subscribe to the services needed for Asset Compute. Click **Add to Project** > **API** and add `Asset Compute`, `IO Events`, and `IO Events Management` services. When adding the first API, it prompts to create a private key. Save this information on your machine as you need this key to test your custom application with the developer tool.

## Next step {#next-step}

Now that your environment is set up, you are ready to [create a custom application](develop-custom-application.md).

<!-- TBD items for later:
 
* Any steps in the beginning that lead to gotchas later should be called out for caution? For example,
  * don't change some defaults initially
  * know risks when deviating from standard path
  * naming conventions to follow
  * Retrieve and format credentials (YAML file details)
-->

---
title: Develop for [!DNL Asset Compute Service]
description: Create custom applications using [!DNL Asset Compute Service].
exl-id: a0c59752-564b-4bb6-9833-ab7c58a7f38e
---
# Develop a custom application {#develop}

Before you begin to develop a custom application:

* Ensure that all the [prerequisites](/help/using/understand-extensibility.md#prerequisites-and-provisioning) are met.
* Install the [required software tools](/help/using/setup-environment.md#create-dev-environment).
* See [set up your environment](setup-environment.md) to make sure you are ready to create a custom application.

## Create a custom application {#create-custom-application}

Make sure to have [Adobe aio-cli](https://github.com/adobe/aio-cli) installed locally.

1. To create a custom application, [create an App Builder project](https://developer.adobe.com/app-builder/docs/getting_started/first_app/#4-bootstrapping-new-app-using-the-cli). To do so, run `aio app init <app-name>` in your terminal.

    If you have not logged in already, this command prompts a browser asking you to sign into the [Adobe Developer Console](https://developer.adobe.com/console/user/servicesandapis) with your Adobe ID. See [here](https://developer.adobe.com/app-builder/docs/getting_started/first_app/#3-signing-in-from-cli) for more information on signing in from the cli.

    Adobe recommends that you should log in first. If you are having issues, then follow the instructions [to create an app without logging in](https://developer.adobe.com/app-builder/docs/getting_started/first_app/#42-developer-is-not-logged-in-as-enterprise-organization-user).

1. After logging in, follow the prompts in the CLI and select the `Organization`, `Project`, and `Workspace` to use for the application. Choose the project and workspace that you created when you [set up your environment](setup-environment.md). When prompted `Which extension point(s) do you wish to implement ?`, make sure to select `DX Asset Compute Worker`:

    ```sh
    $ aio app init <app-name>
    Retrieving information from [!DNL Adobe I/O] Console.
    ? Select Org My Adobe Org
    ? Select Project MyAdobe Developer App BuilderProject
    ? Which extension point(s) do you wish to implement ? (Press <space> to select, <a>
    to toggle all, <i> to invert selection)
    ❯◯ DX Experience Cloud SPA
    ◯ DX Asset Compute Worker
    ```

1. When prompted with `Which Adobe I/O App features do you want to enable for this project?`, select `Actions`. Make sure to deselect `Web Assets` option as web assets use different authentication and authorization checks.

    ```bash
    ? Which Adobe I/O App features do you want to enable for this project?
    select components to include (Press <space> to select, <a> to toggle all, <i> to invert selection)
    ❯◉ Actions: Deploy Runtime actions
    ◯ Events: Publish to Adobe I/O Events
    ◯ Web Assets: Deploy hosted static assets
    ◯ CI/CD: Include GitHub Actions based workflows for Build, Test and Deploy
    ```

1. When prompted `Which type of sample actions do you want to create?`, make sure to select `Adobe Asset Compute Worker`:

    ```bash
    ? Which type of sample actions do you want to create?
    Select type of actions to generate
    ❯◉ Adobe Asset Compute Worker
    ◯ Generic
    ```

1. Follow the rest of the prompts and open the new application in Visual Studio Code (or your favorite code editor). It contains the scaffolding and sample code for a custom application.

    Read here about the [main components of an App Builder app](https://developer.adobe.com/app-builder/docs/getting_started/first_app/#5-anatomy-of-an-app-builder-application).

    The template application leverages Adobe's [Asset Compute SDK](https://github.com/adobe/asset-compute-sdk#asset-compute-sdk) for the uploading, downloading, and orchestration of application renditions so developers only need to implement the custom application logic. Inside the `actions/<worker-name>` folder, the `index.js` file is where to add the custom application code.

See [example custom applications](#try-sample) for examples and ideas for custom applications.

### Add credentials {#add-credentials}

As you log in when creating the application, most of the App Builder credentials get collected in your ENV file. However, using the developer tool requires additional credentials.

<!-- TBD: Check if manual setup of credentials is required.
Manual set up of credentials is removed from troubleshooting and best practices page. Link was broken.
If you did not log in, refer to our troubleshooting guide to [set up credentials manually](troubleshooting.md).
-->

#### Developer tool storage credentials {#developer-tool-credentials}

The tool for developers to evaluate custom apps using the [!DNL Asset Compute service] necessitates the use of a cloud storage container. This container is essential to store test files and for the reception and presentation of renditions produced by the apps.

   >[!NOTE]
   >
   >This container is separate from the cloud storage of [!DNL Adobe Experience Manager] as a [!DNL Cloud Service]. It only applies for developing and testing with the Asset Compute developer tool.

Make sure to have access to a [supported cloud storage container](https://github.com/adobe/asset-compute-devtool#prerequisites). This container is used collectively by various developers for different projects whenever necessary.

#### Add credentials to ENV file {#add-credentials-env-file}

Insert the subsequent credentials for the development tool into the `.env` file. The file is located at the root of your App Builder project:

1. Add the absolute path to the private key file created while adding services to your App Builder Project:

    ```conf
    ASSET_COMPUTE_PRIVATE_KEY_FILE_PATH=
    ```

1. Download the file from the Adobe Developer Console. Go to the root of the project and click on "Download All" in the upper-right corner. The file is downloaded with `<namespace>-<workspace>.json` as the filename. Do one of the following:

   * Rename the file as `console.json` and move it in the root of your project.
   * Optionally, you can add the absolute path to the Adobe Developer Console integration JSON file. This file is the same [`console.json`](https://developer.adobe.com/app-builder/docs/getting_started/first_app/#42-developer-is-not-logged-in-as-enterprise-organization-user) file that is downloaded in your project workspace.

      ```conf
      ASSET_COMPUTE_INTEGRATION_FILE_PATH=
      ```

1. Add either S3 or Azure storage credentials. You only need access to one cloud storage solution.

    ```conf
    # S3 credentials
    S3_BUCKET=
    AWS_ACCESS_KEY_ID=
    AWS_SECRET_ACCESS_KEY=
    AWS_REGION=

    # Azure Storage credentials
    AZURE_STORAGE_ACCOUNT=
    AZURE_STORAGE_KEY=
    AZURE_STORAGE_CONTAINER_NAME=
    ```

>[!TIP]
>
>The `config.json` file contains credentials. From within your project, add the JSON file to your `.gitignore` file to prevent its sharing. The same applies to `.env` and `.aio` files.

## Execute the application {#run-custom-application}

Before executing the application with the Asset Compute developer tool, properly configure the [credentials](#developer-tool-credentials).

To run the application in the developer tool, use `aio app run` command. It deploys the action to Adobe [!DNL I/O Runtime], and starts the development tool on your local computer. This tool is used to test application requests during development. Here is an example rendition request:

```json
"renditions": [
    {
        "worker": "https://1234_my_namespace.adobeioruntime.net/api/v1/web/example-custom-worker-master/worker",
        "name": "image.jpg"
    }
]
```

>[!NOTE]
>
>Do not use the `--local` flag with the `run` command. It does not work with [!DNL Asset Compute] custom applications and the Asset Compute developer tool. Custom applications are called by the [!DNL Asset Compute] service that cannot access actions running on the developer's local computers.

See [here](test-custom-application.md) how to test and debug your application. When you are finished developing your custom application, [deploy your custom application](deploy-custom-application.md).

## Try the sample application provided by Adobe {#try-sample}

The following are example custom applications:

* [worker-basic](https://github.com/adobe/asset-compute-example-workers/tree/master/projects/worker-basic)
* [worker-animal-pictures](https://github.com/adobe/asset-compute-example-workers/tree/master/projects/worker-animal-pictures)

### Template custom application {#template-custom-application}

The [worker-basic](https://github.com/adobe/asset-compute-example-workers/tree/master/projects/worker-basic) is a template application. It generates a rendition by simply copying the source file. The content of this application is the template received when choosing `Adobe Asset Compute` in the creation of the aio app.

The application file, [`worker-basic.js`](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-basic/worker-basic.js) uses the [`asset-compute-sdk`](https://github.com/adobe/asset-compute-sdk#overview) to download the source file, orchestrate each rendition processing, and upload the resulting renditions back to cloud storage.

The [`renditionCallback`](https://github.com/adobe/asset-compute-sdk#rendition-callback-for-worker-required) defined inside the application code is where to perform all the application processing logic. The rendition callback in `worker-basic` simply copies the source file contents to the rendition file.

```javascript
const { worker } = require('@adobe/asset-compute-sdk');
const fs = require('fs').promises;

exports.main = worker(async (source, rendition) => {
    // copy source to rendition to transfer 1:1
    await fs.copyFile(source.path, rendition.path);
});
```

## Call an external API {#call-external-api}

In the application code, you can make external API calls to help with application processing. An example application file that invokes an external API is below.

```javascript
exports.main = worker(async function (source, rendition) {

    const response = await fetch('https://adobe.com', {
        method: 'GET',
        Authorization: params.AUTH_KEY
    })
});
```

For example, the [`worker-animal-pictures`](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-animal-pictures/worker-animal-pictures.js#L46) makes a fetch request to a static URL from Wikimedia using the [`node-httptransfer`](https://github.com/adobe/node-httptransfer#node-httptransfer) library.

<!-- TBD: Revisit later to see if this note is required.
>[!NOTE]
>
>For extra authorization for these API calls, see [custom authorization checks](#custom-authorization-checks).
-->

### Pass custom parameters {#pass-custom-parameters}

You can pass custom defined parameters through the rendition objects. They can be referenced inside the application in [`rendition` instructions](https://github.com/adobe/asset-compute-sdk#rendition). An example of a rendition object is:

```json
"renditions": [
    {
        "worker": "https://1234_my_namespace.adobeioruntime.net/api/v1/web/example-custom-worker-master/worker",
        "name": "image.jpg",
        "my-custom-parameter": "my-custom-parameter-value"
    }
]
```

An example of an application file accessing a custom parameter is the following:

```javascript
exports.main = worker(async function (source, rendition) {

    const customParam = rendition.instructions['my-custom-parameter'];
    console.log('Custom paramter:', customParam);
    // should print out `Custom parameter: "my-custom-parameter-value"`
});
```

The `example-worker-animal-pictures` passes a custom parameter [`animal`](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-animal-pictures/worker-animal-pictures.js#L39) to determine which file to fetch from Wikimedia.

## Authentication and authorization support {#authentication-authorization-support}

By default, Asset Compute custom applications come with Authorization and Authentication checks for the App Builder project. Enabled by setting the `require-adobe-auth` annotation to `true` in the `manifest.yml`.

### Access other Adobe APIs {#access-adobe-apis}

<!-- TBD: Revisit this section. Where do we document console workspace creation?
-->

Add the API services to the [!DNL Asset Compute] Console workspace created in setup. These services are part of the JWT access token generated by [!DNL Asset Compute Service]. The token and other credentials are accessible inside the application action `params` object.

```javascript
const accessToken = params.auth.accessToken; // JWT token for Technical Account with entitlements from the console workspace to the API service
const clientId = params.auth.clientId; // Technical Account client Id
const orgId = params.auth.orgId; // Experience Cloud Organization
```

### Pass credentials for third-party systems {#pass-credentials-for-tp}

To handle credentials for other external services, pass them as default parameters on the actions. They are automatically encrypted in transit. For more information, see [creating actions in the Adobe I/O Runtime developer guide](https://developer.adobe.com/runtime/docs/guides/using/creating_actions/). Then set them using environment variables during deployment. These parameters can be accessed in the `params` object inside the action.

Set the default parameters inside the `inputs` in the `manifest.yml`:

```yaml
packages:
  __APP_PACKAGE__:
    actions:
      worker:
        function: 'index.js'
        runtime: 'nodejs:10'
        web: true
        inputs:
           secretKey: $SECRET_KEY
        annotations:
          require-adobe-auth: true
```

The `$VAR` expression reads the value from an environment variable named `VAR`.

While developing, you can assign the value in the local `.env` file. The reason is because `aio` automatically imports environment variables from `.env` files, along with those variables set by the initiating shell. In this example, the `.env` file looks like the following:

```CONF
#...
SECRET_KEY=secret-value
```

For production deployment one might set the environment variables in the CI system, for example using secrets in GitHub Actions. Lastly, access the default parameters inside the application as such:

```javascript
const key = params.secretKey;
```

## Sizing applications {#sizing-workers}

An application runs in a container in Adobe [!DNL I/O Runtime] with [limits](https://developer.adobe.com/runtime/docs/guides/using/system_settings/) that can be configured through the `manifest.yml`:

```yaml
    actions:
      myworker:
        function: /actions/myworker/index.js
        limits:
          timeout: 300000
          memorySize: 512
          concurrency: 1
```

Due to extensive processing done by Asset Compute applications, you must adjust these limits for optimal performance (large enough to handle binary assets) and efficiency (not wasting resources due to unused container memory).

The default timeout for actions in Runtime is a minute but it can be increased by setting the `timeout` limit (in milliseconds). If you expect to process larger files, increase this time. Consider the total time that it takes to download the source, process the file and upload the rendition. If an action times out, that is, it does not return the activation before the specified timeout limit, Runtime discards the container and not reuse it.

Asset Compute applications by nature tend to be network and disk Input or output bound. The source file must be downloaded first. Processing is often resource-intensive and then the resulting renditions are uploaded again.

You can specify the memory allocated to an action container in megabytes using the `memorySize` parameter. Currently, this parameter also defines how much CPU access the container gets, and most importantly it is a key element of the cost of using Runtime (larger containers cost more). Use a larger value here when your processing requires more memory or CPU but be careful not to waste resources as the larger the containers are, the lower the overall throughput is.

Furthermore, it is possible to control action concurrency within a container using the `concurrency` setting. This setting is the number of concurrent activations a single container (of the same action) gets. In this model, the action container is like a Node.js server receiving multiple concurrent requests, up to that limit. The default `memorySize` in the runtime is set to 200 MB, ideal for smaller App Builder actions. For Asset Compute applications, this default may be excessive due to their heavier local processing and disk usage. Some applications, depending on their implementation, might also not work well with concurrent activity. The Asset Compute SDK ensures that activations are separated by writing files to different unique folders.

Test applications to find the optimal numbers for `concurrency` and `memorySize`. Larger containers = higher memory limit could allow for more concurrency but could also be wasteful for lower traffic.

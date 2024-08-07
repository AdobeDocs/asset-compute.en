---
title: Test and debug [!DNL Asset Compute Service] custom application
description: Test and debug [!DNL Asset Compute Service] custom application.
exl-id: c2534904-0a07-465e-acea-3cb578d3bc08
---
# Test and debug a custom application {#test-debug-custom-worker}

## Run unit tests for a custom application {#test-custom-worker}

Install [Docker Desktop](https://www.docker.com/get-started) on your machine. To test a custom worker, run the following command at the root of the application:

```bash
$ aio app test
```

<!-- TBD
To run tests for a custom application, run `aio asset-compute test-worker` command at the root of the custom application application.

Document interactively running `adobe-asset-compute` commands `test-worker` and `run-worker`.
-->

This command runs a custom unit test framework for Asset Compute application actions in the project as described below. It is hooked up through a configuration in the `package.json` file. It is also possible to have JavaScript unit tests such as Jest. The `aio app test` runs both.

The [aio-cli-plugin-asset-compute](https://github.com/adobe/aio-cli-plugin-asset-compute#install-as-local-devdependency) plugin is embedded as a development dependency in the custom application app so that it doesn't need to be installed on build/test systems.

### Application unit test framework {#unit-test-framework}

The Asset Compute application unit test framework lets you test applications without writing any code. It relies on the source to rendition file principle of applications. A certain file and folder structure has to be set up to define test cases with test source files, optional parameters, expected renditions and custom validation scripts. By default, the renditions are compared for byte equality. Furthermore, external HTTP services can be easily mocked using simple JSON files.

### Add tests {#add-tests}

Tests are expected inside the `test` folder at the root level of the project. The test cases for each application should be in the path `test/asset-compute/<worker-name>`, with one folder for each test case:

```yaml
action/
manifest.yml
package.json
...
test/
  asset-compute/
    <worker-name>/
        <testcase1>/
            file.jpg
            params.json
            rendition.png
        <testcase2>/
            file.jpg
            params.json
            rendition.gif
            validate
        <testcase3>/
            file.jpg
            params.json
            rendition.png
            mock-adobe.com.json
            mock-console.adobe.io.json
```

Have a look at [example custom applications](https://github.com/adobe/asset-compute-example-workers/) for some examples. Below is a detailed reference.

### Test output {#test-output}

The `build` directory at the root of the Adobe Developer App Builder app houses the detailed test results and logs of the custom application. These details are also displayed in the output of the `aio app test` command.

### Mock external services {#mock-external-services}

You can simulate external service calls within your actions by creating `mock-<HOST_NAME>.json` files for your test scenarios, with HOST_NAME being the specific host you intend to imitate. An example use case is an application that makes a separate call to S3. The new test structure would look like this:

```json
test/
  asset-compute/
    <worker-name>/
      <testcase3>/
        file.jpg
        params.json
        rendition.png
        mock-<HOST_NAME1>.json
        mock-<HOST_NAME2>.json
```

The mock file is a JSON formatted http response. For more information, see [this documentation](https://www.mock-server.com/mock_server/creating_expectations.html). If there are multiple host names to mock, define multiple `mock-<mocked-host>.json` files. Below is a sample mock file for `google.com` named `mock-google.com.json`:

```json
[{
    "httpRequest": {
        "path": "/images/hello.txt"
        "method": "GET"
    },
    "httpResponse": {
        "statusCode": 200,
        "body": {
          "message": "hello world!"
        }
    }
}]
```

The example `worker-animal-pictures` contains a [mock file](https://github.com/adobe/asset-compute-example-workers/blob/master/projects/worker-animal-pictures/test/asset-compute/worker-animal-pictures/simple-test/mock-upload.wikimedia.org.json) for the Wikimedia service that it interacts with.

#### Share files across test cases {#share-files-across-test-cases}

Adobe recommends using relative symlinks if you share `file.*`, `params.json` or `validate` scripts across multiple tests. They are supported with Git. Make sure to give your shared files a unique name, since you might have different ones. In the example below the tests are mixing and matching a few shared files, and their own:

```json
tests/
    file-one.jpg
    params-resize.json
    params-crop.json
    validate-image-compare

    jpg-png-resize/
        file.jpg    - symlink: ../file-one.jpg
        params.json - symlink: ../params-resize.json
        rendition.png
        validate    - symlink: ../validate-image-compare

    jpg2-png-crop/
        file.jpg
        params.json - symlink: ../params-crop.json
        rendition.gif
        validate    - symlink: ../validate-image-compare

    jpg-gif-crop/
        file.jpg    - symlink: ../file-one.jpg
        params.json - symlink: ../params-crop.json
        rendition.gif
        validate
```

### Test expected errors {#test-unexpected-errors}

Error tests cases should not contain an expected `rendition.*` file and should define the expected `errorReason` inside the `params.json` file.

>[!NOTE]
>
>If a test case does not contain an expected `rendition.*` file and does not define the expected `errorReason` inside the `params.json` file, it is assumed to be an error case with any `errorReason`.

Error Test Case Structure:

```json
<error_test_case>/
    file.jpg
    params.json
```

Parameter file with error reason:

```javascript
{
    "errorReason": "SourceUnsupported",
    // ... other params
}
```

See a complete list and description of [Asset Compute error reasons](https://github.com/adobe/asset-compute-commons#error-reasons).

## Debug a custom application {#debug-custom-worker}

The following steps show how you can debug your custom application using Visual Studio Code. It allows for seeing live logs, hit breakpoints and step through code as well as live reloading of local code changes upon every activation.

The `aio` out-of-the-box automates many of these steps. Go to the section Debugging the Application in the [Adobe Developer App Builder documentation](https://developer.adobe.com/app-builder/docs/getting_started/first_app). For now, the below steps include a workaround.

1. Install the latest [wskdebug](https://github.com/apache/openwhisk-wskdebug) from GitHub and the optional [ngrok](https://www.npmjs.com/package/ngrok).

   ```shell
   npm install -g @openwhisk/wskdebug
   npm install -g ngrok --unsafe-perm=true
   ```

1. Make additions to your user settings in the JSON file. It keeps using the old Visual Studio Code debugger. The new one has [some issues](https://github.com/apache/openwhisk-wskdebug/issues/74) with wskdebug: `"debug.javascript.usePreview": false`.
1. Close any instances of apps open by way of `aio app run`.
1. Deploy the latest code using `aio app deploy`.
1. Run only the Asset Compute Devtool using `aio asset-compute devtool`. Keep it open.
1. In Visual Studio Code Editor, add the following debug configuration to your `launch.json`:

    ```json
    {
      "type": "node",
      "request": "launch",
      "name": "wskdebug worker",
      "runtimeExecutable": "wskdebug",
      "args": [
        "PROJECT-0.0.1/__secured_worker",           // Replace this with your ACTION NAME
        "${workspaceFolder}/actions/worker/index.js",
        "-l",
        "--ngrok"
      ],
      "localRoot": "${workspaceFolder}",
      "remoteRoot": "/code",
      "outputCapture": "std",
      "timeout": 30000
    }
    ```
  
   Fetch the `ACTION NAME` from the output of `aio app deploy`.

1. Select `wskdebug worker` from the run/debug configuration and press the play icon. Wait for it to start until it displays **[!UICONTROL Ready for activations]** in the **[!UICONTROL Debug Console]** window.

1. Click **[!UICONTROL run]** in the Devtool. You can see the actions running in the Visual Studio Code editor and the logs start displaying.

1. Set a breakpoint in your code. Then run again and it should hit.

Any code changes are loaded in real-time and are effective as soon as the next activation happens.

>[!NOTE]
>
>Two activations are present for each request in custom applications. The first request is a web action that calls itself asynchronously in the SDK code. The second activation is the one that hits your code.

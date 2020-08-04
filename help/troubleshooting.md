---
title: Troubleshoot [!DNL Asset Compute Service].
description: Troubleshoot and debug custom workers using [!DNL Asset Compute Service].
---

# Troubleshoot {#troubleshooting}

## General troubleshooting tips {#general-troubleshooting-tips}

* Ensure that the JavaScript worker does not crash on startup. Such crashes are usually related to a missing library or a dependency.
* Ensure that all dependencies to be installed are referenced in the worker's `package.json` file.
* Ensure any errors that may come from cleanup on failure don't generate their own errors that hide the original problem.

* When starting the developer tool for the first time with a new [!DNL Asset Compute Service] integration, it may fail the first processing request because the Asset Compute Events Journal may not be completely set up. Wait for some time for the journal to set up before sending another request.
* If you run into errors sending Asset Compute `/register` or `/process` requests, make sure that all the necessary APIs are added to the Adobe I/O Project and Workspace&mdash;that is, Asset Compute, IO Events, IO Events Management, and Runtime.

## Log in issues via Adobe I/O CLI {#login-via-aio-cli}

If you have issues logging in to the [!DNL Adobe Developer Console] [through the Adobe I/O CLI](https://github.com/AdobeDocs/project-firefly/blob/master/getting_started/first_app.md#3-signing-in-from-cli), then manually add the credentials required for developing, testing, and deploying your custom worker:

1. Navigate to your Firefly project and workspace on the [Adobe Developer Console](https://console.adobe.io/) and press **[!UICONTROL Download]** from the top-right corner. Open this file and save this JSON to a safe place on your machine.

1. Navigate to the ENV file in your Firefly Application.

1. Add the Adobe I/O Runtime Credentials. Get the Adobe I/O Runtime credentials from the downloaded JSON. The credentials are under `project.workspace.services.runtime`. Add the I/O Runtime credentials in the `AIO_runtime_XXX` variables:

    ```json
    AIO_runtime_auth=
    AIO_runtime_namespace=
    ```

1. Add the absolute path to the downloaded JSON in Step 1:

    ```json
        ASSET_COMPUTE_INTEGRATION_FILE_PATH=
    ```

1. Set up the rest of the [required credentials](develop-custom-worker.md) needed for the developer tool.

<!-- TBD for later:
Add any best practices for developers in this section:
* Any items to take care of when creating projects.
* Any naming conventions, reserved keywords, etc.?
* Any terms that can become a source of confusion later based on our OOTB naming.

* If required, add limitations for custom workers and spin those off as best practices.
* Do NOT borrow any content from https://git.corp.adobe.com/nui/nui/blob/master/doc/worker_api.md. It is outdated and irrelevant for 3rd party custom workers.
-->

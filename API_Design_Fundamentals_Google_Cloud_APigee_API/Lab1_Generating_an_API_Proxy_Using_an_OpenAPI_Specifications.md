# Lab 1: Generating an API Proxy Using an OpenAPI Specification

## Overview

An [OpenAPI specification](https://github.com/OAI/OpenAPI-Specification) uses a standard format to describe a RESTful API. Written in either JSON or YAML format, an OpenAPI specification is machine readable, but is also easy for people to read and understand. The specification describes elements of an API, including its base path, resource paths and verbs, operations, headers, query parameters, and responses. In addition, an OpenAPI specification is commonly used to generate API documentation.

In this lab, you import an OpenAPI specification for the retail backend into your Apigee organization, create an API proxy using this specification, and update the specification to point at your new API proxy.

## Objectives

In this lab, you learn how to perform the following tasks:

- Import and edit an OpenAPI specification.
- Create an API proxy from an OpenAPI specification, using the proxy wizard.
- Trace an API proxy.

## Setup

Read these instructions. Labs are timed and you cannot pause them. The timer, which starts when you click Start Lab, shows how long the lab will be made available to you.

This Qwiklabs hands-on lab lets you do the lab activities yourself in a real Apigee cloud environment, not in a simulation or demo environment. You will be using your own Apigee organization and will get to keep your work.

**Note**: Although this lab does not consume any GCP resources, please remember to click **Start Lab** so you get credit for completing the lab.

### Prerequisites

To complete this lab, you need:

- Access to a standard internet browser. The [Chrome browser](https://www.google.com/chrome) is recommended.
- Your own Apigee organization. You will create this organization in this lab.

## Task 1. Create an Apigee evaluation organization

In this task, you create an Apigee evaluation organization that is used for the course labs.

If you already have your own evaluation organization, you can skip this task. It is not recommended to use a paid or team organization for these labs.

1. In a different browser tab, open the [account signup page](https://login.apigee.com/sign_up).

2. Enter your name, email address, password, and any other required information, and agree to the terms of service and privacy policy. We recommend that you use a personal email address for creating this organization.

3. Click **Create Account**. If an error indicates that there is already a user with your username, your email address is already associated with an Apigee organization. You will need to use a different email address to create your evaluation organization.

4. When you receive an email to activate the account, click on the link to activate.

   You are taken into your Apigee organization.

5. if a different organization is shown, select your organization from the dropdown menu in the upper left. All of the labs should be completed using your new organization.

## Task 2. Import and test the backend OpenAPI specification

In this task, you import an OpenAPI specification into your Apigee organization and test the specification.

### Import the backend OpenAPI specification

1. In a different browser tab, open the [Apigee console](https://apigee.com/edge).

2. On the left navigation menu, click **Develop > Specs**.

3. To create a new spec, on the **+Spec** menu, click **Import URL**.

   You will use a backend OpenAPI specification to build the skeleton of your API proxy. You will edit this specification to document the API implemented by your API proxy.

4. Specify the following:

   | Property    | Value                                                        |
   | :---------- | :----------------------------------------------------------- |
   | Import name | **retail-v1**                                                |
   | Import URL  | **https://storage.googleapis.com/cloud-training/developing-apis/specs/retail-backend.yaml** |

5. Click **Import**.

6. Click on the specification name **retail-v1** in the Specs list.

   When the specification editor opens, the [YAML](https://yaml.org/) OpenAPI specification is on the left, and the live documentation is on the right:

   ![OpenAPI Spec](https://cdn.qwiklabs.com/R5ffgs9lpUP78GsRWuQ90GWBUaVM6rN%2BnIjYDpUGcHI%3D)

   Changes made to the YAML specification on the left are immediately reflected in the documentation on the right.

### Test the backend OpenAPI specification

1. In the live documentation, click on the **GET /categories** box to expand the panel.

2. Click **Try it out**, and then click **Execute**.

   You should see a successful response.

   ![Categories Response](https://cdn.qwiklabs.com/ioR047oa4auG0ljiailftcHr2vC5zQC%2Bv0xX1CPOx9g%3D)

   Notice that the live documentation is calling the backend directly. After you generate the API proxy, you will change the specification to point to the API proxy in your organization instead of to the backend service.

## Task 3. Generate and trace the API proxy

In this task, you use the OpenAPI specification to generate an API proxy, and then explore the created API proxy.

### Generate an API proxy using the OpenAPI specification

1. On the left navigation menu, click **Develop > API Proxies**.

2. To start the [proxy wizard](https://docs.apigee.com/api-platform/fundamentals/build-simple-api-proxy), click **+Proxy**.

3. Click **Use OpenAPI Spec** in the **Reverse proxy** box.

4. Select the **retail-v1** specification, and then click **Select**.

5. Specify the following:

   | Property    | Value             |
   | :---------- | :---------------- |
   | Name        | **retail-v1**     |
   | Base path   | **/retail/v1**    |
   | Description | **My retail API** |

   Leave the Target unchanged.

6. Click **Next**.

7. Leave the Common Policies settings at their defaults. Click **Next**.

8. Operations that were found in the OpenAPI specification are listed. Confirm that all operations are selected, and click **Next**.

9. The secure virtual host should be selected, and the default virtual host should not be selected. Click **Next**.

10. On the Summary page, select the **test** environment, and then click **Create and deploy**.

    Your proxy will be generated and deployed.

11. When deployment is complete, click **Edit proxy**.

### Explore and trace the API proxy

1. Click the **Develop** tab.

   This tab is used to edit the proxy that has been generated. Conditional flows have been created for each of the operations in the OpenAPI specification. These conditional flows appear in the proxy endpoint in the Navigator on the left. When you click a conditional flow, it appears in the Code pane on the right.

   ![Develop Tab](https://cdn.qwiklabs.com/VbCh56C9uo03VHN4DaA7sP4HMJYne9l5ZDjH2gaqCUc%3D)

   You will update these conditional flows in later labs.

2. Click the **Trace** tab.

3. **Deployment to Trace** should show the test environment. Click **Start Trace Session**.

4. To send a "get categories" request to your proxy, add

   ```
   /categories
   content_copy
   ```

   to the URL in the **Send Requests** pane.

   Your URL should look similar to:

   ```
   https://myorg-eval-test.apigee.net/retail/v1/categories
   content_copy
   ```

   Instead of **myorg-eval**, your URL should include your org name, which can be found under your name in the upper left corner of the Apigee console.

5. Click **Send**.

   A transaction for this request should appear in the Transactions pane on the left. When a transaction is selected, you'll see a trace of the request and response through Apigee. You should see a **200 Status code** if your backend URL was correctly set, and you added **/categories** to the end of the Send Requests URL.

6. Click on the last circle as shown in the screenshot below. This shows the response that is being returned to the client.

   ![API Response](https://cdn.qwiklabs.com/hb1PW0fqe1Xd5KOzBE4QjU1qj7GRVJFVMY0YjiBkoE0%3D)

   The Send Requests pane is an easy way to create a GET request for your API proxy. However, you cannot add headers, change the verb in the request, or send a payload. You will need a [REST client](https://storage.googleapis.com/cloud-training/developing-apis/docs/rest-clients.pdf) for later labs.

   The Phase Details pane in your trace may look different from the screenshot, depending on the settings in the View Options pane in the lower left corner. The screenshots typically show the contents with the "Automatically Compare Selected Phase" box cleared. If you want your trace to more closely match screenshots in the labs, clear the "Automatically Compare Selected Phase" checkbox.

## Task 4. Modify the OpenAPI specification to reference your proxy

In this task, you modify the OpenAPI specification to reference your API proxy instead of the backend service.

### Modify the OpenAPI specification

1. Keep the trace session open. In another tab, open the [Apigee console](https://apigee.com/edge).

2. On the left navigation menu, click **Develop > Specs**.

3. Click on the **retail-v1** specification.

4. Change the first 15 lines of the OpenAPI specification to look something like this:

   ```yaml
   openapi: "3.0.0"
   info:
     version: 0.0.1
     title: Retail API v1
     description: Retail API
     contact:
       name: Your Name
       email: youremail@example.org
       url: https://example.org
     license:
       name: MIT
       url: https://opensource.org/licenses/MIT
   servers:
     - url: "https://YOURORG-test.apigee.net/retail/v1"
       description: Retail API v1content_copy
   ```

5. You must change the server URL to use your organization. Replace **YOURORG** with your organization name. Your org name typically ends with **-eval**. The URL should end with **/retail/v1**.

   If you have any problems, you can use the URL in the **Send Requests** pane of the trace tool. That URL, after you remove any resource like **/categories**, should match this URL.

6. You can optionally change the description and contact information.

7. Click **Save** to save the proxy.

### Test the updated OpenAPI specification

1. Click **Start Trace Session** in the trace tab. If trace is already running, and the button is red, click the button to stop the trace, and click again to start the trace session.

2. On the right side of the Spec page, click the **GET /categories** box, and try the call by clicking **Try it out** and **Execute**. You should see the call in the trace tool.

   Click on the first circle in the trace to see the request. You may see headers that were automatically added by the browser, such as Origin, User-Agent, and Referer.

   ![Trace](https://cdn.qwiklabs.com/3GXvsePkwhMKr1gxV1VVm6UafXOgbqKUJzHaDQkWkGk%3D)

   If you receive a "TypeError: Failed to fetch" error when sending from the spec, your organization name might not have been specified correctly in the OpenAPI spec. Confirm the change you made to the first 15 lines of the OpenAPI spec, and confirm that your servers.url includes "/retail/v1" and your organization name, which includes "-eval" in most cases.
# Lab 3: Publishing APIs as Products



## Overview

It is important to identify the caller during an API call.

In this lab, you create an API product and associate it with an API proxy. You will then create an application and associate it with the API product, and use the application's API key when calling the API.

## Objectives

In this lab, you learn how to perform the following tasks:

- Create an API product for an API proxy or group of API proxies.
- Create a developer and an application.
- Associate an application with an API product.
- Use a VerifyAPIKey policy to restrict access to registered applications.

## Setup

Read these instructions. Labs are timed and you cannot pause them. The timer, which starts when you click Start Lab, shows how long the lab will be made available to you.

This Qwiklabs hands-on lab lets you do the lab activities yourself in a real Apigee cloud environment, not in a simulation or demo environment. You will be using your own Apigee organization and will get to keep your work.

**Note**: Although this lab does not consume any GCP resources, please remember to click **Start Lab** so you get credit for completing the lab.

### Prerequisites

To complete this lab, you need:

- Access to a standard internet browser. The [Chrome browser](https://www.google.com/chrome) is recommended.
- Your own Apigee organization. See these [instructions](https://storage.googleapis.com/cloud-training/developing-apis/solutions/instructions/apigeeorg.pdf) to create an Apigee organization.
- A REST client. See [this document](https://storage.googleapis.com/cloud-training/developing-apis/docs/rest-clients.pdf) for information about and links to some popular REST clients.

## Task 0 (if necessary). Import solution to previous lab

Important: This lab is one of a series of labs that build upon each other. Complete this task if you do not have a working retail-v1 proxy completed during the previous lab.

Prerequisites:

- Apigee organization: [instructions](https://storage.googleapis.com/cloud-training/developing-apis/solutions/instructions/apigeeorg.pdf)
- OpenAPI spec, API proxy: [instructions](https://storage.googleapis.com/cloud-training/developing-apis/solutions/instructions/openapi-apiproxy.pdf)
- Target server: [instructions](https://storage.googleapis.com/cloud-training/developing-apis/solutions/instructions/targetserver.pdf)

1. After confirming the prerequisites, download a [working solution to the previous lab](https://storage.googleapis.com/cloud-training/developing-apis/solutions/retail-v1-lab02.zip) as a proxy bundle ZIP file.

1. Open the [Apigee console](https://apigee.com/edge).

2. **If you have a retail-v1 proxy already**, navigate to the proxy page (**Develop** > **API Proxies**) and select your proxy. Click on the **Project** menu, and then select **Upload a New Revision**.

   Click **Choose File** and select the bundle zip file you just downloaded. Click **Upload**.

   The proxy bundle will be imported into a new revision.

3. **If you did not have a retail-v1 proxy already**, navigate to the proxy page (**Develop** > **API Proxies**), and click **+Proxy**.

   Click **Upload proxy bundle**, select your bundle zip file you just downloaded, change the name to **retail-v1**, and click **Next**.

   Click **Create**, and then click **Edit Proxy**.

4. Click the **Develop** tab.

5. Click the **Revision** menu, and select the revision with the highest revision number.

6. Select the **Deployment** menu, and check the deployment status. If this revision does not show a green circle next to the test environment, click on **test** to deploy the proxy.

   The **test** environment will be green when it is deployed.

## Task 1. Add a VerifyAPIKey policy to your API proxy

In this task, you add a [VerifyAPIKey policy](https://docs.apigee.com/api-platform/reference/policies/verify-api-key-policy) to force callers to present an API key when accessing the API.

### Save as new revision

1. Navigate to the **retail-v1** API proxy.

2. Click the **Project** menu, and then select **Save as new revision**.

   The previous revision will be retained in case you need to roll back your changes.

3. To deploy the new revision to the test environment, on the **Deployment** menu, click **test**. In the message box, click **Deploy**.

   The test environment in the Deployment menu will be green when the selected revision is deployed.

### Add the VerifyAPIKey policy

1. On the **Develop** tab, select the **PreFlow** in the default proxy endpoint, and then click **+Step** to add a policy in the request preflow.

   The VerifyAPIKey policy should be one of the first policies executed, because you want to avoid most processing if the caller is not allowed to use the API proxy. Ensure that you have selected the request preflow in the default *proxy* endpoint, not the target endpoint.

   ![proxy request preflow](https://cdn.qwiklabs.com/UZ2fOc88IopWWsTgcth8xiJc7RlgatwK8S7TmxMP5lM%3D)

2. Select the **Verify API Key** policy type, and change the display name to **VAK-VerifyKey**. This should also change the name field. Click **Add** to create the policy.

   It is important to get the policy name correct. The VerifyAPIKey policy will populate variables that include the policy name, and you will use those variables in later labs.

   You will use a naming convention for policies in these labs. The prefix, VAK, indicates that the policy type is VerifyAPIKey. The rest of the policy name, VerifyKey, indicates what the policy is intended to do. This naming convention is used because the step elements in the endpoint flows don't show the type of policy being attached.

3. Click on the **VAK-VerifyKey** policy and look at the **APIKey** element. The policy currently expects the API key in a query parameter named **apikey**.

4. Change the policy configuration so it expects the API key in a header named **apikey**. Set the **APIKey** configuration like this:

   ```
       <APIKey ref="request.header.apikey"/>
   content_copy
   ```

   If the API key is specified in a query parameter, it will probably appear in access logs. It is a better practice to expect the API key in a field that is less likely to be logged, such as a header.

5. Click **Save** to save and deploy your proxy.

### Use trace to verify the change

1. Click the **Trace** tab.

2. Start the trace session and invoke the API using the trace tool.

   The transaction should return 401. Click on the VerifyAPIKey policy in the transaction map. You should see that the request.header.apikey variable was read.

   ![header variable](https://cdn.qwiklabs.com/%2FU4fdvzX5rN5Myn9KEqksRFW4M%2FKW1TJCeVJCBpV%2Bfo%3D)

   The error message is "Failed to resolve API Key variable request.header.apikey". The API key was not found.

3. Invoke the request using a REST client.

   Add the **apikey** header, but use a bad value. This response will indicate that the API key is invalid:

   ![invalid key](https://cdn.qwiklabs.com/3eKacUmGV4FwNz8m8OKzKus8tvgxjvhSPQwLhYf3DXM%3D)

   This error means you are passing the API key in the correct location.

## Task 2. Create an API product for your proxy

In this task, you create an API product associated with your API proxy.

1. On the left navigation menu, click **Publish > API Products**.

2. To create a new API product, click **+API Product**.

3. In the **Product details** pane, specify the following:

   | Property     | Value               |
   | :----------- | :------------------ |
   | Name         | **Retail**          |
   | Display Name | **Retail**          |
   | Environment  | *select* **test**   |
   | Access       | *select* **Public** |

   Leave **Automatically approve access requests** selected.

4. In the **API Resources** pane, click the **Add a proxy** link.

5. In the message box, select **retail-v1** and click **Add(1)**.

6. Click **Save** in the upper right corner to save the API product.

7. Return to the **API Products** page. Your **Retail** API product will be listed.

## Task 3. Create an app developer

In this task, you create an app developer, which will allow you to register an app.

App developers are typically created using a developer portal, and you will be creating your developer portal in a later lab. For now you will use the Apigee console to create the developer.

1. On the left navigation menu, click **Publish > Developers**.
2. To create a new app developer, click **+Developer**.
3. Enter the first name, last name, username, and email. You can choose any values. Click **Create** to create the app developer.

## Task 4. Create an app

In this task, you create an app for your app developer.

1. On the left navigation menu, click **Publish > Apps**.

2. To create a new app, click **+App**.

3. In the **App details** pane, specify the following:

   | Property     | Value                                   |
   | :----------- | :-------------------------------------- |
   | Name         | **My Retail App**                       |
   | Display Name | **My Retail App**                       |
   | App type     | **Developer**                           |
   | Developer    | *select the developer you just created* |

4. In the **Credentials** pane, click **Add product**, click on **Retail**, and then click **Add(1)** to add. Status should already be marked as approved.

5. Click **Create** in the upper right corner to create the app.

6. The **Key** and **Secret** are now configured for the app. Click on **Show** next to **Key**.

## Task 5. Test the API using the app's API key

In this task, you use the app's key to gain access to the API.

1. In your REST client, create a header with the name **apikey**.

2. Copy the value of the key for your app, and paste it into the header value in the REST client. Ensure that you have no leading or trailing spaces in your header value.

3. Return to the trace tool for the **retail-v1** proxy.

4. Start the trace session, and submit a request with the correct API key in the REST client. Add **/categories** to the end of the URL to retrieve categories.

   In the trace tool click on the **VerifyAPIKey** policy. In the Phase Details you can see that the VerifyAPIKey policy set many variables when the API key was successfully verified.


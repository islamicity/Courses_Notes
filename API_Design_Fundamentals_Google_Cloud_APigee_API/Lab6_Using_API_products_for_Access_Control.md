# Lab 6(Lab 3a): Using API Products for Access Control



## Overview

In this lab, you use API product custom attributes to control functionality of your API proxy.

## Objectives

In this lab, you learn how to perform the following tasks:

- Configure custom attributes for an API product.
- Create logic in the proxy that uses the VerifyAPIKey variables automatically populated with the API product custom attribute values.

## Setup

Read these instructions. Labs are timed and you cannot pause them. The timer, which starts when you click Start Lab, shows how long the lab will be made available to you.

This Qwiklabs hands-on lab lets you do the lab activities yourself in a real Apigee cloud environment, not in a simulation or demo environment. You will be using your own Apigee organization and will get to keep your work.

**Note**: Although this lab does not consume any GCP resources, please remember to click **Start Lab** so you get credit for completing the lab.

### Prerequisites

To complete this lab, you need:

- Access to a standard internet browser. The [Chrome browser](https://www.google.com/chrome) is recommended.
- Your own Apigee organization. If you do not have an Apigee organization, you may create one using these [instructions](https://storage.googleapis.com/cloud-training/developing-apis/solutions/instructions/apigeeorg.pdf).
- A REST client. See [this document](https://storage.googleapis.com/cloud-training/developing-apis/docs/rest-clients.pdf) for information about and links to some popular REST clients.

## Task 1. Create a new proxy

In this task, you create a new API proxy.

1. Open the [Apigee console](https://apigee.com/edge) in another browser tab.

2. On the left navigation menu, click **Develop > API Proxies**.

3. To start the proxy wizard, click **+Proxy**.

4. Click **No target**.

5. Specify the following:

   | Property  | Value         |
   | :-------- | :------------ |
   | Name      | **lab3a-v1**  |
   | Base path | **/lab3a/v1** |

6. Click **Next**.

7. Leave the Policies defaults, and click **Next**.

8. Ensure that only the **secure** virtual host is selected, and click **Next**.

9. Select **test** to deploy to the test environment, and then click **Create and deploy**.

## Task 2. Create an API product for the proxy

In this task, you create an API product associated with the API proxy.

1. On the left navigation menu, click **Publish > API Products**.

2. To create a new API product, click **+API Product**.

3. In the **Product details** pane, specify the following:

   | Property     | Value      |
   | :----------- | :--------- |
   | Name         | **lab-3a** |
   | Display Name | **lab-3a** |
   | Environment  | **test**   |
   | Access       | **Public** |

   Leave **Automatically approve access requests** selected.

4. In the **API Resources** pane, click the **Add a proxy** link.

5. In the message box, select **lab3a-v1**, and then click **Add(1)**.

6. Click **Save** in the upper right corner to save the API product.

## Task 3. Add a custom attribute to the API product

In this task, you add a custom attribute to the API product.

1. Click **Edit** to edit the API product you just created.

2. In the **Custom attributes** pane, click the **Add a custom attribute** link.

3. In the message box, specify the following:

   | Property | Value        |
   | :------- | :----------- |
   | Name     | **readOnly** |
   | Value    | **yes**      |

4. Click **Add**.

5. Click **Save** in the upper right corner to save the updated API product.

## Task 4. Create an app

In this task, you create an app that is associated with the API product.

1. On the left navigation menu, click **Publish > Apps**.

2. To create a new app, click **+App**.

3. In the **App details** pane, specify the following:

   | Property     | Value                                         |
   | :----------- | :-------------------------------------------- |
   | Name         | **Lab3a**                                     |
   | Display Name | **Lab3a**                                     |
   | App type     | **Developer**                                 |
   | Developer    | **helloworld dev** *(or any other developer)* |

4. In the **Credentials** pane, click **Add product**, click on **lab-3a**, and then click **Add(1)** to add. Status should already be marked as approved.

5. Click **Create** in the upper right corner to create the app.

   The **Key** and **Secret** are now configured for the app.

6. Click **Show** next to **Key**, and note the API key. You will use the key when calling the proxy.

## Task 5. Add VerifyAPIKey policy to the API proxy

In this task, you add a [VerifyAPIKey policy](https://docs.apigee.com/api-platform/reference/policies/verify-api-key-policy/) to force callers to present an API key when accessing the API.

### Add the VerifyAPIKey policy

1. Return to the **lab-3a** proxy, and select the **Develop** tab.

2. Select the **PreFlow** in the default proxy endpoint, and then click **+Step** to add a policy in the request preflow.

   Ensure that you have selected the request preflow in the *default* proxy endpoint, not the target endpoint.

3. Select the **Verify API Key** policy type, and change the display name to **VAK-VerifyKey**. This should also change the name field. Click **Add** to create the policy.

   It is important to get the policy name correct. The VerifyAPIKey policy will populate variables that include the policy name.

   Note that the policy configuration expects the API key in a query parameter named **apikey**.

4. Click **Save** to save and deploy the proxy.

### Test the proxy

1. Click the **Trace** tab.

2. Start the trace session. To send a request, click **Send**.

   An error is returned because there is no valid API key.

3. Add **?apikey=123** to the URL, and then click **Send**.

   An error is returned because the API key is invalid.

4. Replace **123** with the key from your app, and then click **Send**.

   The VerifyAPIKey policy is successful, and the proxy returns **200**.

## Task 6. Add read-only access to the API proxy

In this task, you modify the proxy to check for a custom attribute in the API product associated with the app and block requests accordingly.

### Add raise fault policy

1. Click on the **Develop** tab and, to add a new policy immediately following the **VerifyAPIKey** policy, click on **+Step** within the request flow of the proxy preflow.

2. Select the **Raise Fault** policy, and name it **RF-403Forbidden**.

   Replace the policy configuration with:

   ```
   <RaiseFault continueOnError="false" enabled="true" name="RF-403Forbidden">
       <FaultResponse>
           <Set>
               <StatusCode>403</StatusCode>
               <ReasonPhrase>Forbidden</ReasonPhrase>
           </Set>
       </FaultResponse>
       <IgnoreUnresolvedVariables>true</IgnoreUnresolvedVariables>
   </RaiseFault>
   content_copy
   ```

   ![RaiseFault policy](https://cdn.qwiklabs.com/LA8bC4ur3dr9xyvR5mXHWlVKAHi8iZIx3acEsw8tnwo%3D)

### Modify policy condition

1. In the **Navigator** on the left side, in the **Proxy Endpoints** box, click **default**.

   The ProxyEndpoint configuration is displayed in the Code box.

2. Within the **Step** element for the **RF-403Forbidden** RaiseFault policy, add the following condition:

   ```
   <Condition>verifyapikey.VAK-VerifyKey.apiproduct.readOnly == "yes" AND request.verb != "GET"</Condition>
   content_copy
   ```

   ![RaiseFault condition](https://cdn.qwiklabs.com/%2BU6zcU2wT1hksfwsmBOuV1LMWN%2FS1JM3GvRTJm6l3tk%3D)

   The step should now look like:

   ```
   <Step>
       <Condition>verifyapikey.VAK-VerifyKey.apiproduct.readOnly == "yes" AND request.verb != "GET"</Condition>
       <Name>RF-403Forbidden</Name>
   </Step>
   content_copy
   ```

3. Click **Save** to save and deploy the proxy.

### Test the proxy

1. Click the **Trace** tab.

2. Start the trace session.

3. Modify the URL by adding your API key in an **apikey** query parameter.

4. Click **Send**.

   The **GET** request is successful.

   ![GET success](https://cdn.qwiklabs.com/AVfr2pGkQQqzedOfrvhn0lLZ0gQX%2B94vvfz1t5EG%2BDw%3D)

5. Make a **PUT** request to the same URL. Invoke the request using a REST client.

   The **PUT** request is blocked, because the API product has a custom attribute named **readOnly** with the value **yes**.

   ![PUT forbidden](https://cdn.qwiklabs.com/AWA8A3YyyAISUFSTOeshY7ByYpAkueYuZNDT%2BR7FLTs%3D)

## Task 7 (optional). Add a new API product with the readOnly attribute set to no

In this task, you add a new API product for the same proxy with the readOnly attribute set to no, create a new app that includes that product, and confirm that the app can make non-GET calls.


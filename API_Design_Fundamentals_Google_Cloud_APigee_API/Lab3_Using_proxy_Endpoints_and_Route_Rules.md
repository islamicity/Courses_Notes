# Lab 2a: Using Proxy Endpoints and Route Rules



## Overview

In this lab, you use a second proxy endpoint to receive insecure HTTP requests. You then use a route rule to block insecure requests so that they can't be proxied to the target.

## Objectives

In this lab, you learn how to perform the following tasks:

- Create multiple proxy endpoints within a single proxy.
- Modify route rules to block requests to the target.

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

### Create a new proxy

1. Open the [Apigee console](https://apigee.com/edge) in another browser tab.

2. On the left navigation menu, click **Develop > API Proxies**.

3. To start the proxy wizard, click **+Proxy**.

4. Click **Reverse proxy**.

5. Specify the following:

   | Property              | Value                            |
   | :-------------------- | :------------------------------- |
   | Name                  | **lab2a-v1**                     |
   | Base path             | **/lab2a/v1**                    |
   | Target (Existing API) | **https://httpbin.org/anything** |

6. Click **Next**.

7. Accept the common policy defaults, and click **Next**.

8. Ensure that only the **secure** virtual host is selected. You will add the **default** virtual host with a new proxy endpoint. Click **Next**.

9. Select **test** to deploy to the test environment, and then click **Create and deploy**.

10. Click **Edit proxy**, and then click the **Develop** tab.

11. Confirm that your proxy endpoint only includes the secure virtual host in the **HTTPProxyConnection** configuration.

    ![correct virtual host for default proxy endpoint](https://cdn.qwiklabs.com/jzDIJfoBD5kkS1mHNjrIuIJNFkUjwbu2s0EE7ZMdv9g%3D)

    If the default virtual host is configured here, remove that line to match the image above.

## Task 2. Add a new proxy endpoint

In this task, you add a new proxy endpoint to the proxy. This proxy endpoint will be called when the proxy receives insecure HTTP traffic.

### Add the proxy endpoint

1. In the **Navigator** on the left side, find the **Proxy Endpoints** box. Click the **+** button to the right to add a new proxy endpoint.

   ![Revisions](https://cdn.qwiklabs.com/avmysmucG6vQwt6zOk1zXZG4y1SQHtsrPUZE4gQDjWU%3D)

2. Set the proxy endpoint name to:

   ```
   PlainHTTP
   content_copy
   ```

3. Click **Add**.

   The new PlainHTTP proxy endpoint appears in the Code pane.

### Update the BasePath and RouteRule

1. The PlainHTTP proxy endpoint will handle all API requests that are sent using insecure HTTP. Change the HTTPProxyConnection in the PlainHTTP proxy endpoint.

   Replace

   ```xml
       <HTTPProxyConnection>
           <BasePath/>
           <Properties/>
           <VirtualHost>default</VirtualHost>
       </HTTPProxyConnection>content_copy
   ```

   with

   ```xml
       <HTTPProxyConnection>
           <BasePath>/lab2a/v1</BasePath>
           <VirtualHost>default</VirtualHost>
       </HTTPProxyConnection>content_copy
   ```

   The BasePath is the same as is used in the default proxy endpoint.

2. Requests should not be proxied to the backend for non-encrypted traffic. Change the RouteRule in the PlainHTTP proxy endpoint.

   Replace

   ```xml
       <RouteRule name="default">
           <TargetEndpoint>default</TargetEndpoint>
       </RouteRule>content_copy
   ```

   with

   ```xml
       <RouteRule name="noTarget"/>content_copy
   ```

   This route rule has no target endpoint specified, so no insecure traffic will be sent to the target.

   The PlainHTTP proxy endpoint should look like this after both changes:

   ![PlainHTTP proxy endpoint](https://cdn.qwiklabs.com/wnuoTgRZmsw%2BZlYaxgahX%2FbYOnqtR1zMHzFVbY8lFdU%3D)

### Add an error message

1. To add an [AssignMessage policy](https://docs.apigee.com/api-platform/reference/policies/assign-message-policy) to the request preflow in the PlainHTTP proxy endpoint, click **+Step** in the request (upper) part of the preflow.

   ![add step](https://cdn.qwiklabs.com/qL%2B5X73UGo5ejw1o%2FcMuYvsAa5jjHiif628UNLpw488%3D)

2. Choose an **Assign Message** policy.

3. Specify the following:

   | Property     | Value                    |
   | :----------- | :----------------------- |
   | Display Name | **AM-400BadRequestHTTP** |
   | Name         | **AM-400BadRequestHTTP** |

4. Click **Add**.

5. Replace the AssignMessage code with this:

   ```xml
   <AssignMessage async="false" continueOnError="false" enabled="true" name="AM-400BadRequestHTTP">
       <Set>
               <StatusCode>400</StatusCode>
               <ReasonPhrase>Bad Request</ReasonPhrase>
               <Payload contentType="application/json">{
       "message":"HTTP is not supported. Please change your request to HTTPS and try again.",
       "url":"https://{request.header.host}{proxy.basepath}{proxy.pathsuffix}?{request.querystring}"
   }</Payload>
       </Set>
       <IgnoreUnresolvedVariables>true</IgnoreUnresolvedVariables>
       <AssignTo createNew="true" transport="http" type="response"/>
   </AssignMessage>content_copy
   ```

   This AssignMessage policy creates a **400 Bad Request** error response when an insecure HTTP request is received.

6. To save your proxy, click **Save**.

## Task 3. Test your changes

In this task, you test the proxy using the trace tool. Sending requests using **http://** instead of **https://** should result in the error message.

1. Click on the **Trace** tab, and then click **Start Trace Session**.

2. Change the URL to start with **https://**, and then click **Send**.

   The response from [httpbin](https://httpbin.org/) should be **200 OK**.

3. Change the URL to start with **http://**, and then click **Send**.

   The response should be **400 Bad Request**, and the target is not called, because there is no factory icon shown in the trace.

   ![no target called](https://cdn.qwiklabs.com/Qxn2OLG0N%2BVKpmPcX%2BaNvTP3FzIN7nQ1jLkk9iFbIWE%3D)

   If the response for an http request is not 400, confirm that you have configured your proxy endpoints correctly.
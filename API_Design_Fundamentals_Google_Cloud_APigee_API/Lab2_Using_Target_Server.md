# Lab 2: Using Target Servers



## Overview

When you build proxies, it is important to not hardcode configuration that is specific to an environment.

In this lab, you will use an environment-specific target server to specify the backend target URL.

## Objectives

In this lab, you learn how to perform the following tasks:

- Create a named target server in an environment.
- Modify a proxy to call a named target server instead of a hardcoded URL.

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

1. After confirming the prerequisites, download a [working solution to the previous lab](https://storage.googleapis.com/cloud-training/developing-apis/solutions/retail-v1-lab01.zip) as a proxy bundle ZIP file.

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

## Task 1. Create a new target server for the test environment

In this task, you create a new target server configuration for the test environment.

### Create a new target server

1. On the left navigation menu, click **Admin > Environments > Target Servers**.

2. If it is not already selected, use the dropdown in the upper left to select the **test** environment.

3. Click **+Target server**.

4. Specify the following:

   | Property | Value                                  |
   | :------- | :------------------------------------- |
   | Enabled  | *selected*                             |
   | Name     | **TS-Retail**                          |
   | Host     | **gcp-cs-training-01-test.apigee.net** |
   | Port     | **443**                                |
   | SSL      | *selected*                             |

   When the **SSL** box is selected, the dialog shows additional configuration fields. Accept their default values, and click **Add**.

5. Confirm that your values match the values in the image.

   ![target server configuration](https://cdn.qwiklabs.com/%2FWEdNE%2BkTgdeUksoqjtZs%2B1DvQLLBgorybSEjIBWwzA%3D)

## Task 2. Update the API proxy to use the target server

In this task, you modify your retail API proxy to use the target server instead of the hardcoded target URL.

### Update the API proxy

1. On the left navigation menu, click **Develop > API Proxies**.

2. Select the **retail-v1** proxy, and then click the **Develop** tab.

3. In the **Navigator** on the left side, in the **Target Endpoints** box, click **default**. The TargetEndpoint configuration is displayed in the Code box.

4. In the **HTTPTargetConnection** section, around line 13, is the hardcoded URL.

   Replace the hardcoded URL with a reference to the target server you created.

   Replace

   ```xml
       <HTTPTargetConnection>
           <URL>https://gcp-cs-training-01-test.apigee.net/training/db</URL>
       </HTTPTargetConnection>content_copy
   ```

   with

   ```xml
       <HTTPTargetConnection>
           <LoadBalancer>
               <Server name="TS-Retail"/>
           </LoadBalancer>
           <Path>/training/db</Path>
       </HTTPTargetConnection>content_copy
   ```

5. To save these proxy changes as a new revision, on the **Project** menu, click **Save as New Revision**.

   It is a good practice to save changes as a new revision. However, the new revision needs to be manually deployed.

   Click the **Revision** menu to see which revision is deployed.

   ![deployed revision](https://cdn.qwiklabs.com/iaoKdl6Uuc4%2BUwUqS4RedsEdmV3MH49M0jXPbYes1Bk%3D)

   In this example, revision 1 is deployed, and revision 2 is the latest revision.

6. If the latest revision is not deployed, click the **Revision** menu, and then click the latest revision. The new revision will be displayed.

7. Click the **Deployment** menu, and then click **test** to select the test environment. Click **Deploy** in the message box. A message will soon indicate that the proxy has been deployed to the test environment.

   ![deploy message](https://cdn.qwiklabs.com/AtklVodwhp%2B%2FNCMQntzAvhSezPWwIm7qNlD%2Bd8vmSl8%3D)

8. Click the **Deployment** menu. The menu should show that this revision of the proxy is deployed to the **test** environment.

## Task 3. Test the changed API proxy

In this task, you use the trace tool to test that the updated proxy still successfully calls the backend service.

1. Click the **Trace** tab.

2. Click **Start Trace Session**.

3. After the trace session has started, add

   ```
   /categories
   content_copy
   ```

   to the end of the Send Requests URL, and click **Send**.

   A **200** status code response should be received, with the same categories response payload as for the last lab.

   Remember that the Send Requests box is just a convenient way to send a GET request to your API proxy. Any traffic to your API proxy will show up in trace.

## Task 4 (optional). Repeat this process for the prod environment

In this task, you add a target server for the prod environment and test your proxy in the prod environment.

1. Configure a new target server as you did in Task 1. Select the **prod** environment, and use the following:

   | Property | Value                                  |
   | :------- | :------------------------------------- |
   | Enabled  | *selected*                             |
   | Name     | **TS-Retail**                          |
   | Host     | **gcp-cs-training-01-prod.apigee.net** |
   | Port     | **443**                                |
   | SSL      | *selected*                             |

   Note that the host is different for the prod environment.

2. Deploy the **retail-v1** proxy to prod, as you did for test in Task 2.

3. Trace the proxy in the prod environment.

   Ensure that the **prod** environment is specified in the **Deployment to Trace** dropdown, and that the Send Requests URL is using the prod environment.

   You should see that the target server request is going to **gcp-cs-training-01-prod.apigee.net**.

4. After testing the proxy, return to the **Develop** tab and use the **Deployment** menu to undeploy the proxy from the prod environment. You will use only the test environment for the rest of the labs.
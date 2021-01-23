# Lab 4 (Lab 2b): Using the Trace Tool



## Overview

In this lab, you learn how to troubleshoot your proxies using the Apigee trace tool.

## Objectives

In this lab, you learn how to perform the following tasks:

- Invoke requests from the trace tool.
- Trace API calls flowing through a proxy.
- Understand the information available in a trace.
- Set filters to only capture matching calls.
- Save trace sessions for future use.

## Setup

Read these instructions. Labs are timed and you cannot pause them. The timer, which starts when you click Start Lab, shows how long the lab will be made available to you.

This Qwiklabs hands-on lab lets you do the lab activities yourself in a real Apigee cloud environment, not in a simulation or demo environment. You will be using your own Apigee organization and will get to keep your work.

**Note**: Although this lab does not consume any GCP resources, please remember to click **Start Lab** so you get credit for completing the lab.

### Prerequisites

To complete this lab, you need:

- Access to a standard internet browser. The [Chrome browser](https://www.google.com/chrome) is recommended.
- Your own Apigee organization. If you do not have an Apigee organization, you may create one using these [instructions](https://storage.googleapis.com/cloud-training/developing-apis/solutions/instructions/apigeeorg.pdf).
- A REST client. See [this document](https://storage.googleapis.com/cloud-training/developing-apis/docs/rest-clients.pdf) for information about and links to some popular REST clients.

- The default **helloworld** proxy. You can download it from [here](https://storage.googleapis.com/cloud-training/developing-apis/proxies/helloworld.zip) and deploy it to the test environment if you don't already have it in your org.

## Task 1. Understand the features of the trace tool

In this task, you learn about the features of the [Apigee trace tool](https://docs.apigee.com/api-platform/debug/using-trace-tool-0).

### An overview of the trace tool

1. Open the [Apigee console](https://apigee.com/edge) in another browser tab.

2. On the left navigation menu, click **Develop > API Proxies**.

3. Select the sample proxy named **helloworld**.

4. To confirm that it is deployed, click the **Development** dropdown. If you do not see a green circle next to **test**, select **test**, and then click **Deploy** in the message box.

5. Click on the **Trace** tab, which opens the Trace view.

6. To start a trace session, click **Start Trace Session**.

7. To send an API request, click **Send** in the Send Requests pane.

   After a moment, the **Transaction Map** pane should show a request.

   Here is an overview of the trace tool tab:

   ![trace overview](https://cdn.qwiklabs.com/Bum5sYSqMiuOEcfsIzwitiF%2B4K3E084lsfZfRaFJ%2FZ4%3D)

   1. The **Deployment to Trace** dropdown specifies the environment to trace.

      ![deployment to trace](https://cdn.qwiklabs.com/fM5xc6wBXbkfsUntMDMGJvOfE29ACYQZgPiM%2BsOzaxg%3D)

   2. The green **Start Trace Session** button at the top starts a trace session. When the trace session is running, the button is red and says **Stop Trace Session**. A trace session will last a maximum of 10 minutes or until 20 transactions are received, whichever comes first.

      ![stop trace session](https://cdn.qwiklabs.com/HQWkGfffGHu9jesZTDcWtrtnTL66ao%2BtN7AqidQwQs4%3D)

   3. Use the **Send Requests** pane to send GET requests. Note that any requests, not just those sent with the Send Requests pane, are captured by the trace tool.

      ![send requests](https://cdn.qwiklabs.com/Izw4NNCyP9MQnchcH%2B5ZwGSC13G2AUoJpwh%2BiZRHyiI%3D)

   4. The **Transactions** pane on the left contains the list of transactions captured. Initially, there are no transactions. You can select a transaction in this pane. Transactions show the returned status code, method, URI of the request, and the elapsed time of the request.

      ![transactions](https://cdn.qwiklabs.com/G%2BRQBZkFnv2LOquN0y3YuoBSbZjVH6Hgkqvx1Ki9L8M%3D)

   5. The **Transaction Map** pane shows a graphical view of all policies and flows evaluated during the execution of the selected transaction. It also shows the timing of the currently selected processing step.

      ![transaction map](https://cdn.qwiklabs.com/jae1uXFn12D7%2B8wNBEUfpSu%2BwcKA9HAYwJ4YtDX8gFo%3D)

   6. The **Phase Details** box shows information about the state of your proxy during the selected processing step.

      ![phase details](https://cdn.qwiklabs.com/C8ztN1UgjCBToA%2BOB%2F2zeQIeZuOLt2pUZUjAl1Lga50%3D)

   7. The **Filters** button on the far left opens the Filters view, which can be used to filter which API calls are captured. You can specify required headers or query parameters with their values. Only API calls with those specified values will be traced.

      ![filters](https://cdn.qwiklabs.com/%2BxQfq3R%2FTM0wrZJBPtniYc7d8sCcVMO5A0LxoLnAfDI%3D)

   8. The **Output from all Transactions** button at the bottom opens a pane that shows any console messages. There should be no messages for this proxy.

   9. The **View Options** pane controls what is shown in the Transaction Map and Phase Details panes.

      ![view options](https://cdn.qwiklabs.com/PDGuP15%2F6ZiC87alr3d7GgFMTso7PwO0h0GG1r8BxGY%3D)

### Transaction Map

The Transaction Map shows the policies, conditions, and flows processed during a specific transaction.

You can select individual items in the view.

![transaction map details](https://cdn.qwiklabs.com/ReIYDjSvNRs9yEWdgvPUTHWHL8u1TsXyXS4bqwIV5Yg%3D)

- Policies are displayed as icons. You can select a policy to see the variables used and modified by the policy. A badge on the policy icon indicates whether the policy was skipped (![policy skipped](https://cdn.qwiklabs.com/r0OG0zq%2F0rF9gWXyZw0VOTLrA3B1gkgmINcDAj62FgQ%3D)), raised a fault (![policy raised fault](https://cdn.qwiklabs.com/a4LXYSsJgvt48d7Tay3pKPNcMEQ72MASx%2BA47e2XsF0%3D)), or is disabled (![policy disabled](https://cdn.qwiklabs.com/oVKQQ2cN9Bn0J0yvBQcolI3HMZxoQuC%2F9xGzwEUnnes%3D)).

- Flows are represented by vertical bars.

- Conditions are represented by diamonds with an indication of whether the condition evaluated to true (**T**) or false (**F**).

- To inspect the message, click on the circles in the transaction map.

  These four circles show the following:

  1. Request as received from the client
  2. Request as sent to the target
  3. Response as received from the target
  4. Response as sent to the client

- The elapsed time for a selected item is displayed in the transaction map time line.

### Phase Details

The details for the selected policy, flow, or condition are shown in the Phase Details pane.

The variables that are read and assigned are shown, as are properties of the policy.

## Task 2. Inspect variables

In this task, you learn how to inspect variables set and used by policies.

1. Stop and restart the trace session.

2. To make several requests in quick succession, rapidly click **Send**. Continue until you get a 429 status code returned. If you do not get a 429 status code in the first 20 requests, restart the trace session and try again.

3. Click a transaction with a 429 status code. The **Quota policy** will have a red exclamation mark indicating that a fault was raised.

4. Hover over the quota policy. The tooltip shows information about the execution of the policy.

   ![quota policy](https://cdn.qwiklabs.com/JT8itzyTSuqjnF7OsMA%2B7ZDjAU8df7x186MxPBXfBaU%3D)

5. Click on the quota policy. Explore the **Phase Details** pane to see the properties shown for the policy. For example, the **ratelimit.check-quota.available.count** property is 0. This request exceeded the quota.

   ![quota details](https://cdn.qwiklabs.com/vvwbFY6EvpXjwCm8GZ6irtoU7nx%2Fnfe8NfBwSt9uBzQ%3D)

6. Compare the quota policy icon and details with the icon and details for a request that returned 200.

## Task 3. Capturing requests sent from outside the trace tool

In this task, you trace requests not sent via the trace tool.

1. Stop and restart your trace session.
2. Copy the URL from the Send Requests pane, and open the URL in a new browser tab.
3. Switch back to the trace tool browser tab. You should see a captured GET request. The request came from a browser, so you will see the **User-Agent** header in the request. The User-Agent header will be different depending on the browser you use.

## Task 4. Filtering specific requests

In this task, you filter specific requests in the trace tool.

1. To open the Filters pane, click the **Filters** bar on the far left.

2. In the Filters pane, add an entry for a new query parameter:

   | Property | Value    |
   | :------- | :------- |
   | Name     | **test** |
   | Value    | **123**  |

3. Stop and restart your trace session.

4. Click on **Send** several times. No transactions should be saved, because the test query parameter with the correct value has not been supplied.

5. Append **?test=123** to the end of the URL. The end of the URL should now be **/v0/hello?test=123**. Now click on **Send** several times.

   These requests have the required query parameter name and value, so the transactions are captured.

## Task 5. Save trace sessions for future use

In this task, you download a trace session. The session can be opened in any org.

1. When there are transactions in the transaction map, click **Download Trace Session** to download an XML file containing the trace session details for those transactions.
2. To open the [offline trace tool](https://docs.apigee.com/api-platform/debug/using-offline-trace-tool), click **Offline Trace** in the Develop section of the left Navigation bar.
3. Click **Choose File**, select the trace file which should start with **trace-**, and open the file.

You can explore the transactions in the session and see the same information that was available in the live trace session.
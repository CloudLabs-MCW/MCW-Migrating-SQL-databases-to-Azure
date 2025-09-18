# Exercise 4: Integrate App Service with the virtual network

### Estimated Duration: 30 minutes

In this lab, you will configure VNet integration with Azure App Services and open the web application. This setup allows your web app to securely communicate with resources in your virtual network. By the end of this lab, your web application will be integrated with the VNet and accessible for use.

## Lab objectives

In this lab, you will complete the following tasks:

- Task 1: Configure VNet integration with App Services
- Task 2: Open the web application

### Task 1: Configure VNet integration with App Services

In this task, you add the networking configuration to your App Service to enable communication with resources in the VNet.

1. In the Azure portal, search and select **Resource groups** from the list select the **<inject key="Resource Group Name" enableCopy="false"/>** and then click on **wwi-web-<inject key="Suffix" enableCopy="false"/>** App Service from the list of resources.

   ![](media/new-image57.png)

2. On the **App Service** blade, select **Networking** from the left-hand menu under the **Settings** section.

   ![On the App Service blade, Networking is selected in the left-hand menu, and Click here to configure is highlighted under VNet Integration.](media/web-app-network.png "App Service")

3. On the **Networking (1)** page, under **Outbound Traffic Configuration**, click **Not Configured (2)** under **Virtual Network Integration**.

   ![](media/sql40.png)

4. Now click on **Add virtual network integration** under **Virtual Network Integration**.

   ![](media/new-image59.png)

5. On the Network Feature Status dialog, enter the following and click **Connect**.

   - **Virtual Network**: Select the `sqlmi-vnet`.
   - **Subnet**: Select any existing subnet from the drop-down menu.

      ![](media/new-image60.png)

6. Within a few minutes, the VNet is added, and your App Service is restarted to apply the changes. Select Refresh to confirm whether the Vnet is connected or not.

   ![](media/new-image61(2).png)

   > **Note**: If you receive a message adding the Virtual Network to the Web App fails, select **Disconnect** on the VNet Configuration blade, and repeat steps 3 - 5 above.

### Task 2: Open the web application

In this task, you verify your web application now loads, and you can see the home page of the web app.

1. Select **Overview** in the left-hand menu of your App Service and select the **URL** of your App service to launch the website. This link opens the URL in a browser window.

   ![](media/new-image62.png)

2. Verify that the website and data are loaded correctly. The page should look similar to the following:

   ![](media/gs-g-et-60.png)

   > **Note**: It can often take several minutes for the network configuration to be reflected in the web app. If you get an error screen, try selecting Refresh a few times in the browser window. If that does not work, try selecting **Restart** on the Azure Web App's toolbar.

3. Congratulations, you successfully connected your application to the new SQL MI database.

4. Please note down the Managed database name **WideWorldImporters<inject key="Suffix" enableCopy="false"/>** and fully qualified domain name of your SQL-managed instance, which you copied from the Azure Cloud Shell as you need this database name for upcoming lab.

> **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
- If you receive a success message, you can proceed to the next task.
- If not, carefully read the error message and retry the step, following the instructions in the lab guide.
- If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.

<validation step="cdcba397-3f4f-4efe-8a59-632a5651f93b" />

## Summary

In this lab, you have configured VNet integration with App Services and opened the web application.

### You have successfully completed the lab!
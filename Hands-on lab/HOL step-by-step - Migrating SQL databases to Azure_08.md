# Exercise 4: Integrate App Service with the virtual network

### Estimated Duration: 30 minutes

In this lab, you will configure VNet integration with Azure App Services and open the web application. This setup allows your web app to securely communicate with resources in your virtual network. By the end of this lab, your web application will be integrated with the VNet and accessible for use.

## Lab objectives

In this lab, you will complete the following tasks:

- Task 1: Configure VNet integration with App Services
- Task 2: Open the web application

### Task 1: Configure VNet integration with App Services

In this task, you add the networking configuration to your App Service to enable communication with resources in the VNet.

1. In the Azure portal `https://portal.azure.com`, select **Resource groups** from the left-hand menu, select the **<inject key="Resource Group Name" enableCopy="false"/>** resource group and then select the **wwi-web-<inject key="Suffix" />** App Service from the list of resources.

   ![The wwi-web-UNIQUEID App Service is highlighted in the list of resource group resources.](media/datamod9.png "Resource group")

2. On the App Service blade, select **Networking** from the left-hand menu.

   ![On the App Service blade, Networking is selected in the left-hand menu, and Click here to configure is highlighted under VNet Integration.](media/networking.png "App Service")

3. On the **Networking** page, click on the **VNet integration** under **Outbound Traffic**.

   ![Add VNet is highlighted on the VNet Configuration blade.](media/Vnet-integration.png "App Service")

3. Now click on **Add virtual network integration** under **Virtual Network Integration**.

   ![Add VNet is highlighted on the VNet Configuration blade.](media/datamod10.png "App Service")

1. On the Network Feature Status dialog, enter the following and click **Connect**.

   - **Virtual Network**: Select the `vnet-sqlmi--cus`.
   - **Subnet**: Select any existing subnet from the drop-down menu.

      ![image](https://user-images.githubusercontent.com/45102602/171028272-b63fce6e-06fd-4f74-ba45-8bac755cb9d6.png)

  > **Note**: If you see **Failed to add delegation to existing subnet** please select any other available subnet from the list of dropdowns.

  > **Note**: If you are not able to select any existing subnet, Select the create new subnet option and enter the name as Webappsubnet<inject key="Suffix" />. Select the Virtual Network address block i.e., 10.0.0.0/16 from the drop-down list. In the subnet address block enter new address block 10.0.xx.0/24 for the subnet, make sure it is not overlapping other subnet's address.

  > **Note**: If the address space is overlapping with other subnets, change the virtual network address block by selecting a different virtual network address block i.e., 10.1.0.0/16 or 10.2.0.0/16 from the drop-down. In the subnet address block, enter 10.1.xx.0/24 or 10.2.xx.0/24 according to the virtual network address block you have selected and make sure it is not overlapping the other subnet's address.

   ![The values specified above are entered into the Network Feature Status dialog.](media/updated18.png "App Service")

5. Within a few minutes, the VNet is added, and your App Service is restarted to apply the changes. Select Refresh to confirm whether the Vnet is connected or not.

   ![The details of the VNet Configuration are displayed. The Certificate Status, Certificates in sync, is highlighted.](media/datamod11.png "App Service")

   > **Note**: If you receive a message adding the Virtual Network to the Web App fails, select **Disconnect** on the VNet Configuration blade, and repeat steps 3 - 5 above.

### Task 2: Open the web application

In this task, you verify your web application now loads, and you can see the home page of the web app.

1. Select **Overview** in the left-hand menu of your App Service and select the **URL** of your App service to launch the website. This link opens the URL in a browser window.

   ![The App service URL is highlighted.](media/app-service-url.png "App service URL")

2. Verify that the website and data are loaded correctly. The page should look similar to the following:

   ![Screenshot of the WideWorldImporters Operations Web App.](media/wwi-web-app.png "WideWorldImporters Web")

   > **Note**: It can often take several minutes for the network configuration to be reflected in the web app. If you get an error screen, try selecting Refresh a few times in the browser window. If that does not work, try selecting **Restart** on the Azure Web App's toolbar.

3. Congratulations, you successfully connected your application to the new SQL MI database.

## Summary

In this hands-on lab, you will configure VNet integration with Azure App Services and open the web application.



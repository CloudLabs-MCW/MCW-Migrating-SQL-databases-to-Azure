# Exercise 3: Update the web application to use the new SQL MI database

### Estimated Duration: 30 Minutes

## Lab Scenario

In this exercise, you will deploy a web app to Azure and update its App Service configuration. This process involves setting up the web app in the Azure environment and configuring the necessary settings to ensure it runs smoothly. By the end of this lab, you will have a fully deployed and configured web app on Azure, ready for use.

## Lab Objectives

In this exercise, you will complete the following tasks:

- Task 1: Deploy the web app to Azure
- Task 2: Update App Service configuration

    > **Note**: Azure SQL Managed Instance has a private IP address in a dedicated VNet, so to connect an application, you must configure access to the VNet where the Managed Instance is deployed. To learn more, read Connect your application to Azure SQL Managed Instance `https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance`.

## Task 1: Deploy the web app to Azure

1. Navigate to your **Lab VM** desktop.

1. In the File Explorer dialog, navigate to the `C:\hands-on-lab\MCW-Migrating-SQL-databases-to-Azure-master\Hands-on lab\lab-files`. In the **lab-files** folder, double-click **WideWorldImporters.sln** to open the solution in **Visual Studio**.

   ![The folder at the path specified above is displayed, and WideWorldImporters.sln is highlighted.](media/windows-explorer-lab-files-web-solution.png "Windows Explorer")

1. If prompted **How you want to open the file?**, select **Visual Studio 2022 (1)** and then click **OK (2)**.

    ![](media/new/4.png)

1. Select **Sign in with Microsoft** and choose **Work or school account (1)**, click **Continue (2)** and enter the following **Azure account** credentials if prompted:
   
   * Email/Username: <inject key="AzureAdUserEmail"></inject>
   * Password: <inject key="AzureAdUserPassword"></inject>

     ![](media/E3T1S4.1-1809.png)

     ![](media/new-image40.png)

1. On the **Sign in to app apps, websites, and services on this device?** pop-up, click on **No**. Then, on the **Account added to this device** page, select **Done**. 

    ![](media/new/5.png)

1. Once you sign in, click on **Start Visual Studio**.

    ![](media/new-image45.png)

    >**Note:** If you are prompted with **ASP.Net and Web development** installer, select **Install**. This process may take a few minutes and open Visual Studio once the installation is complete.

    ![](media/new/6.png)

1. If you are prompted with a security warning, uncheck **Ask me for every project in this solution (1)**, and then select **OK (2)**.

    ![A Visual Studio security warning is displayed, and the Ask me for every project in this solution checkbox is unchecked and highlighted.](media/new/8.png)

1. Once logged into **Visual Studio**, in the **Solution Explorer** on the right side, right-click the **WideWorldImporters.Web (1)** project, and then select **Publish (2)** from the context menu.

    ![In the Solution Explorer, the context menu for the WideWorldImporters.Web project is displayed, and Publish is highlighted.](media/new/9.png)

    >**Note:** If you see the **WideWorldImporters.Web (unloaded)** project, right-click on it and select **Reload Project**.

    > **Note:** If the project does not load even after clicking **Reload Project**, click **Install** under the prompt to install the required extra components and .NET framework (this process may take about 5 minutes). After installation, retry loading the project by right-clicking it and selecting **Reload Project**.

1. On the **Publish** dialog box, select **Azure (1)** in the **Target** box, and click **Next (2)**.

    ![](media/new-image46.png)

1. Next, in the **Specific target** box, select **Azure App Service (Windows) (1)** and click **Next (2)**.

    ![](media/new-image47.png)

1. Finally, in the **App Service** box, select your **subscription (1)**, expand the **hands-on-lab-<inject key="Suffix" enableCopy="false"/>** resource group, and select the **wwi-web-<inject key="Suffix" enableCopy="false"/> (2)** Web App then click on **Finish (3)**.

    ![](media/new-image48.png)

1. Once completed, click on **Close**.

    ![](media/new-image49.png)

1. Back on the Visual Studio Publish page for the `WideWorldImporters.Web` project, select **Publish** to start the process of publishing your Web API to your Azure API App.

    ![](media/new-image50.png)

1. When the publish completes, you will see a message on the Visual Studio Output page that the **Publish Succeeded**.

    ![](media/sql32.png)

1. If you select the link of the published web app from the Visual Studio output window, an error page is returned because the database connection strings have not been updated to point to the SQL MI database. You address this in the next task.

    ![An error screen is displayed because the database connection string has not been updated to point to SQL MI in the web app's configuration.](media/web-app-error-screen.png "Web App error")

## Task 2: Update App Service configuration

In this task, you update the WWI gamer info web application to connect to and utilize the SQL MI database.

1. Navigate back to the Azure portal, search for **Resource groups (1)** and select **Resource groups (2)** from the results.

   ![](media/gs-g-et-49.png)

1. Select the **<inject key="Resource Group Name" enableCopy="false"/>** resource group from the list.

     ![](media/new-image(3).png)
 
1. Select the **wwi-web-<inject key="Suffix" enableCopy="false"/>** App Service from the list of resources.

   ![](media/E3T2S3-1809.png)

1. From the left navigation pane, select **Environment variables** **(1)** under Settings, select **Connection strings** **(2)** and click on **Advanced edit** **(3)**.

   ![](media/E3T2S4-1809.png)

1. Replace the **value** of the connection string of both `wwiContext` and `WwiReadOnlyContext` with the below-mentioned **value**: 

    ``
    Server=tcp:your-sqlmi-host-fqdn-value,1433;Database=WideWorldImportersSuffix;User ID=contosoadmin;Password=IAE5fAijit0w^rDM;Trusted_Connection=False;Encrypt=True;TrustServerCertificate=True;
    ``
1. Now, replace `your-sqlmi-host-fqdn-value` with the fully qualified domain name for your **SQL MI** that you copied to a text editor earlier from the **Azure Cloud Shell**, and replace the `Suffix` with value: **<inject key="suffix" />** and select **OK**.

    ![](media/gs-g-et-52.png)

    ![](media/E3T2S5-1809.png)

    >**Note**: Copy the name and value of both **`wwiContext`** and **`WwiReadOnlyContext`** and paste them into a text editor; they will be used in a later step.
   
1. Click on **Apply** and then select **Confirm**. 

   ![](media/gs-g-et-54.png)

   ![](media/gs-g-et-55.png)
     
1. Back on **wwi-web-<inject key="Suffix" enableCopy="false"/>**, select **Environment variables** **(1)** under Settings, select **App settings** **(2)** and click on **+ Add** **(3)**.

      ![](media/E3T2S8-0701.png)
    
1. Add the **Name** as `wwiContext` and **Value** which you recorded in Notepad and click on **Apply**.

    ![](media/gs-g-et-56.png)

1. Repeat above step for `wwiReadOnlyContext` and paste the **Name** and **Value** which you recorded in notepad and click on **Apply**.

      ![](media/new-image55.png)

1. Click on **Apply**.

1. When prompted that your app may restart if you are updating connection strings. Are you sure you want to continue?, Select **Confirm**.

     ![](media/E3T2S12-0701.png)

1. From the left menu, select **Overview** to return to the **Overview** blade of your **App Service**. Then, click on **Default Domain** in the Overview blade. Still result in an error being returned. The error occurs because the SQL Managed Instance has a private IP address in its VNet. To connect an application, you need to configure access to the VNet where the Managed Instance is deployed, which you handle in the next exercise.

    ![](media/new-image62.png)
    
    ![](media/gs-g-et-50.png)

> **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
- If you receive a success message, you can proceed to the next task.
- If not, carefully read the error message and retry the step, following the instructions in the lab guide.
- If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.
    
<validation step="f23ada59-1fcd-4778-a213-dd10679a3f43" />

## Summary

In this exercise, you have deployed a web app to Azure and updated its App Service configuration.

### You have successfully completed the exercise. Now click on **Next >>** from the lower right corner to move on to the next exercise.

![](./media/next-pg.png)

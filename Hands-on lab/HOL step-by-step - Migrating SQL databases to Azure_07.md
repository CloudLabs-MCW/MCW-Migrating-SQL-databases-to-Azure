# Exercise 4: Update the web application to use the new SQL MI database

### Estimated Duration: 30 minutes

## Scenario

Contoso has successfully migrated the **WideWorldImporters** database to **Azure SQL Managed Instance (SQL MI)** and now needs to update its gamer information web application to use the new cloud database environment. 

As an application administrator, you will deploy the web application to **Azure App Service** and configure the application settings and connection strings to securely connect with the SQL Managed Instance database. This ensures the application is properly integrated with the migrated database and ready for cloud-based operations.

## Overview

In this lab, you will deploy a web app to Azure and update its App Service configuration. This process involves setting up the web app in the Azure environment and configuring the necessary settings to ensure it runs smoothly. By the end of this lab, you will have a fully deployed and configured web app on Azure, ready for use.

## Lab Objectives

In this lab, you will complete the following tasks:

- Task 1: Deploy the web app to Azure
- Task 2: Update App Service configuration

### Task 1: Deploy the web app to Azure

In this task, you will use JumpBox VM and then, using Visual Studio on the JumpBox, deploy the `WideWorldImporters` web application into the App Service in Azure.

1. You have already logged in to JumpBox VM, use this VM to continue with the lab. 

1. In the File Explorer dialog, navigate to the `C:\hands-on-lab\MCW-Migrating-SQL-databases-to-Azure-master\Hands-on lab\lab-files`. In the `lab-files` folder, double-click `WideWorldImporters.sln` to open the solution in Visual Studio.

   ![The folder at the path specified above is displayed, and WideWorldImporters.sln is highlighted.](media/windows-explorer-lab-files-web-solution.png "Windows Explorer")

1. If prompted about **How you want to open the file?**, select **Visual Studio 2022**, and then select **OK**.

    ![](media/new-image38.png)

1. Select **Sign in** and choose **Work or school account** > **Continue** and enter the following **Azure account** credentials if prompted:
   
   * Email/Username: <inject key="AzureAdUserEmail"></inject>
   * Password: <inject key="AzureAdUserPassword"></inject>

     ![](media/new-image39.png)

     ![](media/new-image40.png)

1. On the **Stay signed in to all your apps** pop-up, click on **OK**. Then on **You're all set** page, select **Done**. 

1. Once you signed in, Click on **Start Visual Studio**.

    ![](media/new-image45.png)

1. If you are prompted with a security warning, uncheck **Ask me for every project in this solution**, and then select **OK**.

    ![A Visual Studio security warning is displayed, and the Ask me for every project in this solution checkbox is unchecked and highlighted.](media/visual-studio-security-warning.png "Visual Studio")

1. Once logged into **Visual Studio**, right-click the **`WideWorldImporters.Web`** project in the **Solution Explorer**, and then select **Publish**.

    ![In the Solution Explorer, the context menu for the WideWorldImporters.Web project is displayed, and Publish is highlighted.](media/visual-studio-project-publish.png "Visual Studio")

1. On the **Publish** dialog, select **Azure (1)** in the **Target** box, and click **Next (2)**.

    ![](media/new-image46.png)

1. Next, in the **Specific target** box, select **Azure App Service (Windows) (1)** and click **Next (2)**.

    ![](media/new-image47.png)

1. Finally, in the **App Service (1)** box, select your subscription, expand the **hands-on-lab-<inject key="Suffix" enableCopy="false"/>** resource group, and select the **wwi-web-<inject key="Suffix" enableCopy="false"/> (2)** Web App then click on **Finish (3).**

    ![](media/new-image48.png)

1. Select **Close**.

    ![](media/new-image49.png)

1. Back on the Visual Studio Publish page for the `WideWorldImporters.Web` project, select **Publish** to start the process of publishing your Web API to your Azure API App.

    ![](media/new-image50.png)

1. When the publish completes, you will see a message on the Visual Studio Output page that the publish succeeded.

    ![](media/sql32.png)

2. If you select the link of the published web app from the Visual Studio output window, an error page is returned because the database connection strings have not been updated to point to the SQL MI database. You address this in the next task.

    ![An error screen is displayed because the database connection string has not been updated to point to SQL MI in the web app's configuration.](media/web-app-error-screen.png "Web App error")

### Task 2: Update App Service configuration

In this task, you update the WWI gamer info web application to connect to and utilize the SQL MI database.

1. Navigate back to Azure portal, search and select **Resource groups** from the Azure services list.

   ![](media/new-image2.png)

2. Select the **<inject key="Resource Group Name" enableCopy="false"/>** resource group from the list.

    ![](media/new-image(3).png)
 
3. Select the **wwi-web-<inject key="Suffix" enableCopy="false"/>** App Service from the list of resources.

   ![](media/new-image57.png)

4. On the App Service blade, select **Environment variables** **(1)** under Settings from the left-hand pane, select **Connection strings** **(2)** and click on **Advanced edit** **(3)**.

   ![](media/new-image51.png)

6. Replace the **value** of the connection string of `wwiContext` with the below and replace **`your-sqlmi-host-fqdn-value`** with the fully qualified domain name for your SQL MI that you copied to a text editor earlier from the Azure Cloud Shell and replace the suffix with value: <inject key="suffix" />.
    
    ``
    Server=tcp:your-sqlmi-host-fqdn-value,1433;Database=WideWorldImportersSuffix;User ID=contosoadmin;Password=IAE5fAijit0w^rDM;Trusted_Connection=False;Encrypt=True;TrustServerCertificate=True;
    ``

   >**Note**: Copy the name and value of **`wwiContext`** and paste them into a text editor they will be used in a later step.

7. Repeat **steps 5**, this time for the **`wwiReadOnlyContext`** connection string.

    ![The save button on the Configuration blade is highlighted.](media/WwiReadOnlyContext1.png "Save")

    >**Note**: Copy the name and value of **`WwiReadOnlyContext`** and paste them into a text editor they will be used in a later step.
   
8. Select **OK**.

9. Click on **Apply** and then select **confirm**. 

    ![The save button on the Configuration blade is highlighted.](media/WwiReadOnlyContextapplay.png "Save")

   ![](media/new-image52.png)
     
10. Back on **wwi-web-<inject key="Suffix" enableCopy="false"/> | Environment variables**, Click on **Add** from the **App settings**.
     
      ![](media/new-image53.png)
    
11. Add the **Name** and **Value** of `wwiContext` which you recorded in notepad and click on **Apply**

    ![](media/new-image54.png)

12. Repeat above step for **`wwiReadOnlyContext`** and paste the **Name** and **Value** which you recorded in notepad and click on **Apply**

      ![](media/new-image55.png)

13. Click on **Apply**.

14. When prompted that Your app may restart if you are updating connection strings. Are you sure you want to continue?, select **Confirm**.

     ![](media/new-image52.png)

15. From the left menu, select **Overview** to return to the **Overview** blade of your **App Service**. Then, click on **Default Domain** in the Overview blade. still results in an error being returned. The error occurs because the SQL Managed Instance has a private IP address in its VNet. To connect an application, you need to configure access to the VNet where the Managed Instance is deployed, which you handle in the next exercise.

    ![](media/new-image62.png)
    
    ![An error screen is displayed because the application cannot connect to SQL MI within its private virtual network.](media/web-app-error-screen.png "Web App error")

## Review

In this lab, you have deploy the web app to Azure and updated App Service configuration.

### You have successfully completed the lab!

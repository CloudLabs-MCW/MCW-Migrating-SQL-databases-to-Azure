## Exercise 3: Update the web application to use the new SQL MI database

Duration: 30 minutes

With the `WideWorldImporters` database now running on SQL MI in Azure, the next step is to make the required modifications to the WideWorldImporters gamer information web application.

> **Note**
>
> Azure SQL Managed Instance has a private IP address in a dedicated VNet, so to connect an application, you must configure access to the VNet where Managed Instance is deployed. To learn more, read Connect your application to Azure SQL Managed Instance `https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance`.

### Task 1: Deploy the web app to Azure

In this task, you will use JumpBox VM and then, using Visual Studio on the JumpBox, deploy the `WideWorldImporters` web application into the App Service in Azure.

1. You have already logged-in to JumpBox VM, use this VM to continue with the lab. 

1. In the File Explorer dialog, navigate to the `C:\hands-on-lab` folder and then drill down to `Migrating-SQL-databases-to-Azure-master\Hands-on lab\lab-files`. In the `lab-files` folder, double-click `WideWorldImporters.sln` to open the solution in Visual Studio.

   ![The folder at the path specified above is displayed, and WideWorldImporters.sln is highlighted.](media/windows-explorer-lab-files-web-solution.png "Windows Explorer")

1. If prompted about how you want to open the file, select **Visual Studio 2022**, and then select **OK**.

    ![In the Visual Studio version selector, Visual Studio 2019 is selected and highlighted.](media/newupdatedphoto2.png "Visual Studio")

1. Select **Sign in** and enter the following Azure account credentials when prompted:
   * Email/Username: <inject key="AzureAdUserEmail"></inject>
   * Password: <inject key="AzureAdUserPassword"></inject>

    ![On the Visual Studio welcome screen, the Sign in button is highlighted.](media/latestupdate1.4.png "Visual Studio")
    
1. Once you signed in, Click on **Start Visual Studio**.

    ![In the Visual Studio version selector, Visual Studio 2019 is selected and highlighted.](media/latestupdate1.5.png "Visual Studio")

1. At the security warning prompt, uncheck **Ask me for every project in this solution**, and then select **OK**.

    ![A Visual Studio security warning is displayed, and the Ask me for every project in this solution checkbox is unchecked and highlighted.](media/visual-studio-security-warning.png "Visual Studio")

1. Once logged into Visual Studio, right-click the `WideWorldImporters.Web` project in the Solution Explorer, and then select **Publish**.

    ![In the Solution Explorer, the context menu for the WideWorldImporters.Web project is displayed, and Publish is highlighted.](media/visual-studio-project-publish.png "Visual Studio")

1. On the **Publish** dialog, select **Azure** in the Target box, and select **Next**.

    ![In the Publish dialog, Azure is selected and highlighted in the Target box. The Next button is highlighted.](media/updated15.png "Publish API App to Azure")

1. Next, in the **Specific target** box, select **Azure App Service (Windows)**.

    ![In the Publish dialog, Azure App Service (Windows) is selected and highlighted in the Specific Target box. The Next button is highlighted.](media/updated16.png "Publish API App to Azure")

1. Finally, in the **App Service** box, select your subscription, expand the **hands-on-lab-<inject key="Suffix" enableCopy="false"/>** resource group, and select the **wwi-web-<inject key="Suffix" enableCopy="false"/>** Web App, Click on **Finish**.

    ![In the Publish dialog, The wwi-web-UNIQUEID Web App is selected and highlighted under the hands-on-lab- resource group.](media/newupdatedphoto1.png "Publish API App to Azure")

1. Back on the Visual Studio Publish page for the `WideWorldImporters.Web` project, select **Publish** to start the process of publishing your Web API to your Azure API App.

    ![The Publish button is highlighted on the Publish page in Visual Studio.](media/wwi-web-publish.png "Publish")

1. When the publish completes, you will see a message on the Visual Studio Output page that the publish succeeded.

    ![The Publish Succeeded message is displayed in the Visual Studio Output pane.](media/visual-studio-output-publish-succeeded.png "Visual Studio")

2. If you select the link of the published web app from the Visual Studio output window, an error page is returned because the database connection strings have not been updated to point to the SQL MI database. You address this in the next task.

    ![An error screen is displayed because the database connection string has not been updated to point to SQL MI in the web app's configuration.](media/web-app-error-screen.png "Web App error")

### Task 2: Update App Service configuration

In this task, you update the WWI gamer info web application to connect to and utilize the SQL MI database.

1. In the Azure portal `https://portal.azure.com`, select **Resource groups** from the Azure services list.

   ![Resource groups is highlighted in the Azure services list.](media/azure-services-resource-groups.png "Azure services")

2. Select the <inject key="Resource Group Name" enableCopy="false"/> resource group from the list.

   ![Resource groups is selected in the Azure navigation pane, and the "hands-on-lab-< resource group is highlighted.](./media/resource-groups.png "Resource groups list")

3. In the list of resources for your resource group, select the **<inject key="Resource Group Name" enableCopy="false"/>** resource group and then select the **wwi-web-<inject key="Suffix" enableCopy="false"/>** App Service from the list of resources.

   ![The wwi-web-UNIQUEID App Service is highlighted in the list of resource group resources.](media/rg-app-service.png "Resource group")

4. On the App Service blade, select **Configuration** under Settings on the left-hand side.

   ![The Configuration item is selected under Settings.](media/app-service-configuration-menu.png "Configuration")

5. On the Configuration blade, locate the **Connection strings** section and then select the Pencil (Edit) icon to the right of the `WwiContext` connection string.

   ![In the Connection string section, the pencil icon is highlighted to the right of the WwiContext connection string.](media/app-service-configuration-connection-strings.png "Connection Strings")

6. The value of the connection string should look like this:

    
`` 
Server=tcp:your-sqlmi-host-fqdn-value,1433;Database=WideWorldImportersSUFFIX;User     ID=sqlmiuser;Password=Password.1234567890;Trusted_Connection=False;Encrypt=True;TrustServerCertificate=True;
``
   

7. In the Add/Edit connection string dialog, replace `your-sqlmi-host-fqdn-value` with the fully qualified domain name for your SQL MI that you copied to a text editor earlier from the Azure Cloud Shell and replace suffix with value: <inject key="suffix" /> and also change the UserID with `sqlmiuser` and Password with `Password.1234567890`.

   ![The your-sqlmi-host-fqdn-value string is highlighted in the connection string.](https://raw.githubusercontent.com/CloudLabs-MCW/MCW-Migrating-SQL-databases-to-Azure/fix/Hands-on%20lab/media/images/9.png "Edit Connection String")

8. The updated value should look similar to the following screenshot.

   ![The updated connection string is displayed, with the fully qualified domain name of SQL MI highlighted within the string.](media/app-service-configuration-edit-conn-string-value.png "Connection string value")
   
   ![The updated connection string is displayed, with the fully qualified domain name of SQL MI highlighted within the string.](media/latestupdate1.3.png "Connection string value")

9. Select **OK**.

10. Repeat steps 3 - 7, this time for the `WwiReadOnlyContext` connection string.

11. Select **Save** at the top of the Configuration blade.

    ![The save button on the Configuration blade is highlighted.](media/app-service-configuration-save.png "Save")

12. When prompted that changes to application settings and connection strings will restart your application, select **Continue**.

    ![The prompt warning that the application will be restarted is displayed, and the Continue button is highlighted.](media/app-service-restart.png "Restart prompt")

13. Select **Overview** to the left of the Configuration blade to return to the overview blade of your App Service.

    ![Overview is highlighted on the left-hand menu for App Service](media/app-service-overview-menu-item.png "Overview menu item")

14. At this point, selecting the **Default domain ** for the App Service on the Overview blade still results in an error being returned. The error occurs because SQL Managed Instance has a private IP address in its VNet. To connect an application, you need to configure access to the VNet where Managed Instance is deployed, which you handle in the next exercise.

    ![An error screen is displayed because the application cannot connect to SQL MI within its private virtual network.](media/web-app-error-screen.png "Web App error")

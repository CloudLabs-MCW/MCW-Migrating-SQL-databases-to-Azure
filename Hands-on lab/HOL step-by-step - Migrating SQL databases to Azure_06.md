# Exercise 2: Migrate the database to SQL MI

### Estimated Duration: 90 minutes

In this lab, you will migrate the WideWorldImporters database from a SQL Server 2008 VM to Azure SQL Managed Instance. You’ll start by setting up an SMB network share and configuring the MSSQLSERVER service to run under the sqlmiuser account. Then, you’ll back up the database, gather connection information, and create an online data migration project. Finally, you’ll perform the migration cutover and verify the database and transaction log migration. These steps ensure a smooth transition to Azure’s cloud services.

## Lab Objectives

Lab Objectives

In this lab, you will complete the following tasks:

- Task 1: Create an SMB network share on the VM
- Task 2: Change MSSQLSERVER service to run under sqlmiuser account
- Task 3: Create a backup of the WideWorldImporters database
- Task 4: Retrieve SQL MI and SQL Server 2008 VM connection information
- Task 5: Create and run an online data migration project
- Task 6: Perform migration cutover
- Task 7: Verify database and transaction log migration

### Task 1: Create an SMB network share on the **<inject key="SQLVM Name" enableCopy="false"/>** VM

In this task, you create a new SMB network share on the <inject key="SQLVM Name" enableCopy="false"/> VM. DMS uses this shared folder for retrieving backups of the `WideWorldImporters` database during the database migration process.

1. On the **<inject key="SQLVM Name" enableCopy="false"/>** VM, open **Windows Explorer** by selecting its icon on the Windows Taskbar.

   ![The Windows Explorer icon is highlighted in the Windows Taskbar.](media/windows-task-bar.png "Windows Taskbar")

2. In the Windows Explorer window, expand **Computer** in the tree view, select **Windows (C:)**, and then select **New folder** in the top menu.

   ![In Windows Explorer, Windows (C:) is selected under Computer in the left-hand tree view, and New folder is highlighted in the top menu.](media/windows-explorer-new-folder.png "Windows Explorer")

3. Name the new folder **dms-backups**, then right-click the folder and select **Share with** and **Specific people...** in the context menu.

   ![In Windows Explorer, the context menu for the dms-backups folder is displayed, with Share with and Specific people highlighted.](media/windows-explorer-folder-share-with.png "Windows Explorer")

4. In the File Sharing dialog, ensure the **sqlmiuser** is listed with a **Read/Write** permission level, and then select **Share**.

   ![In the File Sharing dialog, the sqlmiuser is highlighted and assigned a permission level of Read/Write.](https://raw.githubusercontent.com/microsoft/MCW-Migrating-SQL-databases-to-Azure/master/Hands-on%20lab/media/file-sharing.png)

5. In the **Network discovery and file sharing** dialog, select the default value of **No, make the network that I am connected to a private network**.

   ![In the Network discovery and file sharing dialog, No, make the network that I am connected to a private network is highlighted.](media/network-discovery-and-file-sharing.png "Network discovery and file sharing")

6. Back on the File Sharing dialog, note the shared folder's path, ```\\SQL2008-SUFFIX\dms-backups```, and select **Done** to complete the sharing process.

   ![The Done button is highlighted on the File Sharing dialog.](media/EX2-Task1-S6.png "File Sharing")

### Task 2: Change MSSQLSERVER service to run under sqlmiuser account

In this task, you use the SQL Server Configuration Manager to update the service account used by the SQL Server (MSSQLSERVER) service to the `sqlmiuser` account. Changing the account used for this service ensures it has the appropriate permissions to write backups to the shared folder.

1. On your **<inject key="SQLVM Name" enableCopy="false"/>** VM, select the **Start menu**, enter "SQL configuration" into the search bar, and then select **SQL Server Configuration Manager** from the search results.

   ![In the Windows Start menu, "sql configuration" is entered into the search box, and SQL Server Configuration Manager is highlighted in the search results.](media/windows-start-sql-configuration-manager.png "Windows search")

   > **Note**: Be sure to choose **SQL Server Configuration Manager**, and not **SQL Server 2017 Configuration Manager**, which does not work for the installed SQL Server 2008 R2 database.

2. In the SQL Server Configuration Managed dialog, select **SQL Server Services** from the tree view on the left, then right-click **SQL Server (MSSQLSERVER)** in the list of services and select **Properties** from the context menu.

   ![SQL Server Services is selected and highlighted in the tree view of the SQL Server Configuration Manager. In the Services pane, SQL Server (MSSQLSERVER) is selected and highlighted. Properties is highlighted in the context menu.](media/sql-server-configuration-manager-services.png "SQL Server Configuration Manager")

3. In the SQL Server (MSSQLSERVER) Properties dialog, select **This account** under Log on as, and enter the following:

   - **Account name**: `sqlmiuser`
   - **Password**: `Password.1234567890`

      ![In the SQL Server (MSSQLSERVER) Properties dialog, This account is selected under Log on as, and the sqlmiuser account name and password are entered.](media/sql-server-service-properties.png "SQL Server (MSSQLSERVER) Properties")

4. Select **OK**.

5. Select **Yes** in the Confirm Account Change dialog.

   ![The Yes button is highlighted in the Confirm Account Change dialog.](media/confirm-account-change.png "Confirm Account Change")

6. Observe that the **Log On As** value for the SQL Server (MSSQLSERVER) service changed to `./sqlmiuser`.

   ![In the list of SQL Server Services, the SQL Server (MSSQLSERVER) service is highlighted.](media/sql-server-service.png "SQL Server Services")

7. Close the SQL Server Configuration Manager.

### Task 3: Create a backup of the WideWorldImporters database

To perform online data migrations, DMS looks for database and transaction log backups in the shared SMB backup folder on the source database server. In this task, you create a backup of the `WideWorldImporters` database using SSMS and write it to the ```\\SQL2008-SUFFIX\dms-backups``` SMB network share you made in a previous task. The backup file needs to include a checksum, so you add that during the backup steps.

1. On the **<inject key="SQLVM Name" enableCopy="false"/>** VM, open **Microsoft SQL Server Management Studio 17** by entering "sql server" into the search bar in the Windows Start menu.

   ![SQL Server is entered into the Windows Start menu search box, and Microsoft SQL Server Management Studio 17 is highlighted in the search results.](media/start-menu-ssms-17.png "Windows start menu search")

2. In the SSMS **Connect to Server** dialog, enter <inject key="SQLVM Name" /> into the Server name box, ensure **Windows Authentication** is selected, and then select **Connect**.

   ![The SQL Server Connect to Search dialog is displayed, with SQL2008 entered into the Server name and Windows Authentication selected.](https://raw.githubusercontent.com/CloudLabs-MCW/MCW-Migrating-SQL-databases-to-Azure/fix/Hands-on%20lab/media/ssms.png "Connect to Server")

3. Once connected, expand **Databases** under **<inject key="SQLVM Name" enableCopy="false"/>** in the Object Explorer, and then right-click the **WideWorldImporters** database. In the context menu, select **Tasks** and then **Back Up...**

   ![In the SSMS Object Explorer, the context menu for the WideWorldImporters database is displayed, with Tasks and Back Up... highlighted.](https://raw.githubusercontent.com/CloudLabs-MCW/MCW-Migrating-SQL-databases-to-Azure/fix/Hands-on%20lab/media/dm5.png "SSMS Backup")

4. In the Back Up Database dialog, you should see `C:\WideWorldImporters.bak` listed in the Destinations box. This device is no longer needed, so select it and then select **Remove**.

   ![In the General tab of the Back Up Database dialog, C:\WideWorldImporters.bak is selected, and the Remove button is highlighted under destinations.](media/ssms-back-up-database-general-remove.png)

5. Next, select **Add** to add the SMB network share as a backup destination.

   ![In the General tab of the Back Up Database dialog, the Add button is highlighted under destinations.](media/ssms-back-up-database-general.png "Back Up Database")

6. In the Select Backup Destination dialog, select the Browse (`...`) button.

   ![The Browse button is highlighted in the Select Backup Destination dialog.](media/ssms-select-backup-destination.png "Select Backup Destination")

7. In the Location Database Files dialog, select the `C:\dms-backups` folder, enter **WideWorldImporters.bak** into the File name field, and then select **OK**.

   ![In the Select the file pane, the C:\dms-backups folder is selected and highlighted, and WideWorldImporters.bak is entered into the File name field.](media/ssms-locate-database-files.png "Location Database Files")

8. Select **OK** to close the Select Backup Destination dialog.

   ![The OK button is highlighted on the Select Backup Destination dialog and C:\dms-backups\WideWorldImporters.bak is entered in the File name textbox.](media/ssms-backup-destination.png "Backup Destination")

9. In the Back Up Database dialog, select **Media Options** in the Select a page pane, and then set the following:

   - Select **Back up to the existing media set** and then select **Overwrite all existing backup sets**.
   - Under Reliability, check the box for **Perform checksum before writing to media**. A checksum is required by DMS when using the backup to restore the database to SQL MI.

      ![In the Back Up Database dialog, the Media Options page is selected, and Overwrite all existing backup sets and Perform checksum before writing to media are selected and highlighted.](media/ssms-back-up-database-media-options.png "Back Up Database")

10. Select **OK** to perform the backup.

11. You will receive a message when the backup is complete. Select **OK**.

    ![Screenshot of the dialog confirming the database backup was completed successfully.](media/ssms-backup-complete.png "Backup complete")

### Task 4: Retrieve SQL MI and SQL Server 2008 VM connection information

In this task, you use the Azure Cloud shell to retrieve the information necessary to connect to your <inject key="SQLVM Name" enableCopy="false"/> VM from DMS.

1. In the Azure portal `https://portal.azure.com`, select the Azure Cloud Shell icon from the top menu.

   ![The Azure Cloud Shell icon is highlighted in the Azure portal's top menu.](media/cloud-shell-icon.png "Azure Cloud Shell")

2. In the Cloud Shell window that opens at the bottom of your browser window, select **PowerShell**.

   ![In the Welcome to Azure Cloud Shell window, PowerShell is highlighted.](media/cloud-shell-select-powershell.png "Azure Cloud Shell")

3. If prompted about not having a storage account mounted, click on **Show advanced settings**. Select Create New under Storage account and provide values as below: 
  
      - **Resource Group**: Select **Use existing** then <inject key="Resource Group Name" enableCopy="false"/>
      - **Storage account**: **storage<inject key="Suffix" enableCopy="false"/>**
      - **File Share**: **blob**

         ![This is a screenshot of the cloud shell opened in a browser window. Powershell was selected.](media/b4-image36.png "Azure Cloud Shell")

4. After a moment, a message is displayed that you have successfully requested a Cloud Shell, and you are presented with a PS Azure prompt.

   ![In the Azure Cloud Shell dialog, a message is displayed that requesting a Cloud Shell succeeded, and the PS Azure prompt is displayed.](media/cloud-shell-ps-azure-prompt.png "Azure Cloud Shell")

5. At the prompt, retrieve the public IP address of the SqlSerer2008 VM. This IP address will be used to connect to the database on that server. Enter the following PowerShell command, **replacing `<your-resource-group-name>`** in the resource group name variable with the name of your resource group: <inject key="Resource Group Name" /> and vm name with <inject key="SQLVM Name" />. 


   ```PowerShell
   $resourceGroup = "<your-resource-group-name>"
   az vm list-ip-addresses -g $resourceGroup -n VMNAME --output table
   ```

   > **Note**
   >
   > If you have multiple Azure subscriptions, and the account you are using for this hands-on lab is not your default account, you may need to run `az account list --output table` at the Azure Cloud Shell prompt to output a list of your subscriptions, then copy the Subscription Id of the account you are using for this lab and then run `az account set --subscription <your-subscription-id>` to set the appropriate account for the Azure CLI commands.

6. Within the output, locate and copy the value of the `ipAddress` property below the `PublicIPAddresses` field. Paste the value into a text editor, such as Notepad.exe, for later reference.

   ![The output from the az vm list-ip-addresses command is displayed in the Cloud Shell, and the public IP address for the Sql2008VM is highlighted.](https://raw.githubusercontent.com/CloudLabs-MCW/MCW-Migrating-SQL-databases-to-Azure/fix/Hands-on%20lab/media/vmip.png "Azure Cloud Shell")

7. Leave the Azure Cloud Shell open for the next task.

### Task 5: Create and run an online data migration project

In this task, you create a new online data migration project in DMS for the `WideWorldImporters` database.

1. In Azure Data Studio click on **Azure SQL Migration** and click on **+ New migration**.

   ![In the Windows Start menu, "data migration" is entered into the search bar, and Microsoft Data Migration Assistant is highlighted in the Windows start menu search results.](media/Ex1-Task2-S1.png "Data Migration Assistant")

2. In **Step 1: Database for assessment** blade, select **widewordimporters**, click on **Next**. 

   ![The new project icon is highlighted in DMA.](media/Ex1-Task2-S2.png "New DMA project")

3. In **Step 2: Assessment summary and SKU recommendation (1)**, you will view the summary and SKU recommendations for your SQL server. Click on **Next (2)**. 

   ![The new project settings for doing a SQL Server to Azure SQL Database migration assessment are entered into the dialog.](media/E1T2S3.png "New project settings")

4. In **Step 3: Target Platform and Assessment Results**, Select **Azure SQL Managed Instance (1)** from the drop down. Then select **WideWorldImporters** under database, checkbox the **WideWorldImporters** under database, and Click on the **Select** button.

   ![](media/Ex2-Task5-S4.png)

5. In **Step 4: Azure SQL target** blade, click on **Link account**, and click on **Add an account** it will redirect to a web page, login using your below **Azure credentials** once your account has been added successfully! Go back to the Azure Data Studio, and click on **close**. 
  
   - **Email/Username**: <inject key="AzureAdUserEmail"></inject>
   - **Password**: <inject key="AzureAdUserPassword"></inject>
  
6. The field will be populated with the details and click on **Next**. 

   ![](media/E2T5S6.png )
   
7. In **Step 4: Azure Database Migration Service** blade, select **Online migration**, select the location of the database backups to use during migration select **My database backups are on a network share** and Click on **Create new** under Azure Database Migration Service.

   ![](media/Ex2-Task5-S7.png) 
   
8. Under **Create Azure Database Migration Service** page, select **hands-on-lab-<inject key="Suffix" />** from the drop-down menu, enter the name as **wwi-sqldms**, and click on **create**.
  
   ![](media/Ex2-Task5-S8.png) 

9. After the resources have been created you can see similar output as shown in the below screenshot. Copy any of the **Authentication keys** to the notepad as it will be used later in the task, minimize the **Azure Data Studio**.  

   ![](media/Ex2-Task5-add.png)
   
   > **Note**: Don't close/cancel Azure Data Studio.

10. On the **sql2008-<inject key="Suffix" />** VM, Select the **Start menu**, enter `Integration Runtime` into the search bar, and then select **Microsoft Integration Runtime** from the search results.
   
      ![](media/Ex2-Task5-S9.png) 
      
11. Paste the **Authentication key** in the box which you coped in earlier in the task and click on **Register**.

    ![](media/Ex2-Task5-S10.png)

12. In New Integration Runtime (Self-hosted) Node leave default and click on **Finish**.

    ![](media/Ex2-Task5-S11.png)

13. Wait for the Integration Runtime to be successful to continue further.

    ![](media/Ex2-Task5-S11b.png)

14. Navigate back to the **Azure Data Studio** click on **Test connection**. Once the test connection is completed successfully click on **Done**. 

    ![](media/Ex2-Task5-S12.png)
          
15. In **Step 6: Data source configuration** blade, enter the following details and click on **Run Validation**:

      > **Note**: Make sure to replace the SUFFIX value with <inject key="Suffix" />   
 
      - **Password**: Enter **Password.1234567890**
      - **Windows user account with read access to the network share location**: Enter **SQL2008-<inject key="Suffix" />\sqlmiuser** 
      - **Password**: Enter **Password.1234567890** 
      - **Resource Group**: Select **hands-on-lab-<inject key="Suffix" />** 
      - **Storage account**: Select the **sqlmistore<inject key="Suffix" />** storage account. 
      - **Target database name**: Enter **WideWorldImporters<inject key="Suffix" />**, make sure to Add **SUFFIX** at the end.
      - **Network share path**: Enter **\\\SQL2008-<inject key="Suffix" />\dms-backups**

         ![](media/E2T5S15.png)

16. In the Run Validate page wait till all the validation steps are successful then click on **Done**.

      ![](media/Ex2-Task5-S14.png)

17. Once you back to **Step 6: Data source configuration** blade, click on **Next**.

      ![](media/Ex2-Task5-S15.png)

18. In **Step 7: Summary** blade, click on **Start migration** .

    ![](media/E2T5S18.png)

19. Click on **Migrations (1)**, from the dropdown menu set Status to **Status: All (2)**, feel free to **Refresh (3)** till the migration status is **Ready for cutover (4)**. 
    
    ![](media/Ex2-Task5-S18.png)

### Task 6: Perform migration cutover

Since you performed an "online data migration," the migration wizard continuously monitors the SMB network share for newly added log backup files. Online migrations enable any updates on the source database to be captured until you initiate the cutover to the SQL MI database. In this task, you add a record to one of the database tables, backup the logs, and complete the migration of the `WideWorldImporters` database by cutting over to the SQL MI database.

1. From the **Azure portal**, navigate to **hands-on-lab-<inject key="Suffix" />** resource group and search for **wwi-sqldms** Database Migration Services and select.

   ![](media/sql-dms.png)

1. In **wwi-sqldms** balde, click on **Migrations**, and selct **sql2008-<inject key="Suffix" />** under **Source name**.
  
   ![](media/sql-2008.png)

1. On the WideWorldImporters screen, note the status of **Restored** for the `WideWorldImporters.bak` file.

   ![](media/EX2-task6-s4.png)
   
1. Navigate back to the **Azure Data studio**, right click on **<inject key="SQLVM Name" /> (1)**, click on **Manage (2)**, and select **New Query (3)**.

    ![](media/Ex1-Task1-S12.png) 

1. Paste the following SQL script, which inserts a record into the `Game` table, into the new query window:

   ```SQL
   USE WideWorldImporters;
   GO

   INSERT [dbo].[Game] (Title, Description, Rating, IsOnlineMultiplayer)
   VALUES ('Space Adventure', 'Explore the universe with our newest online multiplayer gaming experience. Build custom rocket ships and take off for the stars in an infinite open-world adventure.', 'T', 1)
   ```

1. To run the script, select **Run** from the Azure Data Studio toolbar.

    ![](media/Ex1-Task1-S14.png "SSMS Toolbar")

1. After adding the new record to the `Games` table, back up the transaction logs. DMS detects any new backups and ships them to the migration service. Select **New Query** again in the toolbar, and paste the following script into the new query window:

   ```SQL
   USE master;
   GO

   BACKUP LOG WideWorldImporters
   TO DISK = 'c:\dms-backups\WideWorldImportersLog.trn'
   WITH CHECKSUM
   GO
   ```

1. To run the script, select **Run** from the Azure Data Studio toolbar.

    ![](media/Ex1-Task1-S14.png "SSMS Toolbar")

1. Return to the migration status page in the Azure portal. On the WideWorldImporters screen, select **Refresh**, and you should see the **WideWorldImportersLog.trn** file appear with a status of **Queued**.

      ![](media/EX2-task6-s10.png)

   > **Note**: If you don't see it in the transaction logs entry, continue selecting refresh every 10-15 seconds until it appears.

1. Continue selecting **Refresh**, and you should see the **WideWorldImportersLog.trn** status change to **Uploaded**.

1. After the transaction logs are uploaded, they are restored to the database. Once again, continue selecting **Refresh** every 10-15 seconds until you see the status change to **Restored**, which can take a minute or two.

    ![A status of Restored is highlighted next to the WideWorldImportersLog.trn file in the list of active backup files.](https://raw.githubusercontent.com/microsoft/MCW-Migrating-SQL-databases-to-Azure/master/Hands-on%20lab/media/dms-migration-wizard-transaction-log-restored.png)

1. Navigate back to the **Azure Data studio**, click on **Azure SQL Migration**, click on **Migrations** and select **WideWorldImporters** under source database. 

      ![](media/EX2-task6-s13.png)

1. After verifying the transaction log status of **Restored**, select **Complete cutover**.

    ![](media/EX2-task6-s14.png)

1. On the Complete cutover dialog, verify that log backups pending restore are `0`, check **I confirm there are no additional log backups to provide and want to complete cutover**, and then select **Complete cutover**.

    ![](media/EX2-task6-s15.png)

1. Move back to the Migration blade, and verify that the migration status of WideWorldImporters has to change to **Succeeded**. You might have to refresh a couple of times to see the status as Succeeded.

    ![](media/EX2-task6-s16.png)

1. You can also view the migration status in the Azure portal. Return to the wwi-sqldms blade of Azure Database Migration Service, click on **Migrations** ensure that Migration status is **Succeeded**. You might have to refresh to view the status.

    ![](media/EX2-task6-s17.png)

1. You have successfully migrated the `WideWorldImporters` database to Azure SQL Managed Instance.

### Task 7: Verify database and transaction log migration

In this task, you connect to the SQL MI database using SSMS and quickly verify the migration.

1. First, use the Azure Cloud Shell to retrieve the fully qualified domain name of your SQL MI database. In the Azure portal `https://portal.azure.com`, select the Azure Cloud Shell icon from the top menu.

   ![The Azure Cloud Shell icon is highlighted in the Azure portal's top menu.](media/cloud-shell-icon.png "Azure Cloud Shell")

2. In the Cloud Shell window that opens at the bottom of your browser window, select **PowerShell**.

   ![In the Welcome to Azure Cloud Shell window, PowerShell is highlighted.](media/cloud-shell-select-powershell.png "Azure Cloud Shell")

3. After a moment, a message is displayed that you have successfully requested a Cloud Shell, and be presented with a PS Azure prompt.

   ![In the Azure Cloud Shell dialog, a message is displayed that requesting a Cloud Shell succeeded, and the PS Azure prompt is displayed.](media/cloud-shell-ps-azure-prompt.png "Azure Cloud Shell")

4. At the prompt, retrieve information about SQL MI in the SQLMI-Shared-RG resource group by entering the following PowerShell command.

   ```PowerShell
   $resourceGroup = "SQLMI-Shared-RG"
   az sql mi list --resource-group $resourceGroup
   ```

   > **Note**: If you have multiple Azure subscriptions, and the account you are using for this hands-on lab is not your default account, you may need to run the `az account list --output table` at the Azure Cloud Shell prompt to output a list of your subscriptions. Copy the Subscription ID of the account you are using for this lab and then run `az account set --subscription <your-subscription-id>` to set the appropriate account for the Azure CLI commands.

5. Within the above command's output, locate and copy the value of the `fullyQualifiedDomainName` property. Paste the value into a text editor, such as Notepad.exe, for reference below.

   ![The output from the az sql mi list command is displayed in the Cloud Shell, and the fullyQualifiedDomainName property and value are highlighted.](media/cloud-shell-az-sql-mi-list-output.png "Azure Cloud Shell")

6. Return to SSMS on your **<inject key="SQLVM Name" enableCopy="false"/>** VM, and then select **Connect** and **Database Engine...** from the Object Explorer menu.

   ![In the SSMS Object Explorer, Connect is highlighted in the menu, and Database Engine is highlighted in the Connect context menu.](media/ssms-object-explorer-connect.png "SSMS Connect")

7. In the Connect to Server dialog, enter the following:

   - **Server name**: Enter the fully qualified domain name of your SQL-managed instance, which you copied from the Azure Cloud Shell in the previous steps.
   - **Authentication**: Select **SQL Server Authentication**.
   - **Login**: Enter `contosoadmin`
   -  **Password**: Enter `IAE5fAijit0w^rDM`
   - Check the **Remember password** box.

      ![The SQL managed instance details specified above are entered into the Connect to Server dialog.](https://raw.githubusercontent.com/SpektraSystems/MCW-Migrating-SQL-databases-to-Azure/stage/Hands-on%20lab/media/ssmsmi.png "Connect to Server")

8. Select **Connect**. 

9. The SQL MI connection appears below the <inject key="SQLVM Name" enableCopy="false"/> connection. Expand Databases the SQL MI connection and select the <inject key="Database Name" /> database.

   ![In the SSMS Object Explorer, the SQL MI connection is expanded, and the WideWorldImporters database is highlighted and selected.](https://raw.githubusercontent.com/CloudLabs-MCW/MCW-Migrating-SQL-databases-to-Azure/fix/Hands-on%20lab/media/dm23.png "SSMS Object Explorer")

10. With the **<inject key="Database Name" enableCopy="false"/>** database selected, select **New Query** on the SSMS toolbar to open a new query window.

11. In the new query window, enter the following SQL script:
    > **Note**: Make sure to replace the SUFFIX value with **<inject key="Suffix" />**

      ```SQL
      USE WideWorldImportersSUFFIX;
         GO

         SELECT * FROM Game
      ```  

12. Select **Execute** on the SSMS toolbar to run the query. Observe the records contained within the `Game` table, including the new `Space Adventure` game you added after initiating the migration process.

    ![In the new query window, the query above has been entered, and in the results pane, the new Space Adventure game is highlighted.](media/datamod8.png "SSMS Query")

13. You are done using the **<inject key="SQLVM Name" enableCopy="false"/>** VM. Close any open windows and log off the VM. The JumpBox VM is used for the remaining tasks of this hands-on lab.

## Summary

In this hands-on lab, you will migrate the WideWorldImporters database from a SQL Server 2008 VM to Azure SQL Managed Instance.

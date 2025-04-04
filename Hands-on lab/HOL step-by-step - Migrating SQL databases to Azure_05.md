# Exercise 1: Perform database assessments

### Estimated Duration: 90 minutes

In this lab, you will connect to the WideWorldImporters database on the SqlServer2008 VM and perform assessments for migration to Azure SQL Database and Azure SQL Managed Instance. These assessments will help you understand the compatibility and readiness of your database for migration to Azure. You will evaluate the database schema, data, and performance to identify any potential issues and determine the best migration strategy. This process ensures a smooth transition to Azure’s cloud services, leveraging their scalability, security, and advanced features.

## Lab Objective

In this lab, you will complete the following tasks:

- Task 1: Connect to the WideWorldImporters database on the SqlServer2008 VM
- Task 2: Perform assessment for migration to Azure SQL Database
- Task 3: Perform assessment for migration to Azure SQL Managed Instance

### Task 1: Connect to the WideWorldImporters database on the SqlServer2008 VM

In this task, you perform some configuration for the `WideWorldImporters` database on the SQL Server 2008 R2 instance to prepare it for migration.

1. Navigate to the [Azure portal](https://portal.azure.com) and select **Resource groups** from the Azure services list.

   ![Resource groups is highlighted in the Azure services list.](media/datamod13.png "Azure services")

1. Select the **hands-on-lab-<inject key="Resource Group Name" enableCopy="false"/>** resource group from the list.

   ![Resource groups is selected in the Azure navigation pane, and the "hands-on-lab resource group is highlighted.](./media/resource-groups1.png "Resource groups list")

1. In the list of resources for your resource group, select the **<inject key="SQLVM Name" enableCopy="false"/>** VM.

   ![The SqlServer2008 VM is highlighted in the list of resources.](https://raw.githubusercontent.com/CloudLabs-MCW/MCW-Migrating-SQL-databases-to-Azure/fix/Hands-on%20lab/media/images/vmrg.png "Resource list")

1. From the overview page of  the **<inject key="SQLVM Name" enableCopy="false"/>** VM, select **Connect**.

    ![The Passed tab is highlighted, and VA1219 is entered into the search filter. VA1219 with a status of PASS is highlighted in the results.](media/datamod18.png "Passed")

1. On the **sql2008-<inject key="Suffix" enableCopy="false"/> | Connect** page, click on the **Select (1)** button in the **Native RDP** tile, on the **Native RDP** tab wait for **Configure prerequisites for Native RDP (2)** to validate, and then click on **Download RDP file (3)**. 
  
   ![The Passed tab is highlighted, and VA1219 is entered into the search filter. VA1219 with a status of PASS is highlighted in the results.](media/datamod14png.png "Passed")

1. Click on **Keep**, on the Downloads pop-up. 

   ![The Passed tab is highlighted, and VA1219 is entered into the search filter. VA1219 with a status of PASS is highlighted in the results.](media/datamod15.png "Passed")

1. Click on **Open file**.

   ![](media/datamod16.png)

1. Next, on the RDP tab click on **Connect**.

   ![The Passed tab is highlighted, and VA1219 is entered into the search filter. VA1219 with a status of PASS is highlighted in the results.](media/datamod17.png "Passed")

1. Enter the following credentials when prompted, and then select **OK**:

   - **Username**: `sqlmiuser`
   - **Password**: `Password.1234567890`

      ![The credentials specified above are entered into the Enter your credentials dialog.](media/rdc-credentials-sql-2008.png "Enter your credentials")

1. Select **Yes** to connect if prompted that the remote computer's identity cannot be verified.

   ![In the Remote Desktop Connection dialog box, a warning states that the remote computer's identity cannot be verified and asks if you want to continue anyway. At the bottom, the Yes button is circled.](./media/remote-desktop-connection-identity-verification-sqlserver2008.png "Remote Desktop Connection dialog")

1. Open file explorer on your **<inject key="SQLVM Name" enableCopy="false"/>** virtual machine, naviagate to C:\ drive and click on **IntegrationRuntime** installer.

   ![](media/intergation-runtime1.png "intergation runtime ")

1. In **Welcome to the Microsoft Integration Runtime Setup Wizard**, click on **Next**.

   ![](media/Ex1-install-s2.png "Windows start menu search")

1. In **End-User License Agreement**, select the checkbox **I accept the terms in the License Agreement**, and click on **Next**.

   ![](media/Ex1-install-s3.png "Windows start menu search")

1. In **Destination Folder**, click on **Next**.

   ![](media/Ex1-install-s4.png "Windows start menu search")

1. In **Ready to install Microsoft Integration Runtime**, click on **Install**.

   ![](media/Ex1-install-s5.png "Windows start menu search")

1. Once the deployment is completed click on **Finish** and minimize the application.

   ![](media/Ex1-install-s6.png "Windows start menu search")

1. Next go back to LabVM, and open **Azure Data Studio** by entering "Azure Data Studio" into the search bar in the Windows Start menu and selecting **Azure Data Studio** from the search results.

   ![](media/E1T1S15.png "Windows start menu search")
   
1. In the Azure Data Studio select **Extensions (1)** from the Activity Bar, enter **SQL Migration (2)** into the search bar, select **Azure SQL Migration (3)**, and click on **Install (4)**.  

   ![](media/Ex1-Task1-S9b.png)

1. In the Azure Data Studio select **Connections (1)** from the Activity Bar, click on **New Connection (1)** dialog, enter **<inject key="SQLVM Name" /> (2)** into the Server name box, ensure **Windows Authentication** is selected, and then select **Connect (3)**.
  
    ![The SQL Server Connect to Search dialog is displayed, with SQL2008-entered into the Server name and Windows Authentication selected.](media/Ex1-Task1-S10.png "Connect to Server")
    
    > **Note**: If you see **Connection error** pop-up click on **Enable Trust server certificate**.

      ![](media/ex1-note.png "Connect issue")

1. Once connected, verify you see the `WideWorldImporters` database listed under databases.

    ![The WideWorldImporters database is highlighted under Databases on the SQL2008-instance.](media/Ex1-Task1-S11.png "WideWorldImporters database")

1. Right click on **<inject key="SQLVM Name" /> (1)**, click on **Manage (2)**, and select **New Query (3)** from the Azure Data Studio toolbar.

    ![The New Query button is highlighted in the SSMS toolbar.](media/Ex1-Task1-S12.png "SSMS Toolbar")

1. Next, copy and paste the SQL script below into the new query window. This script enables the Service broker and changes the database recovery model to FULL.

    ```sql
    USE master;
    GO

    -- Update the recovery model of the database to FULL and enable Service Broker
    ALTER DATABASE WideWorldImporters SET
    RECOVERY FULL,
    ENABLE_BROKER WITH ROLLBACK IMMEDIATE;
    GO
    ```

1. To run the script, select **Run** from the Azure Data Studio toolbar.

    ![The Execute button is highlighted in the SSMS toolbar.](media/Ex1-Task1-S14.png "SSMS Toolbar")

### Task 2: Perform assessment for migration to Azure SQL Database

In this task, you use the Microsoft Data Migration Assistant (DMA) to assess the `WideWorldImporters` database against the Azure SQL Database (Azure SQL DB). The assessment provides a report about any feature parity and compatibility issues between the on-premises database and the Azure SQL DB service.

1. In Azure Data Studio click on **Azure SQL migration** and click on **+ New migration**.

   ![In the Windows Start menu, "data migration" is entered into the search bar, and Microsoft Data Migration Assistant is highlighted in the Windows start menu search results.](media/Ex1-Task2-S1.png "Data Migration Assistant")

2. In **Step 1: Database for assessment**, select **widewordimplantation**, click on **Next**. 

   ![The new project icon is highlighted in DMA.](media/Ex1-Task2-S2.png "New DMA project")

3. In **Step 2: Assessment summary and SKU recommendation (1)**, you will view the summary and SKU recommendations for your SQL server. Click on **Next (2)**. 

   ![The new project settings for doing a SQL Server to Azure SQL Database migration assessment are entered into the dialog.](media/E1T2S3.png "New project settings")

4. In **Step 3: Target Platform and Assessment Results**, Select **Azure SQL DataBase(1)** from the drop down. Then select **WideWorldImporters(2)** under the database, review the migration assessment to determine the possibility of migrating to Azure SQL DB, and Click on the **Cancel (3)** button.

   ![The new project settings for doing a SQL Server to Azure SQL Database migration assessment are entered into the dialog.](media/E1T2S4.png "New project settings")

    > The DMA assessment for migrating the `WideWorldImporters` database to a target platform of Azure SQL DB reveals features in use that are not supported. These features, including Service broker, prevent WWI from migrating to the Azure SQL DB PaaS offering without making changes to their database.

### Task 3: Perform assessment for migration to Azure SQL Managed Instance

With one PaaS offering ruled out due to feature parity, perform a second DMA assessment against Azure SQL Managed Instance (SQL MI). The assessment provides a report about any feature parity and compatibility issues between the on-premises database and the SQL MI service.

1. In Azure Data Studio click on **Azure SQL migration** and click on **+ New migration**.

   ![In the Windows Start menu, "data migration" is entered into the search bar, and Microsoft Data Migration Assistant is highlighted in the Windows start menu search results.](media/Ex1-Task2-S1.png "Data Migration Assistant")

2. In **Step 1: Database for assessment**, select **widewordimplantation**, click on **Next**. 

   ![The new project icon is highlighted in DMA.](media/Ex1-Task2-S2.png "New DMA project")

3. In **Step 2: Assessment summary and SKU recommendation (1)**, you will view the summary and SKU recommendations for your SQL server. Click on **Next (2)**. 

   ![The new project settings for doing a SQL Server to Azure SQL Database migration assessment are entered into the dialog.](media/E1T2S3.png "New project settings")

4.  In **Step 3: Target Platform and Assessment Results**, Select **Azure SQL Managed Instance (1)** from the drop down. Then select **WideWorldImporters (2)** under the database, review the migration assessment to determine the possibility of migrating to Azure SQL DB, and Click on the **Cancel (3)** button.

   ![The new project settings for doing a SQL Server to Azure SQL Database migration assessment are entered into the dialog.](media/E1T3S4.png "New project settings")

10. The database, including the Service Broker feature, can be migrated as is, providing an opportunity for WWI to have a fully managed PaaS database running in Azure. Previously, their only option for migrating a database using features incompatible with Azure SQL Database, such as Service Broker, was to deploy the database to a virtual machine running in Azure (IaaS) or modify the database and associated applications to remove the use of the unsupported features. The introduction of Azure SQL MI, however, provides the ability to migrate databases into a managed Azure SQL database service with _near 100% compatibility_, including the features that prevented them from using Azure SQL Database.

## Summary

In this exercise, you connected to the WideWorldImporters database on the SqlServer2008 VM, performed an assessment for migration to Azure SQL Database, and conducted an assessment for migration to Azure SQL Managed Instance. This process involved evaluating the current database setup, identifying potential compatibility issues, and determining the best migration strategy to ensure a smooth transition to Azure's cloud services.

## You have successfully completed the lab.

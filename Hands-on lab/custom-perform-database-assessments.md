# Lab 03: Perform database assessments

### Estimated Duration: 60 Minutes

In this lab, you use the Azure Data Studio to perform assessments on the `WideWorldImporters` database. You create two assessments: one for SQL DB and a second for SQL MI. These assessments provide reports about any feature parity and compatibility issues between the on-premises database and the Azure-managed SQL database service options.

## Lab Objectives

In this lab, you will perform the following tasks:

- Task 1: Connect to the WideWorldImporters database on the SQL 2019 VM
- Task 2: Perform assessments for migration

## Task 1: Connect to the WideWorldImporters database on the SQL 2019 VM

In this task, you will create `WideWorldImporters` database on the SQL 2019 VM instance, assess it for Azure SQL Database and Azure SQL Managed Instance using the Azure SQL Migration extension inside Azure Data Studio for migration.

1. Navigate to the [Azure portal](https://portal.azure.com) and select **Resource groups** from the Azure services list.

   ![](./media/new/q1.png)

1. Select the **SQLMigrationRG** resource group from the list.

   ![](./media/L3T1S2-2009.png)

1. In the list of resources for your resource group, select the **sql2019-<inject key="DeploymentID" enableCopy="false"/>** VM.

   ![](./media/L3T1S3-2009.png)

1. On the **sql2019-<inject key="DeploymentID" enableCopy="false"/>** VM blade in the Azure portal, in the **Overview** page, click on **Connect (1)** dropdown and select **Connect (2)**.

   ![](./media/L3T1S4-2009.png) 

1. On the Connect blade, select **Download RDP File** under **Native RDP**, then open the downloaded RDP file.

   ![](./media/L3T1S5-2009.png)

1. Open the downloaded RDP file, click on **More choices (1) > Use a different account (2)**, and enter the following credentials when prompted, and then select **OK:**

   - **Username:** `.\sqlmiuser` **(3)**
   - **Password:** `Password.1234567890` **(4)**

      ![](./media/enter-creds.png) 

1. Select **Yes** to connect if prompted that the remote computer's identity cannot be verified.

   ![In the Remote Desktop Connection dialog box, a warning states that the remote computer's identity cannot be verified and asks if you want to continue anyway. At the bottom, the Yes button is circled.](./media/remote-desktop-connection-identity-verification-sqlserver2008.png "Remote Desktop Connection dialog")

1. Open file explorer on your **sql2019-<inject key="DeploymentID" enableCopy="false"/>** virtual machine, naviagate to **C:\ (1)** drive and double click on **IntegrationRuntime (2)** installer.

   ![](./media/integration-runtime.png)

1. In **Welcome to the Microsoft Integration Runtime Setup Wizard**, click on **Next**.

   ![](media/Ex1-install-s2.png "Windows start menu search")

1. In **End-User License Agreement**, select the checkbox **I accept the terms in the License Agreement**, and click on **Next**.

   ![](media/Ex1-install-s3.png "Windows start menu search")

1. In **Destination Folder**, click on **Next**.

   ![](media/Ex1-install-s4.png "Windows start menu search")

1. In **Ready to install Microsoft Integration Runtime**, click on **Install**.

   ![](media/new/q2.png)

1. Once the deployment is completed, click on **Finish** and minimize the application.

   ![](media/Ex1-install-s6.png "Windows start menu search")

1. In the **Register Integration Runtime (Self-hosted)** pop up, click on **Cancel**.

   ![](media/new/q3.png)

1. In the **sql2019-<inject key="DeploymentID" enableCopy="false"/>** virtual machine, search for **PowerShell (1)** in the search bar, then select **Windows PowerShell (2)** from the search results to open a new PowerShell window.

   ![](media/new/w1.png)

1. Run the command below to create a database named **WideWorldImporters**.

   ```
   Invoke-Sqlcmd -Query "CREATE DATABASE WideWorldImporters;" -ServerInstance <YOUR SQL VM NAME>
   ```

   ![](media/new/w2.png)

   > **Note:** Replace "YOUR SQL VM NAME" with your actual SQL 2019 VM name **sql2019-<inject key="DeploymentID" enableCopy="false"/>**,

1. In the Windows search bar, type **Azure Data Studio (1)**, then select **Azure Data Studio (2)** from the search results to open a new PowerShell window.

   ![](media/new/w3.png)
   
1. In the Azure Data Studio select **Extensions (1)** from the Activity Bar, enter **SQL Migration (2)** into the search bar, select **Azure SQL Migration (3)**, and click on **Install (4)**.  

   ![](media/Ex1-Task1-S9b.png)

1. In the Azure Data Studio select **Connections (1)** from the Activity Bar, click on **New Connection (2)** dialog, enter **sql2019-<inject key="DeploymentID" enableCopy="false"/>(3)** into the Server name box, ensure **Windows Authentication (4)** is selected, and then select **Connect (5)**.
  
   ![The SQL Server Connect to Search dialog is displayed, with SQL2008-entered into the Server name and Windows Authentication selected.](media/Ex1-Task1-S10.png "Connect to Server")
    
   > **Note:** If you see **Connection error** pop-up click on **Enable Trust server certificate**.

   ![](media/new/w4.png)

1. Once connected, under **sql2019-<inject key="DeploymentID" enableCopy="false"/> (1)** connection, expand **Databases (1)** and verify that you see the `WideWorldImporters`**(2)** database listed.

   ![](media/new/w5.png)

1. Select the **WideWorldImporters (1)**, navigate to **Home (2)**, and select **New Query (3)** from the **Azure Data Studio** toolbar.

   ![](./media/wwi-new-query.png)

1. Next, copy and paste the SQL script below into the new query window. This script enables the Service Broker and changes the database recovery model to FULL.

   ```sql
   USE master;
   GO

   -- Update the recovery model of the database to FULL and enable Service Broker
   ALTER DATABASE WideWorldImporters SET
   RECOVERY FULL,
   ENABLE_BROKER WITH ROLLBACK IMMEDIATE;
   GO
   ```

1. To run the script, click on **Run (1)** from the Azure Data Studio toolbar and verify the results from the **Messages (2)** tab.

   ![](./media/run-query-messages.png) 

## Task 2: Perform assessments for migration

In this task, you use the Microsoft Data Migration Assistant (DMA) to assess the `WideWorldImporters` database against Azure SQL Database (Azure SQL DB). The assessment provides a report about any feature parity and compatibility issues between the on-premises database and the Azure SQL DB service.

1. In Azure Data Studio, select **sql2019-<inject key="DeploymentID" enableCopy="false"/> (1)** tab from the top, click on **Azure SQL migration (2)** and click on **+ New migration (3)**

   ![](./media/new/w6.png) 

1. In **Step 1: Database for assessment**, choose **No (1)** in "Do you want to track the migration process in Azure Portal?" and then select **WideWordImporters (2)** and click **Next (3)** to proceed. 

   ![](./media/new/w7.png)  

1. In **Step 2: Assessment summary and SKU recommendations**, review the assessment summary and SKU recommendations and Azure SQL targets and click on **Next**. 

   ![](./media/step2-review.png) 

1. In **Step 3: Target platform & assessment results**, select **Azure SQL Database** from the dropdown for **Select target type**.

   ![](./media/azure-sql-db.png) 

   > The DMA assessment for migrating the `WideWorldImporters` database to a target platform of Azure SQL DB reveals features in use that are not supported. These features, including Service Broker, prevent WWI from migrating to the Azure SQL DB PaaS offering without making changes to their database.

1. Now select **Azure SQL Managed Instance** from the dropdowm for **Select target type**. Notice that with one PaaS offering ruled out due to feature parity, the assessment against Azure SQL Managed Instance (SQL MI) provides a report about any feature parity and compatibility issues between the on-premises database and the SQL MI service.

   ![](./media/azure-sql-mi.png)

1. The database, including the Service Broker feature, can be migrated as is, providing an opportunity for WWI to have a fully managed PaaS database running in Azure. Previously, their only option for migrating a database using features incompatible with Azure SQL Database, such as Service Broker, was to deploy the database to a virtual machine running in Azure (IaaS) or modify the database and associated applications to remove the use of the unsupported features. The introduction of Azure SQL MI, however, provides the ability to migrate databases into a managed Azure SQL database service with _near 100% compatibility_, including the features that prevented them from using Azure SQL Database.

1. Once you have reviewed the migration possibilities for both Azure SQL Database and Azure SQL Managed Instance, **Cancel** the migration process.

   ![](./media/cancel-migration.png)

## Summary

In this lab, you have completed the following:

- Connected to the WideWorldImporters database on the SQL Server 2019 VM.

- Performed migration assessments to evaluate readiness for Azure migration.

## You have successfully completed the Hands-on lab.

By completing the **Discover And Assess On-prem Windows & SQL Servers** hands-on lab, you learned how to discover and assess on-premises Windows Servers and SQL databases using Azure Migrate and Azure Data Studio. You configured the Azure Migrate Appliance, created migration assessments, and used dependency visualization to understand server relationships. You also assessed database readiness using the SQL Migration extension and explored security features, such as Defender for SQL and Data Discovery. These steps help you plan and prepare for a smooth migration of servers and databases to Azure.

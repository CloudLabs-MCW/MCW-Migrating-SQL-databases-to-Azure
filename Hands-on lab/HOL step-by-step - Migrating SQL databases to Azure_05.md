## Exercise 1: Perform database assessments

Duration: 20 minutes

In this exercise, you use the Microsoft Data Migration Assistant (DMA) to perform assessments on the `WideWorldImporters` database. You create two assessments: one for SQL DB and a second for SQL MI. These assessments provide reports about any feature parity and compatibility issues between the on-premises database and the Azure managed SQL database service options.

> DMA helps you upgrade to a modern data platform by detecting compatibility issues that can impact database functionality in your new version of SQL Server or Azure SQL Database. DMA recommends performance and reliability improvements for your target environment and allows you to move your schema, data, and uncontained objects from your source server to your target server. To learn more, read the [Data Migration Assistant documentation](https://docs.microsoft.com/sql/dma/dma-overview?view=azuresqldb-mi-current).


### Task 1: Connect to the WideWorldImporters database on the SqlServer2008 VM

In this task, you perform some configuration for the `WideWorldImporters` database on the SQL Server 2008 R2 instance to prepare it for migration.


1. Navigate to the [Azure portal](https://portal.azure.com), in **Search resources, services, and docs** search for **Resource groups** and select it from the services list.

   ![Resource groups is highlighted in the Azure services list.](media/Rg-1.png "Azure services")

1. Select the **<inject key="Resource Group Name" enableCopy="false"/>** resource group from the list.

   ![Resource groups is selected in the Azure navigation pane, and the "hands-on-lab resource group is highlighted.](./media/resource-groups1.png "Resource groups list")

1. In the list of resources for your resource group, select the **<inject key="SQLVM Name" enableCopy="false"/>** VM.

   ![The SqlServer2008 VM is highlighted in the list of resources.](./media/vmrg1.png)

1. Fromt the overview page of  the **<inject key="SQLVM Name" enableCopy="false"/>** VM, select **Connect**.

   ![](./media/mod1.png)

1. On the **sql2008-1117397 | Connect** page, click on the **Select (1)** button in the **Native RDP** tile, on the **Native RDP** tab wait for **Configure prerequisites for Native RDP (2)** to validate, and then click on **Download RDP file (3)**. 
  
   ![](./media/mod2.png)

1. Click on **Keep**, on the Downloads pop-up. 

   ![](./media/mod3.png)

1. Click on **Open file**.

   ![](./media/mod4.png)

1. Next, on the RDP tab click on **Connect**.
   
   ![](./media/mod5.png)

1. Enter the following credentials when prompted, and then select **OK**:

   - **Username**: `sqlmiuser`
   - **Password**: `Password.1234567890`

   ![The credentials specified above are entered into the Enter your credentials dialog.](media/rdc-credentials-sql-2008.png "Enter your credentials")

1. Select **Yes** to connect if prompted that the remote computer's identity cannot be verified.

    ![](./media/mod6.png)
  
1. Once logged in, open **Microsoft SQL Server Management Studio 17** (SSMS) by entering "sql server" into the search bar in the Windows Start menu and selecting **Microsoft SQL Server Management Studio 17** from the search results.

   ![SQL Server is entered into the Windows Start menu search box, and Microsoft SQL Server Management Studio 17 is highlighted in the search results.](media/start-menu-ssms-17.png "Windows start menu search")

1. In the SSMS **Connect to Server** dialog, enter <inject key="SQLVM Name" /> into the Server name box, ensure **Windows Authentication** is selected, and then select **Connect**.
  
   ![](./media/ssms.png)

1. Once connected, verify you see the `WideWorldImporters` database listed under databases.

    ![The WideWorldImporters database is highlighted under Databases on the SQL2008-instance.](media/wide-world-importers-database.png "WideWorldImporters database")

    
1. Select **New Query** from the SSMS toolbar.

    ![The New Query button is highlighted in the SSMS toolbar.](media/ssms-new-query.png "SSMS Toolbar")

1. Next, copy and paste the SQL script below into the new query window. This script enables Service broker and changes the database recovery model to FULL.

    ```sql
    USE master;
    GO

    -- Update the recovery model of the database to FULL and enable Service Broker
    ALTER DATABASE WideWorldImporters SET
    RECOVERY FULL,
    ENABLE_BROKER WITH ROLLBACK IMMEDIATE;
    GO
    ```

1. To run the script, select **Execute** from the SSMS toolbar.

    ![The Execute button is highlighted in the SSMS toolbar.](media/ssms-execute.png "SSMS Toolbar")

### Task 2: Perform assessment for migration to Azure SQL Database

In this task, you use the Microsoft Data Migration Assistant (DMA) to assess the `WideWorldImporters` database against Azure SQL Database (Azure SQL DB). The assessment provides a report about any feature parity and compatibility issues between the on-premises database and the Azure SQL DB service.

1. On the **<inject key="SQLVM Name" enableCopy="false"/>** VM, launch DMA from the Windows Start menu by typing "data migration" into the search bar and then selecting **Microsoft Data Migration Assistant** in the search results.

   ![In the Windows Start menu, "data migration" is entered into the search bar, and Microsoft Data Migration Assistant is highlighted in the Windows start menu search results.](media/windows-start-menu-dma.png "Data Migration Assistant")

2. In the DMA dialog, select **+** from the left-hand menu to create a new project.

   ![The new project icon is highlighted in DMA.](media/dma-new.png "New DMA project")

3. In the New project pane, set the following:

   - **Project type**: Select **Assessment**.
   - **Project name**: Enter `ToAzureSqlDb`
   - **Assessment type**: Select **Database Engine**.
   - **Source server type**: Select **SQL Server**.
   - **Target server type**: Select **Azure SQL Database**.

4. Select **Create**.

   ![The new project settings for doing a SQL Server to Azure SQL Database migration assessment are entered into the dialog.](media/dma-new-project-to-azure-sql-db.1.png "New project settings")

5. On the **Options** screen, ensure **Check database compatibility (1)** and **Check feature parity (1)** are checked and then select **Next (2)**.

   ![Check database compatibility and check feature parity are checked on the Options screen.](media/data-mig-ex1-t1-ex5.png "DMA options")

6. On the **Sources** screen, enter the following into the **Connect to a server** dialog that appears on the right-hand side:

   - **Server name**: Enter <inject key="SQLVM Name" />.
   - **Authentication type**: Select **SQL Server Authentication**.
   - **Username**: Enter `WorkshopUser`
   - **Password**: Enter `Password.1234567890`
   - **Encrypt connection**: Check this box.
   - **Trust server certificate**: Check this box.

7. Select **Connect**.

   ![In the Connect to a server dialog, the values specified above are entered](media/data-mig-ex1-t1-ex7.png)

8. On the **Add sources** dialog that appears next, check the box for **WideWorldImporters** and select **Add**.

   ![The WideWorldImporters box is checked on the Add sources dialog.](media/data-mig-ex1-t1-ex8.png)

9. Select **Start Assessment**.

   ![Start assessment](media/data-mig-ex1-t1-ex9.png "Start assessment")

10. Review the migration assessment to determine the possibility of migrating to Azure SQL DB.

    ![For a target platform of Azure SQL DB, feature parity shows two features that are not supported in Azure SQL DB. The Service broker feature is selected on the left and on the right Service Broker feature is not supported in Azure SQL Database is highlighted.](./media/dma-feature-parity-service-broker-not-supported1.png)

    > The DMA assessment for migrating the `WideWorldImporters` database to a target platform of Azure SQL DB reveals features in use that are not supported. These features, including Service broker, prevent WWI from migrating to the Azure SQL DB PaaS offering without making changes to their database.

### Task 3: Perform assessment for migration to Azure SQL Managed Instance

With one PaaS offering ruled out due to feature parity, perform a second DMA assessment against Azure SQL Managed Instance (SQL MI). The assessment provides a report about any feature parity and compatibility issues between the on-premises database and the SQL MI service.

1. To get started, select **+** on the left-hand menu in DMA to create another new project.

   ![The new project icon is highlighted in DMA.](media/dma-new.png "New DMA project")

2. In the New project pane, set the following:

   - **Project type**: Select **Assessment**.
   - **Project name**: Enter `ToSqlMi`
   - **Assessment type**: Select **Database Engine**.
   - **Source server type**: Select **SQL Server**.
   - **Target server type**: Select **Azure SQL Database Managed Instance**.

3. Select **Create**.

   ![The new project settings for doing a SQL Server to Azure SQL Managed Instance migration assessment are entered into the dialog.](media/dma-new-project-to-sql-mi.1.png "New project settings")

4. On the **Options** screen, ensure **Check database compatibility (1)** and **Check feature parity (1)** are checked and then select **Next (2)**.

   ![Check database compatibility and check feature parity are checked on the Options screen.](media/data-mig-ex1-t1-ex5.png "DMA options")

5. On the **Sources** screen, enter the following into the **Connect to a server** dialog that appears on the right-hand side:

   - **Server name**: Enter <inject key="SQLVM Name" />.
   - **Authentication type**: Select **SQL Server Authentication**.
   - **Username**: Enter `WorkshopUser`
   - **Password**: Enter `Password.1234567890`
   - **Encrypt connection**: Check this box.
   - **Trust server certificate**: Check this box.

6. Select **Connect**.

   ![In the Connect to a server dialog, the values specified above are entered.](media/data-mig-ex1-t1-ex7.png)

7. On the **Add sources** dialog that appears next, check the box for **WideWorldImporters** and select **Add**.

   ![The WideWorldImporters box is checked on the Add sources dialog.](media/data-mig-ex1-t1-ex8.png)

8. Select **Start Assessment**.

   ![Start assessment](media/data-mig-ex1-t1-ex9.png "Start assessment")

9. Review the SQL Server feature parity and Compatibility issues options of the migration assessment to determine the possibility of migrating to Azure SQL Managed Instance.

   ![For a target platform of Azure SQL Managed Instance, no issues are listed.](media/dma-feature-parity-sql-mia.png)

   ![For a target platform of Azure SQL Managed Instance, a message that full-text search has changed, and the list of impacted objects are listed.](media/Sql-compatability2a.png)


10. The database, including the Service Broker feature, can be migrated as is, providing an opportunity for WWI to have a fully managed PaaS database running in Azure. Previously, their only option for migrating a database using features incompatible with Azure SQL Database, such as Service Broker, was to deploy the database to a virtual machine running in Azure (IaaS) or modify the database and associated applications to remove the use of the unsupported features. The introduction of Azure SQL MI, however, provides the ability to migrate databases into a managed Azure SQL database service with _near 100% compatibility_, including the features that prevented them from using Azure SQL Database.

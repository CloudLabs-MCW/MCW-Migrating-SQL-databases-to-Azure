## Exercise 1: Improve database security posture with Data Discovery and Classification and Azure Defender for SQL

Duration: 30 minutes

In this exercise, you are able to set up some of the advanced security features of SQL MI and explore some of the security benefits that come with running your database in Azure. [Azure Defender for SQL](https://docs.microsoft.com/azure/azure-sql/database/azure-defender-for-sql) provides advanced SQL security capabilities, including functionality for surfacing and mitigating potential database vulnerabilities and detecting anomalous activities that could indicate a threat to your database. Also, [Data Discovery and Classification](https://docs.microsoft.com/azure/azure-sql/database/data-discovery-and-classification-overview) allows you to discover and classify sensitive data within the database.

### Task 1: Configure Data Discovery and Classification

In this task, you review the [Data Discovery and Classification](https://docs.microsoft.com/azure/azure-sql/database/data-discovery-and-classification-overview) feature of Azure SQL. Data Discovery & Classification introduces a new tool for discovering, classifying, labelling, and reporting the sensitive data in your databases. It introduces a set of advanced services, forming a new SQL Information Protection paradigm aimed at protecting the data in your database, not just the database. Discovering and classifying your most sensitive data (e.g., business, financial, healthcare) can play a pivotal role in your organizational information protection stature.

1. Navigate to the Azure portal: [https://portal.azure.com](https://portal.azure.com)

1. On the **Resource groups** page, select the resource group **SQLMI-shared-RG (1)** from the list.

   ![](media/gt-sql-l2-3.png)

1. On the **SQLMI-shared-RG** resource group page, select the SQL Managed Instance named **sqlmi--cus (1)** from the list of resources.

   ![](media/gt-sql-l2-4.png)

1. On the **Overview (1)** page of the SQL Managed Instance, select the managed database **WideWorldImporters (2)**.

   ![](media/gt-sql-l2-5.png)

1. On the **WideWorldImporters{suffix}** Managed database blade, select **Data Discovery & Classification** from the left-hand menu.

   ![The Data Discovery & Classification tile is displayed.](media/ex9-task1-step2.png "Data Discovery & Classification Dashboard")

1. In the **Data Discovery & Classification** blade, select the info link with the message **Currently database is using SQL Information Protection policy. Found 35 columns with classification recommendations**.

   ![The recommendations link on the Data Discovery & Classification blade is highlighted.](media/datamod12.png "Data Discovery & Classification")

1. Look over the list of recommendations to get a better understanding of the types of data and classifications that can be assigned, based on the built-in classification settings. In the list of classification recommendations, select the recommendation for the **Sales - CreditCard - CardNumber** field.

   ![](media/gt-sql-l2-6.png)

1. Due to the risk of exposing credit card information, WWI would like a way to classify it as highly confidential, not just **Confidential**, as the recommendation suggests. To correct this, select **+ Add classification** at the top of the Data Discovery & Classification blade.

   ![The +Add classification button is highlighted in the toolbar.](media/ex9-task1-step5.png "Data Discovery & Classification")

1. Quickly expand the **Sensitivity label** field and review the various built-in labels from which you can choose. You can also add custom labels, should you desire.

   ![The list of built-in Sensitivity labels is displayed.](media/ads-data-discovery-and-classification-sensitivity-labels.png "Data Discovery & Classification")

1. In the Add classification dialog, enter the following:

   - **Schema name**: Select **Sales (1)**.
   - **Table name**: Select **CreditCard (2)**.
   - **Column name**: Select **CardNumber (nvarchar) (3)**.
   - **Information type**: Select **Credit Card (4)**.
   - **Sensitivity level**: Select **Highly Confidential (5)**.
   - clcik **Add classification (6)**.

      ![](media/gt-sql-l2-7.png)

1. Notice that the **Sales - CreditCard - CardNumber** field disappears from the recommendations list, and the number of recommendations drops by 1.

1. Select **Save** on the toolbar of the Data Classification window. It may take several minutes for the save to complete.

   ![](media/gt-sql-l2-8.png)

1. Other recommendations you can review are the **HumanResources - Employee** fields for **NationIDNumber** and **BirthDate**. Note that the recommendation service flagged these fields as **Confidential - GDPR**. WWI maintains data about gamers from around the world, including Europe, so having a tool that helps them discover data that may be relevant to GDPR compliance is very helpful.

   ![GDPR information is highlighted in the list of recommendations](media/ads-data-discovery-and-classification-recommendations-gdpr.png "Data Discovery & Classification")

1. Check the **Select all (1)** checkbox at the top of the list to select all the remaining recommended classifications, and then select **Accept selected recommendations (2)**.

   ![](media/gt-sql-l2-9.png)

1. Select **Save** on the toolbar of the Data Classification window. It may take several minutes for the save to complete.

   ![](media/gt-sql-l2-10.png)

1. When the save completes, select the **Overview** tab on the Data Discovery & Classification blade to view a report with a full summary of the database classification state.

   ![](media/gt-sql-l2-11.png)

> **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
- If you receive a success message, you can proceed to the next task.
- If not, carefully read the error message and retry the step, following the instructions in the lab guide.
- If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.

<validation step="6f40a664-2ac0-4d45-ad5a-5e00a2153e26" />

### Task 2: Review an Azure Defender for SQL Vulnerability Assessment

In this task, you review an assessment report generated by Azure Defender for the `WideWorldImporters` database and take action to remediate one of the findings in the `WideWorldImporters` database. The [SQL Vulnerability Assessment service](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) is a service that provides visibility into your security state and includes actionable steps to resolve security issues and enhance your database security.

1. On the **Microsoft Defender for Cloud (1)** blade of the **WideWorldImporters{suffix}** managed database, click **View additional findings in Vulnerability Assessment (2)** to open the Vulnerability Assessment blade.

   ![](media/gt-sql-l2-12.png)

   > **Note**: If you see Microsoft Defender for SQL is not enabled click on the **Enable** button, wait till it gets Succeeded and perform the step 2.

      ![The Vulnerability tile is displayed.](media/Microsoft-Defender-for-Cloud1.png "Azure Defender for SQL Vulnerability Assessment tile")
      
1. On the Vulnerability Assessment blade, select **Scan** on the toolbar.

   ![](media/gt-sql-l2-13.png)

   > **Note**: If you encounter an error "Failed to execute Vulnerability Assessment scan for **WideWorldImporters{suffix}**. Error message: The configured storage account was not found in the subscriptions", perform the following steps.

   - Move back to the **Microsoft Defender for Cloud** blade.

   -  Once you are in the **Microsoft Defender for Cloud** blade, click on **Configure** of the Enablement Status: Enabled at the subscription level.

      ![The Vulnerability assessment scan button is selected in the toolbar.](media/microsoft-defender-configure.png "microsoft-defender-configure")

   - On the **Server Settings** blade, click on **Select Storage acount** under Storage account.

      ![The Vulnerability assessment scan button is selected in the toolbar.](media/server-setting-storage-account.png "server-setting-storage-account")

   - On **Choose storage account** blade, select the storage account **sqlmistore<inject key="SUFFIX" enableCopy="false"/>**.

      ![The Vulnerability assessment scan button is selected in the toolbar.](media/choose-storage-account.png "choose-storage-account")
      
   - On the **Server Settings** blade, click on **Save**.

      ![The Vulnerability assessment scan button is selected in the toolbar.](media/server-setting-save.png "server-setting-save")

   - Re-perform the **steps 1 and 2**.
 
1. When the scan completes, a dashboard displaying the number of failing and passing checks, along with a breakdown of the risk summary by severity level is displayed.

   ![The Vulnerability Assessment dashboard is displayed.](media/sql-mi-vulnerability-assessment-dashboard1.png "Vulnerability Assessment dashboard")

1. In the scan results, take a few minutes to browse both the Failed and Passed checks, and review the types of checks that are performed. In the **Unhealthy** list, locate the security check for **Transparent data encryption**. This check has an ID of **VA1219**.

   ![The VA1219 finding for Transparent data encryption is highlighted.](media/sql-mi-vulnerability-assessment-failed-va1219.png "Vulnerability assessment")

1. Select the **VA1219** finding to view the detailed description.

   ![The details of the VA1219 - Transparent data encryption should be enabled finding are displayed with the description, impact, and remediation fields highlighted.](media/sql-mi-vulnerability-assessment-failed-va1219-details1.png "Vulnerability Assessment")

   > The details for each finding provide more insight into the reason for the finding. Of note are fields describing the finding, the impact of the recommended settings, and details on remediation for the finding.

1. You will now act on the recommended remediation steps for the finding and enable [Transparent Data Encryption](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview?tabs=azure-portal) for the `WideWorldImporters` database. To accomplish this, switch over to using SSMS on your JumpBox VM for the next few steps.

   > **Note**: Transparent data encryption (TDE) needs to be manually enabled for Azure SQL Managed Instance. TDE helps protect Azure SQL Database, Azure SQL Managed Instance, and Azure Data Warehouse against the threat of malicious activity. It performs real-time encryption and decryption of the database, associated backups, and transaction log files at rest without requiring changes to the application.

1. On the Azure portal home page (`https://portal.azure.com`), in the **Search resources, services, and docs (1)** box at the top, type **Virtual machines** and select **Virtual machines (2)** from the search results.

   ![](media/gt-sql-l2-26.png)

1. On the **Virtual machines** page, verify that your virtual machines are listed under the **Azure Labs (1)** subscription. This is your default subscription and should be used throughout the lab whenever a subscription field is required.

   ![](media/gt-sql-l2-27.png)

1. Select the **Azure Cloud Shell** icon from the top menu.

   ![](media/gt-sql-l2-14.png)

1. In the Cloud Shell window that opens at the bottom of your browser window, select **PowerShell**.

   ![](media/gt-sql-l2-15.png)

1. On the Getting Started , Choose **mount a storage account (1)** select the **exisitng subscription (2)** then click on **Apply (3)**.

   ![](media/gt-sql-l2-16.png)

1. Choose **I want to create a storage account (1)** , Click on **Next (2)**.

   ![](media/gt-sql-l2-17.png)


1. If prompted about not having a storage account mounted, click on **Show advanced settings**. Select Create New under Storage account and provide values as below: 
  
      - **Resource Group**: Select **Use existing** then <inject key="Resource Group Name" enableCopy="false"/> **(1)**
      - **Region**: **Central US (2)**
      - **Storage account name**: **storage<inject key="Suffix" enableCopy="false"/> (3)**
      - **File Share**: **blob (4)**
      - Click **Create (5)**

         ![](media/gt-sql-l2-18.png)

1. After a moment, a message is displayed that you have successfully requested a Cloud Shell, and you are presented with a PS Azure prompt.

   ![In the Azure Cloud Shell dialog, a message is displayed that requesting a Cloud Shell succeeded, and the PS Azure prompt is displayed.](media/cloud-shell-ps-azure-prompt.png "Azure Cloud Shell")

1. In the Cloud Shell, set your default subscription to the same **Azure Labs subscription** that you verified earlier with your virtual machines by running the following command:

   ```PowerShell
   az account set --subscription "<your-subscription-name>"
   ```

   ![](media/gt-sql-l2-20.png)

1. At the prompt, retrieve information about SQL MI in the SQLMI-Shared-RG resource group by entering the following PowerShell command.

   ```PowerShell
   $resourceGroup = "SQLMI-Shared-RG"
   az sql mi list --resource-group $resourceGroup
   ```

   ![](media/gt-sql-l2-21.png)

   > **Note**: If you have multiple Azure subscriptions, and the account you are using for this hands-on lab is not your default account, you may need to run the `az account list --output table` at the Azure Cloud Shell prompt to output a list of your subscriptions. Copy the Subscription ID of the account you are using for this lab and then run `az account set --subscription <your-subscription-id>` to set the appropriate account for the Azure CLI commands.

1. Within the above command's output, locate and copy the value of the `fullyQualifiedDomainName` property. Paste the value into a text editor, such as Notepad.exe, for reference below.

   ![](media/gt-sql-l2-22.png)

1. Search for **Resource Group** and select it.

   ![Resource groups is highlighted in the Azure services list.](media/datamod13.png "Azure services")

1. Select the **<inject key="Resource Group Name" enableCopy="false"/>** resource group from the list.

   ![Resource groups is selected in the Azure navigation pane, and the "hands-on-lab resource group is highlighted.](./media/resource-groups1.png "Resource groups list")

1. In the list of resources for your resource group, select the **<inject key="SQLVM Name" enableCopy="false"/>** Virtual Machine.

   ![The SqlServer2008 VM is highlighted in the list of resources.](https://raw.githubusercontent.com/CloudLabs-MCW/MCW-Migrating-SQL-databases-to-Azure/fix/Hands-on%20lab/media/images/vmrg.png "Resource list")

1. From the overview page of  the **<inject key="SQLVM Name" enableCopy="false"/>** VM, select **Connect**.

   ![The Passed tab is highlighted, and VA1219 is entered into the search filter. VA1219 with a status of PASS is highlighted in the results.](media/datamod18.png "Passed")

1. On the **sql2008-<inject key="Suffix" enableCopy="false"/> | Connect** page, click on **Download RDP file (2)**. 
  
   ![The Passed tab is highlighted, and VA1219 is entered into the search filter. VA1219 with a status of PASS is highlighted in the results.](media/E1T1S6-1809.png "Passed")

1. Click on **Keep**, on the Downloads pop-up. 

   ![The Passed tab is highlighted, and VA1219 is entered into the search filter. VA1219 with a status of PASS is highlighted in the results.](media/datamod15.png "Passed")

1. Click on **Open file**.

   ![](media/datamod16.png)

1. On the RDP pop-up, **check (1)** "I understand and want to allow RDP files to open on this device for my account" and then click **OK (2)**.

    ![](media/E1T1S8-0701-1.png)

1. Next, on RDP tab, ensure that the **Clipboard** option is enabled, and then select **Connect**.

   ![The Passed tab is highlighted, and VA1219 is entered into the search filter. VA1219 with a status of PASS is highlighted in the results.](media/datamod17.png "Passed")

1. Enter the following credentials when prompted, and then select **OK**:

   - **Username**: `sqlmiuser`
   - **Password**: `Password.1234567890`

      ![The credentials specified above are entered into the Enter your credentials dialog.](media/rdc-credentials-sql-2008.png "Enter your credentials")

1. Select **Yes** to connect if prompted that the remote computer's identity cannot be verified.

   ![In the Remote Desktop Connection dialog box, a warning states that the remote computer's identity cannot be verified and asks if you want to continue anyway. At the bottom, the Yes button is circled.](./media/remote-desktop-connection-identity-verification-sqlserver2008.png "Remote Desktop Connection dialog")

1. On your **sql2008-<inject key="Suffix" enableCopy="false"/>**, open **Microsoft SQL Server Management Studio 17** from the Start menu.

   ![](media/gt-sql-l2-23.png)

1. Enter the following information in the **Connect to Server** dialogue and click on **Connect** **(6)**.

   - **Server name** **(1)**: Enter the fully qualified domain name of your SQL-managed instance, which you copied from the Azure Cloud Shell in a previous task.
   - **Authentication** **(2)**: Select **SQL Server Authentication**.
   - **Login** **(3)**: Enter `contosoadmin`
   - **Password** **(4)**: Enter `IAE5fAijit0w^rDM`
   - Check the **Remember password** **(5)** box.

      ![The SQL managed instance details specified above are entered into the Connect to Server dialog.](media/data-migration-09.png "Connect to Server")

1. In SSMS, select **New Query** from the toolbar and paste the following SQL script into the new query window.

   > **Note**: Make sure to replace the **{Managed-database-Name}** Lab 1 Data Modernization: Migrate SQL DB to Azure SQL MI name start like **WideWorldImporters<inject key="Suffix" enableCopy="false"/>**.

   ```SQL
   USE {Managed-database-Name};
   GO

   ALTER DATABASE [{Managed-database-Name}] SET ENCRYPTION ON
   ```

   >**Note:** You turn transparent data encryption on and off on the database level. To enable transparent data encryption on a database in Azure SQL Managed Instance use must use T-SQL.

      ![The query above is pasted into a new query window in SSMS.](media/create-quary.png "New query")

1. Select **Execute** from the SSMS toolbar. After a few seconds, you will see a message that **Commands completed successfully**. 

    ![The query above is pasted into a new query window in SSMS.](media/create-quary-execute.png "New query")

1. You can verify the encryption state and view information on the associated encryption keys by using the [sys.dm_database_encryption_keys view](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql). Select **New Query** on the SSMS toolbar again, and paste the following query into the new query window:

   ```SQL
   SELECT * FROM sys.dm_database_encryption_keys
   ```

   ![The query above is pasted into a new query window in SSMS.](media/ssms-sql-mi-database-encryption-keys.png "New query")

1. Select **Execute** from the SSMS toolbar. You will see two records in the Results window, which provide information about the encryption state and keys used for encryption.

   ![The Execute button on the SSMS toolbar is highlighted, and in the Results pane the two records about the encryption state and keys for the WideWorldImporters database are highlighted.](media/ssms-sql-mi-database-encryption-keys-results.png "Results")

   > **Note:** By default, service-managed transparent data encryption is used. A transparent data encryption certificate is automatically generated for the server that contains the database.

1. Return to the **lab VM**. Open Azure portal

1. Search and select WideWorldImporters<inject key="Suffix" enableCopy="false"/> managed database.

   ![](media/1.png)

1. From the left navigation select **Microsoft Defender for Cloud (1)**. Select **View additional findings in Vulnerability Assessment (2)**.

   ![](media/2.png)

1. On the toolbar, select **Scan** to start a new assessment of the database.

   ![The Vulnerability assessment scan button is selected in the toolbar.](media/scan.png "Scan")

1. When the scan completes, select the **Findings** tab, enter **VA1219** into the search filter box, and observe that the previous failure is no longer in the findings list.

   ![The Findings tab is highlighted, and VA1219 is entered into the search filter. The list displays no results.](media/sql-mi-vulnerability-assessment-failed-filter-va1219-1.png "Scan Findings List")

1. Now, select the **Passed** tab, and observe the **VA1219** check is listed with a status of **PASS**.

   ![The Passed tab is highlighted, and VA1219 is entered into the search filter. VA1219 with a status of PASS is highlighted in the results.](media/sql-mi-vulnerability-assessment-passed-va1219-1.png "Passed")

   >**Note:** Using the SQL Vulnerability Assessment, it is simple to identify and remediate potential database vulnerabilities, allowing you to improve your database security proactively.

> **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
- If you receive a success message, you can proceed to the next task.
- If not, carefully read the error message and retry the step, following the instructions in the lab guide.
- If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.

<validation step="362d6b88-28af-412e-86ef-5b42ee3f92ff" />

## Summary
By completing this lab, you have successfully enhanced the security posture of your SQL Managed Instance by classifying sensitive data and identifying/remediating potential vulnerabilities. These steps are crucial for protecting data and ensuring compliance with regulations like GDPR

### You have successfully completed the exercise. Now click on **Next >>** from the lower right corner to move on to the next exercise.

![](./media/next2.png)

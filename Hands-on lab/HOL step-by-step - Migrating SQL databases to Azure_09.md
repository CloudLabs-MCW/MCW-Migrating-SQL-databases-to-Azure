# Exercise 6: Improve database security posture with Data Discovery and Classification and Microsoft Defender for SQL

### Estimated Duration: 30 minutes

## Scenario

Contoso wants to strengthen the security and compliance posture of its newly migrated **Azure SQL Managed Instance** environment by identifying sensitive data and addressing potential database vulnerabilities. 

As a security administrator, you will use **Data Discovery & Classification** to classify confidential information such as credit card details and personal data, while leveraging **Microsoft Defender for SQL** to perform vulnerability assessments and remediate security risks. You will also enable advanced protection features like **Transparent Data Encryption (TDE)** to help secure data at rest and improve overall database security.

## Overview

In this lab, you'll set up some of the advanced security features of SQL MI and explore some of the security benefits that come with running your database in Azure. [Microsoft Defender for SQL](https://docs.microsoft.com/azure/azure-sql/database/azure-defender-for-sql) provides advanced SQL security capabilities, including functionality for surfacing and mitigating potential database vulnerabilities and detecting anomalous activities that could indicate a threat to your database. Also, [Data Discovery and Classification](https://docs.microsoft.com/azure/azure-sql/database/data-discovery-and-classification-overview) allows you to discover and classify sensitive data within the database.

## Lab Objectives

In this lab, you will perform the following:

- Task 1: Configure Data Discovery and Classification
- Task 2: Review an Microsoft Defender for SQL Vulnerability Assessment

### Task 1: Configure Data Discovery and Classification

In this task, you review the [Data Discovery and Classification](https://docs.microsoft.com/azure/azure-sql/database/data-discovery-and-classification-overview) feature of Azure SQL. Data Discovery & Classification introduces a new tool for discovering, classifying, labelling, and reporting the sensitive data in your databases. It introduces a set of advanced services, forming a new SQL Information Protection paradigm aimed at protecting the data in your database, not just the database. Discovering and classifying your most sensitive data (e.g., business, financial, healthcare) can play a pivotal role in your organizational information protection stature.

1. Navigate to **SQLMI-Shared-RG** resource group and select the SQL Managed instance named **sqlmi--cus**. Now, from the **Overview** tab search and select the Managed database named **WideWorldImporters<inject key="Suffix" enableCopy="false"/>**.

    ![](media/New-image62.png)
   
1. On the **WideWorldImporters<inject key="Suffix" enableCopy="false"/>** Managed database blade, select **Data Discovery & Classification** from the left-hand menu.

   ![](media/new-image64.png)

1. In the **Data Discovery & Classification** blade, select the info link with the message **Currently database is using SQL Information Protection policy. Found 35 columns with classification recommendations**.

   ![](media/new-image65.png)

1. Look over the list of recommendations to get a better understanding of the types of data and classifications that can be assigned, based on the built-in classification settings. In the list of classification recommendations, select the recommendation for the **Sales - CreditCard - CardNumber** field.

   ![The CreditCard number recommendation is highlighted in the recommendations list.](media/ads-data-discovery-and-classification-recommendations-credit-card.png "Data Discovery & Classification")

1. Due to the risk of exposing credit card information, WWI would like a way to classify it as highly confidential, not just **Confidential**, as the recommendation suggests. To correct this, select **+ Add classification** at the top of the Data Discovery & Classification blade.

   ![](media/new-image66.png)

1. Expand the **Sensitivity label** field and review the various built-in labels from which you can choose. You can also add custom labels, should you desire.

   ![](media/new-image67.png)

1. In the Add classification dialog, enter the following and then select **Add classification**.

   - **Schema name**: Select **Sales**.
   - **Table name**: Select **CreditCard**.
   - **Column name**: Select **CardNumber (nvarchar)**.
   - **Information type**: Select **Credit Card**.
   - **Sensitivity level**: Select **Highly Confidential**.

      ![](media/new-image68.png)

1. Notice that the **Sales - CreditCard - CardNumber** field disappears from the recommendations list, and the number of recommendations drops by 1.

1. Select **Save** on the toolbar of the Data Classification window.

   ![](media/new-image69.png)

1. Other recommendations you can review are the **HumanResources - Employee** fields for **NationIDNumber** and **BirthDate**. Note that the recommendation service flagged these fields as **Confidential - GDPR**. WWI maintains data about gamers from around the world, including Europe, so having a tool that helps them discover data that may be relevant to GDPR compliance is very helpful.

    ![GDPR information is highlighted in the list of recommendations](media/ads-data-discovery-and-classification-recommendations-gdpr.png "Data Discovery & Classification")

1. Check the **Select all** checkbox at the top of the list to select all the remaining recommended classifications, and then select **Accept selected recommendations**.

    ![](media/new-image70.png)

1. Select **Save** on the toolbar of the Data Classification window. It may take several minutes for the save to complete.

    ![Save the updates to the classified columns list.](media/ads-data-discovery-and-classification-save.png "Save")

1. When the save completes, select the **Overview** tab on the Data Discovery & Classification blade to view a report with a full summary of the database classification state.

    ![The View Report button is highlighted on the toolbar.](media/ads-data-discovery-and-classification-overview-report.png "View report")

    > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
    - If you receive a success message, you can proceed to the next task.
    - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
    - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.
    
<validation step="3a15677e-379c-4e25-8d84-76f18b49edcc" />

### Task 2: Review an Microsoft Defender for SQL Vulnerability Assessment

In this task, you review an assessment report generated by Microsoft Defender for the `WideWorldImporters` database and take action to remediate one of the findings in the `WideWorldImporters` database. The [SQL Vulnerability Assessment service](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) is a service that provides visibility into your security state and includes actionable steps to resolve security issues and enhance your database security.

1. Select **Microsoft Defender for Cloud** from the left hand navigation menu of the **WideWorldImporters<inject key="Suffix" />** Managed database.

    ![](media/new-image71.png)
   
1. On the **Microsoft Defender for Cloud** blade for the **WideWorldImporters<inject key="Suffix" />** Managed database, Scroll down and click on **View additional findings in Vulnerability Assessment** to open the Vulnerability Assessment blade.

   ![](media/new-image72.png)

   > **Note**: If you see Microsoft Defender for SQL is not enabled click on the **Enable** button, wait till it gets Succeeded and perform the step 2.

      ![The Vulnerability tile is displayed.](media/Microsoft-Defender-for-Cloud1.png "Azure Defender for SQL Vulnerability Assessment tile")
      
1. On the Vulnerability Assessment blade, select **Scan** on the toolbar.

    ![](media/new-image73.png)

    > **Note**: If you encounter an error "Failed to execute Vulnerability Assessment scan for **WideWorldImporters<inject key="Suffix" enableCopy="false"/>**. Error message: The configured storage account was not found in the subscriptions", perform the following steps.

    - Move back to the **Microsoft Defender for Cloud** blade.

    -  Once you are in the **Microsoft Defender for Cloud** blade, click on **Configure** of the Enablement Status: Enabled at the subscription level.

        ![](media/new-image74.png)

    - On the **Server Settings** blade, click on **Select Storage acount** under Storage account.

       ![The Vulnerability assessment scan button is selected in the toolbar.](media/server-setting-storage-account.png "server-setting-storage-account")

    - On **Choose storage account** blade, select the storage account **sqlmistore<inject key="SUFFIX" enableCopy="false"/>**.

       ![The Vulnerability assessment scan button is selected in the toolbar.](media/choose-storage-account.png "choose-storage-account")
      
    - On the **Server Settings** blade, click on **Save**.

       ![The Vulnerability assessment scan button is selected in the toolbar.](media/server-setting-save.png "server-setting-save")

   - Re-perform the steps 2 and 3.
 
1. When the scan completes, a dashboard displaying the number of failing and passing checks, along with a breakdown of the risk summary by severity level is displayed.

   ![The Vulnerability Assessment dashboard is displayed.](media/sql-mi-vulnerability-assessment-dashboard1.png "Vulnerability Assessment dashboard")

1. In the scan results, take a few minutes to browse both the Failed and Passed checks, and review the types of checks that are performed. In the **Unhealthy** list, locate the security check for **Transparent data encryption**. This check has an ID of **VA1219**.

   ![The VA1219 finding for Transparent data encryption is highlighted.](media/sql-mi-vulnerability-assessment-failed-va1219.png "Vulnerability assessment")

   >**Note**: If changes do not appear immediately, kindly refresh the page.

1. Select the **VA1219** finding to view the detailed description.

   ![The details of the VA1219 - Transparent data encryption should be enabled finding are displayed with the description, impact, and remediation fields highlighted.](media/sql-mi-vulnerability-assessment-failed-va1219-details1.png "Vulnerability Assessment")

   > The details for each finding provide more insight into the reason for the finding. Of note are fields describing the finding, the impact of the recommended settings, and details on remediation for the finding.

1. You will now act on the recommended remediation steps for the finding and enable [Transparent Data Encryption](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview?tabs=azure-portal) for the `WideWorldImporters` database. To accomplish this, switch over to using SSMS on your JumpBox VM for the next few steps.

     > **Note**: Transparent data encryption (TDE) needs to be manually enabled for Azure SQL Managed Instance. TDE helps protect Azure SQL Database, Azure SQL Managed Instance, and Azure Data Warehouse against the threat of malicious activity. It performs real-time encryption and decryption of the database, associated backups, and transaction log files at rest without requiring changes to the application.

1. Navigate to **sql2022-<inject key="Suffix" enableCopy="false"/>** Virtual Machine

1. Back to **SQL Server Management Studio 20** from the Start menu, enter the following information in the **Connect to Server** dialogue and click on **Connect(6)**.

   >**Note**: If you have not connected to **Microsoft SQL Server Management Studio 17** in the previous step, follow the steps below. Otherwise, proceed to the next 
      step.

   - **Server name** **(1)**: Enter the fully qualified domain name of your SQL-managed instance, which you copied from the Azure Cloud Shell in a previous task.
   - **Authentication** **(2)**: Select **SQL Server Authentication**.
   - **Login** **(3)**: Enter `contosoadmin`
   - **Password** **(4)**: Enter `IAE5fAijit0w^rDM`
   - Check the **Remember password** **(5)** box.

      ![](media/E6T2S9-1902.png)

1. In SSMS, select **New Query** from the toolbar and paste the following SQL script into the new query window.

   > **Note**: Make sure to replace the **{Managed-database-Name}** with **WideWorldImporters<inject key="Suffix" enableCopy="false"/>**.

   ```SQL
   USE {Managed-database-Name};
   GO

   ALTER DATABASE [{Managed-database-Name}] SET ENCRYPTION ON
   ```

   > You turn transparent data encryption on and off on the database level. To enable transparent data encryption on a database in Azure SQL Managed Instance use must use T-SQL.

      ![The query above is pasted into a new query window in SSMS.](media/create-quary.png "New query")

1. Select **Execute** from the SSMS toolbar. After a few seconds, you will see a message that **Commands completed successfully**. 

    ![The query above is pasted into a new query window in SSMS.](media/create-quary-execute.png "New query")

10. You can verify the encryption state and view information on the associated encryption keys by using the [sys.dm_database_encryption_keys view](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql). Select **New Query** on the SSMS toolbar again, and paste the following query into the new query window:

    ```SQL
    SELECT * FROM sys.dm_database_encryption_keys
    ```

    ![The query above is pasted into a new query window in SSMS.](media/ssms-sql-mi-database-encryption-keys.png "New query")

1. Select **Execute** from the SSMS toolbar. You will see two records in the Results window, which provide information about the encryption state and keys used for encryption.

    ![The Execute button on the SSMS toolbar is highlighted, and in the Results pane the two records about the encryption state and keys for the WideWorldImporters database are highlighted.](media/ssms-sql-mi-database-encryption-keys-results.png "Results")

    > By default, service-managed transparent data encryption is used. A transparent data encryption certificate is automatically generated for the server that contains the database.

1. Return to the Azure portal and the Azure Defender for SQL's Vulnerability Assessment blade of the `WideWorldImportersSUFFIX` managed database. On the toolbar, select **Scan** to start a new assessment of the database.

    ![The Vulnerability assessment scan button is selected in the toolbar.](media/scan.png "Scan")

1. When the scan completes, select the **Findings** tab, enter **VA1219** into the search filter box, and observe that the previous failure is no longer in the findings list.

    ![The Findings tab is highlighted, and VA1219 is entered into the search filter. The list displays no results.](media/sql-mi-vulnerability-assessment-failed-filter-va1219-1.png "Scan Findings List")

1. Now, select the **Passed** tab, and observe the **VA1219** check is listed with a **Healthy** status.

    ![The Passed tab is highlighted, and VA1219 is entered into the search filter. VA1219 with a status of PASS is highlighted in the results.](media/sql-mi-vulnerability-assessment-passed-va1219-1.png "Passed")

    > Using the SQL Vulnerability Assessment, it is simple to identify and remediate potential database vulnerabilities, allowing you to improve your database security proactively.

    > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
    - If you receive a success message, you can proceed to the next exercise.
    - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
    - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.
    
        <validation step="e183c712-83ca-4351-9d5f-d3a8174f7a62" />

## Summary

In this lab, you have configured Data Discovery and Classification and reviewed the Microsoft Defender for SQL Vulnerability Assessment.

You have successfully completed the exercise. Click **Next >>** to continue to the next exercise.

![](media/ns7.png)

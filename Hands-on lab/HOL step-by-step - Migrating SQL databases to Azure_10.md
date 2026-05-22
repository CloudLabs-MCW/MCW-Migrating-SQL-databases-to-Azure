# Exercise 7: Enable Dynamic Data Masking

### Estimated Duration: 25 minutes

## Scenario

Contoso wants to further protect sensitive customer information stored in the **WideWorldImporters** database by limiting the exposure of confidential data to non-privileged users.

As a database administrator, you will implement **Dynamic Data Masking (DDM)** to hide sensitive fields such as credit card numbers and email addresses without modifying the actual data stored in the database. You will then validate that authorized users can view the original data while standard users only see masked values, improving the overall security posture of the SQL Managed Instance environment.

## Overview

In this lab, you'll work on enabling Dynamic Data Masking (DDM) to enhance the security of sensitive data in the WideWorldImporters database. You'll begin by applying DDM to mask credit card numbers in the Sales.CreditCard table, ensuring that only the last four digits of the card numbers are visible to non-privileged users. Then, you'll extend this approach to protect email addresses in the LoginEmail field of the dbo.Gamer table by partially masking them. This exercise demonstrates how DDM helps limit sensitive data exposure without altering the underlying data in the database.

## Lab Objective

In this lab, you will perform the following:

- Task 1: Enable DDM on credit card numbers
- Task 2: Apply DDM to email addresses

### Task 1: Enable DDM on credit card numbers

In this task, the data in the `WideWorldImporters` database using the ADS Data Discovery & Classification tool, you set the Sensitivity label for credit card numbers to Highly Confidential. In this task, you take another step to protect this information by enabling DDM on the `CardNumber` field in the `CreditCard` table. DDM prevents queries against that table from returning the full credit card number.

1. On your sql2022-<inject key="Suffix" enableCopy="false"/> VM, return to the SQL Server Management Studio (SSMS) window you opened previously.

2. Expand **Database** > **WideWorldImporters<inject key="Suffix" enableCopy="false"/>**.

3. Expand **Tables** under the **WideWorldImporters<inject key="Suffix" enableCopy="false"/>** database and locate the `Sales.CreditCard` table. Expand the table 
   columns and observe that there is a column named `CardNumber`. Right-click the table, and choose **Select Top 1000 Rows** from the context menu.

     ![The Select Top 1000 Rows item is highlighted in the context menu for the Sales.CreditCard table.](media/ssms-sql-mi-credit-card-table-select.png)

4. In the query window that opens review the Results, including the `CardNumber` field. Notice it is displayed in plain text, making the data available to anyone with access to query the database.

   ![Plain text credit card numbers are highlighted in the query results.](media/ssms-sql-mi-credit-card-table-select-results.png "Results")

5. To be able to test the mask being applied to the `CardNumber` field, you first create a user in the database to use for testing the masked field. In SSMS, In the same **Query** window replace the script with the following script:

   ```SQL
   CREATE USER DDMUser WITHOUT LOGIN;
   GRANT SELECT ON [Sales].[CreditCard] TO DDMUser;
   ```

   > The SQL script above creates a new user in the database named `DDMUser` and grants that user `SELECT` rights on the `Sales.CreditCard` table.

6. Select **Execute** from the SSMS toolbar to run the query. You will get a message that the commands completed successfully in the Messages pane.

7. With the new user created, run a quick query to observe the results. In the same **Query** window replace the script with the following script:

   ```SQL
   EXECUTE AS USER = 'DDMUser';
   SELECT TOP 10 * FROM [Sales].[CreditCard];
   REVERT;
   ```

8. Select **Execute** from the toolbar and examine the Results pane. Notice the credit card number, as above, is visible in plain text.

   ![The credit card number is unmasked in the query results.](media/ssms-sql-mi-ddm-results-unmasked.png "Query results")

9. You now apply DDM on the `CardNumber` field to prevent it from being viewed in query results. In the same **Query** window replace the script with the following script to apply a mask to the `CardNumber` field and then select **Execute**.

   ```SQL
   ALTER TABLE [Sales].[CreditCard]
   ALTER COLUMN [CardNumber] NVARCHAR(25) MASKED WITH (FUNCTION = 'partial(0,"xxx-xxx-xxx-",4)')
   ```

10. Run the `SELECT` in the same **Query** window again replace the query with the below query, and observe the results. Specifically, inspect the output in the 
    `CardNumber` field. For reference, the query is below.

    ```SQL
    EXECUTE AS USER = 'DDMUser';
    SELECT TOP 10 * FROM [Sales].[CreditCard];
    REVERT;
    ```

    ![The credit card number is masked in the query results.](media/ssms-sql-mi-ddm-results-masked.png "Query results")

    > The `CardNumber` is now displayed using the mask applied to it, so only the card number's last four digits are visible. Dynamic Data Masking is a powerful feature that enables you to prevent unauthorized users from viewing sensitive or restricted information. It's a policy-based security feature that hides the sensitive data in the result set of a query over designated database fields while the data in the database is not changed.

### Task 2: Apply DDM to email addresses

In this task, you use one of the built-in functions for making email addresses using DDM to help protect this information. From the findings of the Data Discovery & Classification report in ADS, you saw that email addresses are labelled Confidential

1. For this, you will target the `LoginEmail` field in the `[dbo].[Gamer]` table. In the same **Query** window replace the existing script with the following and select 
   **Execute**:

   ```SQL
   SELECT TOP 10 * FROM [dbo].[Gamer]
   ```

   ![In the query results, full email addresses are visible.](media/ddm-select-gamer-results.png "Query results")

2. Now, as you did above, grant the `DDMUser` `SELECT` rights on the [dbo].[Gamer]. In the same **Query** window replace the existing script with the following script, 
   and then select **Execute**:

   ```SQL
   GRANT SELECT ON [dbo].[Gamer] to DDMUser;
   ```

3. Next, apply DDM on the `LoginEmail` field to prevent it from being viewed in full in query results. In the same **Query** window replace the script with the following 
   script to apply a mask to the `LoginEmail` field, and then select **Execute**.

   ```SQL
   ALTER TABLE [dbo].[Gamer]
   ALTER COLUMN [LoginEmail] NVARCHAR(250) MASKED WITH (FUNCTION = 'Email()');
   ```

   > **Note**: Observe the use of the built-in `Email()` masking function above. This masking function is one of several pre-defined masks available in SQL Server databases.

4. Run the `SELECT` query below and examine the results. Specifically, inspect the output in the LoginEmail field. The email addresses should now appear partially masked, ensuring the protection of confidential information.

   ```SQL
   EXECUTE AS USER = 'DDMUser';
   SELECT TOP 10 * FROM [dbo].[Gamer];
   REVERT;
   ```

   ![The email addresses are masked in the query results.](media/ddm-select-gamer-results-masked.png "Query results")

   > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
   - If the validation fails, carefully read the error message and retry the step, following the instructions in the lab guide.
   - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.
    
      <validation step="1b4769fd-cfc8-47f0-b2a1-8f1790f17adc" />

You've successfully applied Dynamic Data Masking to both the **'CardNumber'** field in the **'Sales.CreditCard'** table and the **'LoginEmail'** field in the **'dbo.Gamer'** table. This ensures that sensitive information is not fully exposed to non-privileged users, improving the security posture of the WideWorldImporters database.

## Summary

In this lab, you have enabled DDM on credit card numbers and applied DDM to email addresses.

### You have successfully completed the lab!

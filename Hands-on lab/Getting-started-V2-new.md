# Data Modernization

### Overall Estimated Duration: 4 hours

## Lab Scenario

A leading gaming company, **Contoso**, plans to modernize its on-premises SQL Server 2022 environment by migrating its gamer information database and web application to **Azure SQL Managed Instance (SQL MI)**. The organization wants to achieve a secure and scalable cloud migration with minimal downtime while maintaining application availability. 

As a cloud database administrator, you will assess database compatibility, perform an online migration using Azure Database Migration Service, integrate the application with Azure networking, and enhance database security using Microsoft Defender for SQL, Transparent Data Encryption (TDE), Data Discovery & Classification, and Dynamic Data Masking (DDM).

## Lab Overview

In this hands-on lab, you will implement a proof of concept (PoC) for migrating an on-premises SQL Server 2022 database to Azure SQL Managed Instance (SQL MI). You will conduct assessments to identify any feature parity or compatibility issues between the on-premises SQL Server 2022 database and Azure's managed database offerings. Next, you will migrate the customer's on-premises gamer information web application and database to Azure with minimal downtime. Finally, you will enable advanced SQL MI features to enhance the security and performance of the customer's application.

## Objectives

- **Perform database assessments** : Experienced on how to assess an on-premises SQL Server database for compatibility with Azure SQL Database and Azure SQL Managed Instance, identifying potential migration issues and selecting the most suitable migration path. This ensures a smooth transition to Azure while leveraging its scalability and advanced cloud features.

- **Migrate the database to SQL MI** : Gain experience in creating backups, managing services, and performing online database migrations using Azure SQL MI.

- **Update the web application to use the new SQL MI database**: Learned how to deploy a web application to Azure App Service, configure the app to connect to a SQL Managed Instance (SQL MI), and manage essential Azure settings, including connection strings and network configurations for secure database access.

- **Integrate App Service with the virtual network** : Learned how to configure virtual network (VNet) integration with Azure App Services, allowing secure communication with resources inside the VNet. You will also validate that the web application loads correctly after the integration is complete.

- **Improve database security posture with Data Discovery and Classification and Azure Defender for SQL** : Experienced on how to enhance the security posture of a SQL Managed Instance using Data Discovery and Classification to identify sensitive data, and Azure Defender for SQL to assess and mitigate potential vulnerabilities. This will help in securing your database and ensuring compliance with security regulations like GDPR.

- **Enable Dynamic Data Masking** : Experienced on how to implement Dynamic Data Masking (DDM) in SQL Server to protect sensitive data by masking fields like credit card numbers and email addresses. This ensures that non-privileged users can only access obfuscated data, enhancing data security without altering the underlying database.

## Prerequisites

* Ensure you have access to an active Azure subscription with permissions to create and manage resources such as Azure SQL Managed Instance, App Services, Virtual Networks, and Microsoft Defender for Cloud.
* Verify that the JumpVM environment is available with the required tools pre-installed, including Azure Data Studio, SQL Server Management Studio (SSMS), Visual Studio 2022, and SQL Server Configuration Manager.
* Make sure the on-premises SQL Server 2022 instance hosting the **WideWorldImporters** database is running and accessible for assessment, backup, and migration activities.
*  Confirm that network connectivity is properly configured between the SQL Server VM, Azure SQL Managed Instance, Azure Database Migration Service (DMS), and Azure App Service.
*  Ensure that the required Azure resources, including the SQL Managed Instance, storage account, virtual network, and Azure Database Migration Service, are provisioned before starting the migration tasks.
* Basic knowledge of Azure Portal navigation, SQL Server administration, database backup/restore operations, and Azure networking concepts is recommended to successfully complete the lab exercises.

## Architecture

In this hands-on lab, you will follow a structured architecture that begins with provisioning an Azure SQL Managed Instance (SQL MI) for migrating an on-premises SQL Server 2022 database. The process starts by performing a database assessment to identify compatibility issues, followed by an online data migration using the Azure Data Migration Service (DMS). You will then configure a secure virtual network, deploy the customer’s web application to Azure, and integrate it with the managed instance. The lab concludes with enabling advanced SQL MI features for optimizing performance and security, offering a complete solution for migrating and modernizing SQL Server workloads in Azure.

## Architecture Diagram:

![](./media/preferred-solution-architecture1.png)

## Explanation of Components

- **Azure Portal**:  Centralized web interface for managing Azure resources. It allows users to provision services like SQL Managed Instances, configure networks, monitor performance, and troubleshoot issues. With its intuitive platform, users can easily manage and adjust settings for cloud resources, making it essential for efficient Azure operations.

- **Visual Studio 2019**: It is Used to deploy and publish the customer's web application to Azure. It supports continuous development and integration practices, enabling developers to push updates to the cloud-hosted web app that interacts with SQL MI.

- **SQL Server Configuration Manager**: A vital tool for managing SQL Server services, network configurations, and server-specific settings. It is primarily used to control the behavior of SQL Server instances and related services. 

- **Microsoft SQL Server Management Studio 17**: SQL Server Management Studio (SSMS) 17 is a comprehensive tool for managing SQL Server infrastructure. It provides a user-friendly interface for connecting, querying, configuring, and managing SQL Server databases both on-premises and in the cloud

- **Azure Data Studio**: A cross-platform data management and development tool designed for working with SQL Server, Azure SQL Database, and other data sources. It offers a modern, lightweight interface tailored for both developers and database administrators.

## Getting Started with the Lab

Once the environment is provisioned, a virtual machine (JumpVM) and lab guide will get loaded in your browser. Use this virtual machine throughout the workshop to perform the lab. You can see the number on the bottom of the Lab guide to switch to different exercises of the lab guide.

## Accessing Your Lab Environment

Once you're ready to dive in, your virtual machine and lab guide will be right at your fingertips within your web browser.

 ![Access Your VM and Lab Guide](./media/labguide-1.png)

## Virtual Machine & Lab Guide

Your virtual machine is your workhorse throughout the workshop. The lab guide is your roadmap to success.

## Exploring Your Lab Resources

To get a better understanding of your lab resources and credentials, navigate to the **Environment Details** tab.

 ![Explore Lab Resources](./media/2026-05-20_15-49-02.png)

## Utilizing the Split Window Feature

For convenience, you can open the lab guide in a separate window by selecting the **Split Window** button from the Top right corner.

 ![Use the Split Window Feature](./media/swf.png)
 
## Managing Your Virtual Machine

Feel free to start, stop, or restart your virtual machine as needed from the Resources tab. Your experience is in your hands!

## Lab Guide Zoom In/Zoom Out
 
To adjust the zoom level for the environment page, click the **A↕ : 100%** icon located next to the timer in the lab environment.

 ![Use the Split Window Feature](./media/zs.png)

## Let's Get Started with Azure Portal
 
1.  In the JumpVM, click on Azure portal shortcut of Microsoft Edge browser which is created on desktop.
 
    ![Resource groups is highlighted in the Azure services list.](media/new-image1.png)
 
2. You'll see the **Sign into Microsoft Azure** tab. Here, enter your credentials:
 
   - **Email/Username:** <inject key="AzureAdUserEmail"></inject>
 
       ![Enter Your Username](./media/sc900-image-1.png)
 
3. Next, provide your password:
 
   - **Password:** <inject key="AzureAdUserPassword"></inject>
 
       ![Enter Your Password](./media/sc900-image-2.png)
 
4. If prompted to stay signed in, you can click "No."
 
5. If a **Welcome to Microsoft Azure** pop-up window appears, simply click "Cancel" to skip the tour.
 
## Support Contact
 
The CloudLabs support team is available 24/7, 365 days a year, via email and live chat to ensure seamless assistance at any time. We offer dedicated support channels tailored specifically for both learners and instructors, ensuring that all your needs are promptly and efficiently addressed.

Learner Support Contacts:

- Email Support: cloudlabs-support@spektrasystems.com
- Live Chat Support: https://cloudlabs.ai/labs-support

Click Next from the lower right corner to move on to the next page.

![](./media/ns1.png)

### Happy learning!
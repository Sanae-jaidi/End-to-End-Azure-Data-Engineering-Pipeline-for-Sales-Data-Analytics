# End-to-End Azure Data Engineering Pipeline for Sales Data Analytics

This project demonstrates an End-to-End Azure Data Engineering Solution, A Pipeline performing Data Ingestion, ETL and Analytics all-in-one solution using Microsoft Azure Services and Power BI.

## Business Objective

The goal is to create an Azure-based solution that migrates local data to the cloud using an ETL pipeline. This project highlights key practices in data engineering, including data security, transformation, and analytics.

## System Architecture

![System Architecture](https://github.com/user-attachments/assets/d34c8fc7-b592-4b16-a844-a01e1dccff48)

## Prerequisites

1) Microsoft SQL Server Managment System (SSMS)
2) Azure Subscription (Azure Data Lake Storage Gen2, Azure Data Factory, Azure Key Vault, Azure Databricks, Azure Synapse Analytics, Microsoft Entra ID)
3) Microsoft Power BI
4) Set up "AdventureWorksLT2022" Database with credentials 'msuser'. Set up the same credentials as Secrets in Azure Key Vault

The Database used for this project demonstration is:
AdventureWorksLT2022 Sales Database

![AdventureWorksLT2022](https://github.com/user-attachments/assets/593d845f-ac7f-494a-bc2c-bac0e0aaf133)

## Implementation:

### Part 1: Data Ingestion

Data ingestion from the on-premises SQL server to Azure SQL is accomplished via Azure Data Factory. The process involves:

1. Installation of Self-Hosted Integration Runtime.
2. Establishing a connection between Azure Data Factory and the local SQL Server.
3. Setting up a copy pipeline to transfer all tables from the local SQL server to the Azure Data Lake's "bronze" folder.

Note: The Data is stored in "Parquet format" in ADLS Gen2 storage folders.

<img width="1016" alt="Screenshot 2024-12-09 at 23 52 07" src="https://github.com/user-attachments/assets/097038a2-2aec-4690-b0bd-437d130caa42">

## 2: Data Transformation

After ingesting data into the "bronze" folder, it is transformed following the medallion data lake architecture (bronze, silver, gold). Data transitions through bronze, silver, and ultimately gold, suitable for business reporting tools like Power BI.

Azure Databricks, using PySpark, is used for these transformations. Data initially stored in parquet format in the "bronze" folder is converted to the delta format as it progresses to "silver" and "gold." This transformation is carried out through Databricks notebooks:

1. Mount the storage.
2. Transform data from "bronze" to "silver" layer.
3. Further transform data from "silver" to "gold" layer.

<img width="1456" alt="Screenshot 2024-12-10 at 00 34 54" src="https://github.com/user-attachments/assets/404bf93b-26e9-4430-a342-abb22694ead6">

<img width="1454" alt="Screenshot 2024-12-10 at 00 36 27" src="https://github.com/user-attachments/assets/5cec728c-62a6-4536-b125-d84246928100">

<img width="1457" alt="Screenshot 2024-12-10 at 00 35 32" src="https://github.com/user-attachments/assets/ff3bfa81-021b-4ccd-8019-a3575616eaee">

Azure Data Factory is updated to execute the "bronze" to "silver" and "silver" to "gold" notebooks automatically with each pipeline run.

<img width="1410" alt="Screenshot 2024-12-10 at 00 39 05" src="https://github.com/user-attachments/assets/03ffaf0f-81ae-4b40-88ce-94ca094f096e">

## 3: Data Loading

Data from the "gold" folder is loaded into the Business Intelligence reporting application, Power BI. Azure Synapse is used for this purpose. The steps involve:

1. Creating a link from Azure Storage (Gold Folder) to Azure Synapse.
2. Writing stored procedures to extract table information as a SQL view.
3. Storing views within a server-less SQL Database in Synapse.

<img width="530" alt="Screenshot 2024-12-10 at 00 47 39" src="https://github.com/user-attachments/assets/1f8462e8-2219-447f-b332-ca646095f73c">

## 4: Data Reporting

Power BI connects directly to the cloud pipeline using DirectQuery to dynamically update the database. A Power BI report is developed to visualize AdventureWorks dataset data, including sales, product information, and customer gender.

![dbrd](https://github.com/user-attachments/assets/f751345d-d85e-42d7-b874-0f6fba8dd42f)


## 5: Final Pipeline Test

To verify the end-to-end pipeline, two new customers are added to the local SQL database server. If successful, the pipeline will update, and the Power BI report will dynamically show the new data. The total number of customers should increase from 847 to 849.

![Screenshot 2023-10-12 013527](https://github.com/user-attachments/assets/8416e0cc-79fc-4810-ab05-bb5d03bed54c)

Great success!


## Conclusion and Limitations

This project demonstrates the ability to create an end-to-end ETL cloud solution using Azure. Some considerations:

- The dataset used was small (7mb total, 800 rows). This was done to keep compute + storage costs low.
- Multiple applications were employed for a relatively simple task.
- Given the dataset's simplicity, the project could have been managed entirely through Azure Data Factory, with data cleaning done downstream in Power BI.
- The inclusion of Azure Synapse and Databricks was for the sake of self-learning and emulating real-world business pipelines.

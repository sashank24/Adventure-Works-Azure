# Adventure-Works-Azure
End-to-End Data Engineering pipeline using Azure (Data Lake, Databricks, Spark, Synap# Microsoft ETL Pipeline: Data Lake, Databricks, and Synapse Analytics

## Overview

This repository provides step-by-step instructions for setting up an ETL pipeline using Microsoft technologies for the Dataset Adventure Works:

## Architecture

![ETL Pipeline Architecture](#) <!-- Replace with an actual architecture diagram -->

1. **Bronze Layer**: Azure Data Lake for storing raw data.
2. **Silver Layer**: Databricks notebooks to process and clean the data.
3. **Gold Layer**: Synapse Analytics for building the final analytical models.

## Step-by-Step Guide

### Step 1: Bronze Layer (Azure Data Lake)

1. **Create an Azure Storage Account**:
   - Navigate to the Azure Portal and create a storage account.
   - Enable hierarchical namespace to use it as a Data Lake.

2. **Set Up Containers**:
   - Create a container named `bronze` for raw data storage.
   - Upload sample raw data files (e.g., CSV, JSON).

3. **Grant Access**:
   - app registeration using muicrosoft entra ID to grant access to your Databricks workspace.

### Step 2: Silver Layer (Azure Databricks)

1. **Set Up a Databricks Workspace**:
   - Create an Azure Databricks workspace via the Azure Portal.

2. **Configure Data Lake Access**:
   - Mount the Data Lake storage to Databricks using `dbutils.fs.mount`.
   - Example:
     ```python
     dbutils.fs.mount(
         source="wasbs://bronze@<your_storage_account>.blob.core.windows.net",
         mount_point="/mnt/bronze",
         extra_configs={"fs.azure.account.key.<your_storage_account>.blob.core.windows.net": "<your_account_key>"}
     )
     ```

3. **Data Transformation**:
   - Create notebooks to clean and transform data.
   - Save the cleaned data to a `silver` container in Data Lake:
     ```python
     # Example transformation code
     df = spark.read.csv("/mnt/bronze/adventure_works_data*", header=True)
     cleaned_df = df.dropna()
     cleaned_df.write.parquet("/mnt/silver/cleaned_data", mode="overwrite")
     ```

### Step 3: Gold Layer (Azure Synapse Analytics)

1. **Set Up Azure Synapse Workspace**:
   - Create an Azure Synapse workspace via the Azure Portal.

2. **Link Azure Data Lake**:
   - Use Synapse's Data Explorer to connect to the `silver` container in the Data Lake.

3. **Create Analytical Tables**:
   - Use Synapse SQL Pools to create tables and load data:
     ```sql
     CREATE TABLE GoldTable AS
     SELECT * FROM OPENROWSET(
       BULK 'https://<your_storage_account>.dfs.core.windows.net/silver/cleaned_data',
       FORMAT = 'PARQUET'
     ) AS [result];
     ```

4. **Build Visualizations**:
   - Integrate Synapse with Power BI or other tools for analytics.

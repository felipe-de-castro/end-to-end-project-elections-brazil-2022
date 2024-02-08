# end-to-end-project-elections-brazil-2022

This project provides a data engineering journey on the Brazilian election of 2022 dataset. Starting with a zip file from Superior Electoral Court (Brazilian Electoral Justice) , the data is ingested into the Azure ecosystem via Azure Data Factory. It's initially stored in Azure Data Lake Storage Gen2, then transformed in Azure Databricks. The enriched data, once again housed in ADLS Gen2, undergoes advanced analytics in Azure Synapse. 


## Dataset Used 
This contains the details of over Federal and State candidates, with 28909 candidates.
This dataset contains the details of the candidates such as names, age, political party, electoral coalition etc.

Source(TSE): [Federal and States Candidates in 2022 elections](https://dadosabertos.tse.jus.br/dataset/candidatos-2022/resource/435145fd-bc9d-446a-ac9d-273f585a0bb9)

## Azure Services Used
1. **Azure Data Factory:** For data ingestion from GitHub.
2. **Azure Data Lake Storage Gen2**: As the primary data storage solution.
3. **Azure Databricks:** For data transformation tasks.

## Workflow 

## Initial Setup in Azure
1. Create Azure account (Free Subscription)  
2. Create a Resource Group 'data-election-brazil' to house and manage all the Azure resources associated with this project. 
3. Within the created resource group, setup a storage account. This is specifically configured to leverage Azure Data Lake Storage(ADLS) Gen2 capabilities.
4. Create a Container inside this storage account to hold the project's data. Two directories 'raw-data' and 'transfromed-data' are created to store raw data and transformed data.
  <img src="Images/1_container.png"> 

## Data Ingestion using Azure Data Factory
1. First, created an Azure Data Factory workspace within the previously established resource group (data-election-brazil).
2. After setting up the workspace, launch the Azure Data Factory Studio. 
3. Upload the Election-Candidates dataset from TSE portal.
4. Within the studio, initialize a new data integration pipeline. 
   - Configuring the Data Source with HTTP template as we are using http request to get the data from web site.
   - Establishing the Linked Service for source.
   - Configuring the File Format as 'Binary' and Compression 'Zipdeflate'. For setting up the Linked Service Sink just as 'Binary' and file path (raw-data folder).

<img src="Images/2_pipeline.png">  
5. After the pipeline completes its execution, navigate to your Azure Data Lake Storage Gen2. Dive into the "raw_data" folder and subfolder with the CSVs. 
<img src="Images/3_zipfileasinfolder.png">
The zip file returns as a subfolder within the CSVs:
<img src="Images/4_zipped_files.png">

## Data Transformation using Azure Databricks
1. Navigate to Azure Databricks within the Azure portal and create a workspace within the previously established resource group and launch it.
2. Configuring Compute in Databricks
3. Create a new notebook within Databricks and rename it appropriately, reflecting its purpose or the dataset it pertains to.
4. Establishing a Connection to Azure Data Lake Storage (ADLS) using App Registration.
   - Create a new registration;
   - Copy the credentials (Client ID, Tenant ID), to later write the appropriate code in the Databricks notebook to mount ADLS.
   - In Certificate & Secrets: create a secret for later on store in in the Key Vault, for security purposes.
5. Go to Key Vault in the Azure account:
   - First create a key vault with the same resource used before;
   - In the IAM of the key vault, add roles: Secret Officer with the member as your User and Secrets User with the member as 'AzureDatabricks'.
   - Now in the 'Secrets', generate a secret with the App Registration Key secret, name it and create.
   - This Key Vault will secure your secret key so it exposed in the mount code.

<img src="Images/5_key_vault_code_secure.png">

7. Writing Data Transformations mount ADLS Gen2 to Databricks.
   - Within the code using python with OS library, removed files that were not used. (code is provided in the reference below)
Refer below notebook to transformations and code used to mount ADLS Gen2 to Databricks.
[Election-Brazil-Transformation.ipynb](https://github.com/)

8. Writing Transformed Data to ADLS Gen2.




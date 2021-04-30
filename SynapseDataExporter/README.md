## Table Of Contents:
1. Introduction
2. Architecture
3. Technical details
4. Pipeline workflow explanation for each scenario
5. Setfup and Configure Notifications for MS Teams and Outlook using App logic
 

## Synapse Data Exporter

#### 1. Introduction:
Synapse Data Exporter (SDE) is a tool that allows the customer to export data from Synapse Analytics to Azure Blob Storage. 
The solution helps customers in reducing the cost of data storage and also helps them run analysis on the data after it is exported to blob storage since blob storage is supported as a source in almost all Azure data analysis services.By exporting data to Blob storage the data analysts are able to query the data directly from multiple Azure services such as DataBricks, Streaming Analytics, Event Hub, and Power BI.

It’s helpful in the downstream model where there is a system built within an organization, where people seek this aggregated data and use it for different purposes.

#### 2. Architecture
![Architecture](./images/synapseDataExporter/architecture.png)

#### 3. Technical Details

The main use of synapse data exporter is to extract data from synapse analytics and store data in azure blob storage account with the support of **CSV**, **JSON**, **JSONL** and **PARQUET** formats.

This solution provides **03 scenarios** for exporting data from synapse analytics

a. Export a specific table from synapse analytics
b. Export specific schema from synapse analytics
c. Export entire data from synapse analytics
     
     Total Pipelines: 3 use cases x 4 file formats = 12
     
     Once you deploy the ARM template all the 12 pipelines will be deployed in the Azure Data Factory. You can execute the pipeline as per requirement and file format that you need.
     
![pipeline](./images/synapseDataExporter/pipelines.png)


#### Prerequisites:
1. Resource group for the deployment.
2. Azure Synapse Analytics

#### Parameters that will be provided while deploying the ARM template
1. Resource Group
2. Data Factory Name
3. Location
4. Storage Account Name (option for **new** or **existing**)
5. Sql DB Pool Name
6. Synapse Workspace Name
7. Administration Login: synapse username
8. Administration Password
9. Schema Name: Name of schema from which you want to copy data
10. Table Name: Any table from the given schema that you want to copy
11. Container Name: The name of blob storage container where you want to copy data
12. Dataset Name: write the name of dataset that wil be renamed once it is copied to blob storage
13. Email To
14. Email From
15. Logic app name
16. Key vault name
17. Azure user object id


Click the following button to deploy all the resources.


[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/ttps%3A%2F%2Fraw.githubusercontent.com%2Femumba-msft-data-pipelines%2Fsynapse-data-exporter%2Fmain%2FSynapseDataExporter%2Ftemplates%2Fazuredeploy.json)

Once the template is deployed following resources will be created 
1. Azure Data Factory
2. Storage Account
3. API connection for ms teams
4. API connection for outlook
5. App logic
6. Key Vault

![Resources Created](./images/synapseDataExporter/resources.png)

Open the deployed data factory it will contain 12 pipelines for copying data to blob storage with support of CSV, JSON, JSONL and Parquet formats as shown below.

## 3. Technical Details

Once you open the deployed data factory, click the manage icon from the extreme left, you will notice three linked templates created for storage account, synapse analytics and key vault


![Linked services](./images/synapseDataExporter/linkedServices.png)


Synapse analytics linked service for establishing connection with it.

![synapse-linkedservice](./images/synapseDataExporter/synapseLinkedService.png)

Azure Blob Storage linked service for establishing connection with it.

![blobstorage-linkedservice](./images/synapseDataExporter/storageLinkedSerice.png)

#### Scenario1: Exporting specific table from synapse analytics

Take schema name, table name from the user then copy data from synapse exporter to azure blob storage with support of CSV, JSON, JSONL and PARQUET.

Pipeline For Exporting Data To CSV Format
You need to provide following parameters before for executing the pipeline
1. schemaName
2. tableName
3. containerName
4. datasetName
5. EmailTo
6. From

![CSV Format](./images/synapseDataExporter/sc1PipelineCSV.png)

Pipeline For Exporting Data To JSON Format
You need to provide following parameters before for executing the pipeline
1. schemaName
2. tableName
3. containerName
4. datasetName
5. EmailTo
6. From

![JSON Format](./images/synapseDataExporter/sc1PipelineJson.png)

Pipeline For Exporting Data To JSONL Format
You need to provide following parameters before for executing the pipeline
1. schemaName
2. tableName
3. containerName
4. datasetName
5. EmailTo
6. From

![JSONL Format](./images/synapseDataExporter/sc1pipelineJsonl.png)

Pipeline For Exporting Data To PARQUET Format
You need to provide following parameters before for executing the pipeline
1. schemaName
2. tableName
3. containerName
4. datasetName
5. EmailTo
6. From

![PARQUET Format](./images/synapseDataExporter/sc1PipelineParquet.png)

#### 4. Pipeline workflow explanation for each scenario

####Scenario1: Exporting a specific table

#### Step1: Copy activity
 
 Copy actvity is used which gets the table name from the synapse analytics and copies into the given blob storage account.
 Copy activity source contains synapse linked service and takes values of schema name and table name by using tableName and schemaName parameters
 
![copy-activity-source](./images/scenario1/Scenario1copyDataSource.png)

 In the sink of copy activity, dataset of desired format is selected and required parameters value is given
 
![copy-activity-sink](./images/scenario1/Scenario1CopyDataSink.png)


#### Step2: Web Activity is used to send file name to logic app for notifications along with other parameters

Inside settings of web activity **logic app end point URL** is provided and required parameters values are passed in the body 

![web-activity](./images/scenario1/scenario1WebActivity.png)


![web-activity-body](./images/synapseDataExporter/sc1WebActivityCSV.png)

#### Scenario2: Exporting a specific schema from synapse analytics.

Take schema name then copy all tables of that schema from synapse exporter to azure blob storage with support of CSV, JSON, JSONL and PARQUET.

Pipeline For Exporting Data To CSV Format
You need to provide following parameters before executing the pipeline
1. containerName
2. schemaName
5. EmailTo

![CSV Format](./images/synapseDataExporter/Sc2PipelineCsv.png)

Pipeline For Exporting Data To JSON Format
You need to provide following parameters before for executing the pipeline
1. containerName
2. schemaName
5. EmailTo

![JSON Format](./images/synapseDataExporter/sc2PipelineJson.png)

Pipeline For Exporting Data To JSONL Format
You need to provide following parameters before for executing the pipeline
1. containerName
2. schemaName
5. EmailTo

![JSONL Format](./images/synapseDataExporter/Sc2PipelineParquet.png)

Pipeline For Exporting Data To PARQUET Format
You need to provide following parameters before for executing the pipeline
1. containerName
2. schemaName
5. EmailTo

![parquet Format](./images/synapseDataExporter/Sc2PipelineParquet.png)

#### Pipeline workflow explanation

#### Step1: Lookup activity for getting the list of table
   
   Lookup activity is used to get the tables information. In the settings tab source dataset synapse linked service is selected from where data will be fetched.
   In the query section query is written which fetches all the tables of given schema
   
![lookup-get-tables](./images/scenario2/lookup-get-tables.png)

![lookup-query](./images/scenario2/get-tables-query.png)

#### Step2: For each activity 
   
   For each activity is used which iterates the table name one by one
 
![for-each](./images/scenario2/for-each-loop.png)
 
 
#### Step3: Copy activity inside For each activity
 
 Copy actvity is used which gets the each table name from the synapse analytics and copies into the given blob storage account.
 Copy activity source contains synapse linked service and takes values of schema name and table name by using tableName and schemaName parameters
 
![copy-activity-source](./images/scenario2/copy-data-source.png)

 In the sink of copy activity, dataset of desired format is selected and containing parameters of containerName and datasetName
 
![copy-activity-sink](./images/scenario2/copy-data-sink.png)

Dataset name is created by combining schema name and table table 

![dataset-name](./images/scenario2/copy-data-datasetName.png)

#### Step4: Using get variables and set variables activity to get the list of cocpied files

1. Two variables with the names **fileNamesCopy** and **fileName** are created

![variables](./images/scenario2/Variables.png)

2. Get variables activity is used inside for each activity to get table name each time the for each activity is executed and which is stored in **fileNamesCopy** variable

![variables-apped](./images/scenario2/vairables-append.png)

3. The value of filename is created by concatenating schema name and table name

![variables-apped-value](./images/scenario2/vairable-append-value.png)

4. Set variables activity is used outside the for each activity which will contain the comma separated list of values and will be stored in another variable named **fileName**

![set-variable-activity](./images/scenario2/set-variable.png)


#### Step5: Web Activity is used to send list of files to logic app for notifications along with other parameters

Inside settings of web activity **logic app end point URL** is provided and required parameters values are passed in the body 

![web-activity](./images/scenario2/web-activity.png)


![web-activity-body](./images/synapseDataExporter/sc2Webactivity.png)



#### Scenario3: Exporting all data from synapse analytics

 Copy all data from  into azure blob storage with support of CSV, JSON, JSONL and PARQUET.

Pipeline For Exporting Data To CSV Format
You need to provide following parameters before for executing the pipeline
1. containerName
2. EmailTo
3. From

![CSV Format](./images/synapseDataExporter/sc3PipelineCsv.png)

Pipeline For Exporting Data To JSON Format
You need to provide following parameters before for executing the pipeline
1. containerName
2. EmailTo

![JSON Format](./images/synapseDataExporter/Sc3PipelineJson.png)

Pipeline For Exporting Data To JSONL Format
You need to provide following parameters before for executing the pipeline
1. containerName
2. EmailTo

![JSONL Format](./images/synapseDataExporter/Sc3PipelineJsonl.png)

Pipeline For Exporting Data To PARQUET Format
You need to provide following parameters before for executing the pipeline
1. containerName
2. EmailTo

![parquet Format](./images/synapseDataExporter/Sc3PipelineParquet.png)

#### Step1: Lookup activity for getting tables list
   
   Lookup activity is used to get the tables information. In the settings tab source dataset synapse linked service is selected from where data will be fetched.
   In the query section query is written which fetches all the tables of given schema
   
![lookup-get-tables](./images/synapseDataExporter/sc3lookup.png)

Query is written for getting all schemas tables
![lookup-query](./images/synapseDataExporter/sc3lookupQuery.png)

#### Step3: For each activity 
   
   For each activity is used which iterates the table name one by one
 
![for-each](./images/synapseDataExporter/sc3Foreach.png)
 
 
#### Step4: Copy activity inside For each activity
 
 Copy actvity is used which gets the each table name from the synapse analytics and copies into the given blob storage account.
 Copy activity source contains synapse linked service and takes values of schema name and table name by using tableName and schemaName parameters
 
![copy-activity-source](./images/synapseDataExporter/sc3copysource.png)

 In the sink of copy activity, dataset of desired format is selected and containing parameters of containerName and datasetName
 
![copy-activity-sink](./images/synapseDataExporter/sc3CopySink.png)

Dataset name is created by combining schema name and table table 

![dataset-name](./images/synapseDataExporter/sc3CopySinkQuery.png)

#### Step5: Using get variables and set variables activity to get the list of cocpied files

1. Four variables with the names **fileNamesCopy**, **fileName**, **schemaNameCopy** and **schemaName** are createed

![variables](./images/synapseDataExporter/sc3Variables.png)

2. Get variables activity is used inside for each activity to get table name each time the for each activity is executed and which is stored in **fileNamesCopy** variable.
The value of filename is created by concatenating schema name and table name

![variables-apped](./images/synapseDataExporter/sc3AppendFileame.png)

3. Get variables activity is used inside for each activity to get schema name each time the for each activity is executed and which is stored in **SchemaNameCopy** variable.

![variables-apped-schema](./images/synapseDataExporter/sc3AppendSchema.png) 

4. Set variables activity is used outside the for each activity which will contain the comma separated list of values and will be stored in another variable named **fileName**

![set-variable-activity](./images/synapseDataExporter/sc3SetFileList.png)

4. Set variables activity is used outside the for each activity which will contain the comma separated list of values and will be stored in another variable named **fileName**

![set-variable-schema](./images/synapseDataExporter/Sc3SetSchemaList.png)

#### Step6: Web Activity is used to send list of files to logic app for notifications along with other parameters

Inside settings of web activity **logic app end point URL** is provided and required parameters values are passed in the body 

![web-activity](./images/synapseDataExporter/sc3WebActivity.png)


![web-activity-body](./images/synapseDataExporter/sc3WebActivity.png)

#### 5. Setfup and Configure Notifications for MS Teams and Outlook using App logic

#### Step1: Authenticating Microsoft Teams account with Azure Logic App


1. First, navigate to the resource group that contains your deployment and find the resource titled **"msftTeamsConnectionAuth"**. Click on it and navigate to its **"Edit API connection"** option from the sidebar. 

![connection_image](./images/scenario1/editapiconnections.jpg)

2. In the window there will be a button titled **"Authorize"**, click on it and it will open up Microsoft sign-in page. Enter the team account credentials and it will authorize you to your team's account.

![authorize_image](./images/scenario1/authorizeTeams.jpg)

3. Click on **"Save"** to save the authorization information and navigate to resource group.

4. Now click on the deployed logic app, the default name of which is **"TeamsNotify"**. Click on the option **"Logic app designer"** from the sidebar under heading **"Development tools"**. This will open a visual editor, if there was problem connecting to teams then it will display connection error. In that case, refer back to step 1.

![designer_image](./images/scenario1/sc1AppLogic.png)

#### Step2: Authenticating Microsoft Outlook account with Azure Logic App

1. First, navigate to the resource group that contains your deployment and find the resource titled **"outlook"**. Click on it and navigate to its **"Edit API connection"** option from the sidebar. 

![connection_image_sc2](./images/scenario1/editapiconnections.jpg)

2. In the window there will be a button titled **"Authorize"**, click on it and it will open up Microsoft sign-in page. Enter the outlook account credentials and it will authorize you to your outlook's account.

![authorize_image_sc2](./images/scenario1/authorizeoutlook.png)

3. Click on **"Save"** to save the authorization information and navigate to resource group.

4. Now click on the deployed logic app, the default name of which is **"TeamsNotify"**. Click on the option **"Logic app designer"** from the sidebar under heading **"Development tools"**. This will open a visual editor, if there was problem connecting to teams then it will display connection error. In that case, refer back to step 1.

![designer_image_sc2](./images/scenario2/logicAppDesigner.png)

#### Step3: Logic App contains the logic for sending notifications from azure data factory to ms teams and outlook 

1. Inside the logic app designere action of **when a HTTP request is received** is selected which contains the json paylod. This will also provide end point url of logic app.

![logic-app-http-payload](./images/scenario2/logicApphttp.png)

2. New action for ms teams **Post a message V3** is added which will be used to send notification to ms teams.

![app-logic-teams](./images/scenario2/logicAppTeams.png)

3. New action for Microsoft outlook **Send an email V2** is added which will be used to send notification via email.

![app-logic-outlook](./images/synapseDataExporter/outlogicapp.png)

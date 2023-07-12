<!-- Improved compatibility of back to top link: See: https://github.com/othneildrew/Best-README-Template/pull/73 -->
<div id="top"></div>

<!-- PROJECT LOGO -->
<br />
<div align="center">
  <h2 align="center">Data Cortex Framework</h2>
  <p align="center">
    Case Study - Data Cortex Framework Setup
  </p>
  <!--div>
    <img src="images/profile_pic.png" alt="Logo" width="80" height="80">
  </div-->
</div>

---

<!-- TABLE OF CONTENTS -->

## Table of Contents

<!-- <details> -->
<ol>
    <li>
        <a href="#about-the-project">About The Project</a>
    </li>
    <li>
        <a href="#architecture">Architecture</a>
        <ul>
            <li><a href="#data-foundation-architecture">Data Foundation Architecture</a></li>
            <li><a href="#demand-sensing-architecture">Demand Sensing Architecture</a></li>
        </ul>
    </li>    
    <li>
        <a href="#setup">Setup</a>
        <ul>
            <li><a href="#api-services">API Services</a></li>
            <li><a href="#iam">IAM</a></li>
            <li><a href="#bigquery">BigQuery</a></li>
            <li><a href="#cloud-storage">Cloud Storage</a></li>
            <li><a href="#cloud-composer">Cloud Composer</a></li>
            <li><a href="#looker">Looker</a></li>
            <li><a href="#prerequisite-check">Prerequisite Check</a></li>
        </ul>
    </li>
    <li>
        <a href="#implementation">Implementation</a>
        <ul>
            <li><a href="#data-foundation">Data Foundation</a></li>
            <li><a href="#looker-block-sap">Looker Block (SAP)</a></li>
            <li><a href="#looker-block-salesforce">Looker Block(Salesforce)</a></li>
            <li><a href="#demand-sensing">[Optional] Demand Sensing</a></li>
            <li><a href="#looker-block-demand-sensing">[Optional] Looker Block (Demand Sensing)</a></li>
        </ul>
    </li>
    <li>
        <a href="#model">Model</a>
        <ul>
            <li><a href="#model-training">Model Training</a></li>
            <li><a href="#model-forecast">Model Forecast</a></li>
        </ul>
    </li>
    <li><a href="#usage">Usage</a>
        <ul>
            <li><a href="#via-looker-block-sap">Via Looker Block (SAP)</a></li>
            <li><a href="#via-looker-block-salesforce">Via Looker Block (Salesforce)</a></li>
            <li><a href="#via-looker-block-demand-sensing">[Optional] Via Looker Block (Demand Sensing)</a></li>
        </ul>
    </li>
    <li><a href="#challenges">Challenges</a></li>
    <li><a href="#acknowledgments">Acknowledgments</a></li>
</ol>
<!-- </details> -->

---

<!-- ABOUT THE PROJECT -->

## About The Project

This project is created to showcase how to integrate 3rd party SAS (E.g SAP) into `Google Cloud` using the `Data Cortex Framework`.

The following are some of the requirements:

- Setup `Data Cortex Framework` in `Google Cloud` Platform
- Setup `Looker` visualizations for data stored in `BigQuery`

<p align="right">(<a href="#top">back to top</a>)</p>

---

<!-- Architecture -->

## Architecture

Base on the requirements, the following are the architectures involved in the `Data Cortex Framework`:
- `Data Foundation Architecture` - Connect with 3rd party applications and provide quicker insights for the data
- `Demand Sensing Architecture` - Add insights for demand planning with the data ingested

<p align="right">(<a href="#top">back to top</a>)</p>

### Data Foundation Architecture

The architecture allow for easy connectivity of data from 3rd party applications (E.g SAP) into `BigQuery` and provide templates for data processing and visualization to allow user to have quicker insights on their data. (More information [here][data-cortex-foundation-overview])

| ![data-cortex-foundation-architecture][data-cortex-foundation-architecture] | 
|:--:| 
| *Data Cortex Foundation Architecture* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

### Demand Sensing Architecture

The architecture allow for analysing the data in `BigQuery` to dervie a demand plan via machine learning models to allow user to gain new insights and highlight potential impacts in the near term. (More information [here][ref-cortex-demand-sensing-deployment-guide])

| ![data-cortex-demand-sensing-architecture][data-cortex-demand-sensing-architecture] | 
|:--:| 
| *Data Cortex Demand Sensing Architecture* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

---

<!-- Setup -->

## Setup

Base on the requirements, the following components are required to be setup (More information [here][data-cortex-foundation-github]):

- `API Service` - Enable the services needed to run the `Google Cloud Data Foundation`
- `IAM` - Create a service account to run the `Google Cloud Data Foundation`
- `BigQuery` - Store the data from the 3rd party applications (E.g `SAP`, `Salesforce`)
- `Cloud Storage` - Create a bucket to store the dags created to migrate data from 3rd party applications (E.g `SAP`, `Salesforce`) into `Bigquery`
- `Cloud Composer` - Install the dependencies and setup connections required for `Cloud Composer` to run the generated dags
- `Looker` - Setup the `BigQuery` connection and attributes required to display the visualizations
- `Prerequisite Check` - Ensure that the environment is setup for the `Google Cloud Data Foundation` deployment

<p align="right">(<a href="#top">back to top</a>)</p>

### API Services 

Replace the *SOURCE_PROJECT* with your project id and run the following commands to enable the services (More information [here][data-cortex-setup-api-services]):

```bash
gcloud config set project <SOURCE_PROJECT>

gcloud services enable bigquery.googleapis.com \
                       cloudbuild.googleapis.com \
                       composer.googleapis.com \
                       storage-component.googleapis.com \
                       cloudresourcemanager.googleapis.com
```

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

### IAM

Grant the following permissions to the `Cloud Build` service account (More information [here][data-cortex-setup-service-account-permission] for `Data Cortex Foundation` and [here][data-cortex-setup-demand-sensing-service-account-permission] for `Demand Sensing`):
- Service Usage Consumer
- Source Repository Administrator (*Demand Sensing*)
- Service Account Creator (*Demand Sensing*)
- Storage Object Admin (*Demand Sensing*)
- Storage Object Creator
- Storage Object Viewer (for the `Cloud Build` default bucket or bucket for logs)
- Object Admin (for `Cloud Build` logs)
- Object Writer (to write to the output buckets)
- Cloud Build Editor
- Project Viewer
- BigQuery Data Editor
- BigQuery Job User

<br/>

`NOTE:` In the event that you are using your *user account* to impersonate the `Cloud Build` service account, you will need to grant yourself the following permissions (More information [here][data-cortex-setup-service-account-impersonation]):
- Service Account Token Creator

<br/>

| ![data-cortex-deploy-data-foundation-service-account][data-cortex-deploy-data-foundation-service-account] | 
|:--:| 
| *Cloud Build Service Account* |

<br/>

`NOTE:` While the image shows that the *service account* is provided with *admin* rights, the above mentioned privileges should suffice.

| ![data-cortex-deploy-data-foundation-service-account-permissions][data-cortex-deploy-data-foundation-service-account-permissions] | 
|:--:| 
| *Cloud Build Service Account Permissions* |

### BigQuery

Create the following dataset for the data migration from 3rd party application (E.g `SAP`, `Salesforce`) into `BigQuery`: <br/>
`NOTE`: The region of the dataset should be the same as the buckets in `Cloud Storage`

- `CDC_DATASET` - Dataset that works as a source for the reporting views, and target for the records processed DAGs
- `RAW_DATASET` - Used by the CDC process, this is where the replication tool lands the data from SAP
- `REPORT_DATASET` - Name of the dataset that is accessible to end users for reporting, where views and user-facing tables are deployed
- `MODEL_DATASET` - Name of the dataset that stages results of Machine Learning algorithms or BQML models
- `VERTEX_AI_DATASET` - Name of the dataset that is use to store results of the pipeline
- `LOOKER_PDT_DATASET` - Dataset that allows persistence data for Looker

```bash
bq --location=<REGION> mk -d <TARGET_PROJECT>.<CDC_DATASET>
bq --location=<REGION> mk -d <TARGET_PROJECT>.<RAW_DATASET>
bq --location=<REGION> mk -d <TARGET_PROJECT>.<REPORT_DATASET>
bq --location=<REGION> mk -d <TARGET_PROJECT>.<MODEL_DATASET>
bq --location=<REGION> mk -d <TARGET_PROJECT>.<VERTEX_AI_DATASET>
bq --location=<REGION> mk -d <TARGET_PROJECT>.<LOOKER_PDT_DATASET>
```

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

### Cloud Storage

Replace *REGION*, *DAG_TEMP_BUCKET*, *VERTEX_AI_BUCKET*, *LOG_BUCKET* to create buckets in the **same region** as the `BigQuery` dataset for the following usage (More information [here][data-cortex-setup-cloud-bucket]): <br/>
`NOTE`: The region of the buckets should be the same as the datasets in `BigQuery`

- `LOG_BUCKET` - Store the logs for the `Cloud Build` process
- `DAG_TEMP_BUCKET` - Store the generated scripts generated for `Cloud Composer`
- `VERTEX_AI_BUCKET` - Store the logs and progress of the model training for `Demand Sensing`

```bash
gsutil mb -l <REGION> gs://<LOG_BUCKET>
gsutil mb -l <REGION> gs://<DAG_TEMP_BUCKET>
gsutil mb -l <REGION> gs://<VERTEX_AI_BUCKET>
```

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

### Cloud Composer 

#### Python Dependencies

The following are the python dependencies that need to be installed in `Cloud Composer` for the dag to run successfully:

|#|Library|Description|
|--|--|--|
|1|pytrends|For integrating with Google Trend reporting|
|2|holidays|For generating holidays of different countries|
|3|apache-airflow-providers-salesforce|For running of dag that interface with `Salesforce`|

| ![data-cortex-cloud-composer-dependencies][data-cortex-cloud-composer-dependencies] | 
|:--:| 
| *Cloud Composer Dependencies* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### BigQuery Connections

The following connection is required for the DAGS to run (More information [here][data-cortex-setup-composer-connection]): 
- `sap_cdc_bq` - For CDC Dags
- `sap_reporting_bq` - For Reporting Dags
- `sfdc_cdc_bq` - For Salesforce Deployment

| ![data-cortex-cloud-composer-connections][data-cortex-cloud-composer-connections] | 
|:--:| 
| *Cloud Composer Connection* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

### Looker 

#### Service Account Key

Navigate to `IAM` > Under Service Accounts > Select the `Looker` service account > Select Keys > Select Add Key > Select Create New Key > Select *JSON* for the Key Type. This *JSON* file will be used to setup the connection to `BigQuery`

| ![data-cortex-looker-bigquery-sa-key][data-cortex-looker-bigquery-sa-key] | 
|:--:| 
| *Looker Service Account Key* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### BigQuery Connection

Navigate to `Looker` > Under Admin > Database > Connections > Add a connection to the `BigQuery`with the following fields (More information [here][data-cortex-setup-looker-pdts]):
- `Name` - <CONNECTION_NAME>
- `Dialect` - Google BigQuery Standard SQL
- `Billing Project ID` - <TARGET_PROJECT>
- `Dataset` - <REPORT_DATASET>
- `Authentication` - Service Account
- *Upload JSON or P12 file*
- *Optional Settings > Persistent Derived Tables (PDTS)*
    - `Enable PDTs` - Set to **true**
    - `Temp database` - <LOOKER_PDT_DATASET>

`NOTE:` While the image shows the `Billing Project ID` and `Dataset` is empty, do filled in the corresponding values for these fields

| ![data-cortex-looker-bigquery-connection-a][data-cortex-looker-bigquery-connection-a] | 
|:--:| 

| ![data-cortex-looker-bigquery-connection-b][data-cortex-looker-bigquery-connection-b] | 
|:--:| 
| *Looker BigQuery Connection* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### User Attributes

Navigate to `Looker` > Under Admin > Users > Users Attributes, add the following attributes (More information [here][data-cortex-setup-looker-user-attributes]):
- `default_value_currency_required` - Default Currency
- `client_id_rep` - Desired Client ID Rep

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### default_value_currency_required

Enter the following for the `default_value_currency_required` defintion:
- `Name` - *default_value_currency_required*
- `Label` - Default Value Currency Required
- `Data Type` - String
- `User Access` - View
- `Default Value` - SGD

| ![data-cortex-looker-attributes-default-currency][data-cortex-looker-attributes-default-currency] | 
|:--:| 
| *Looker User Attributes (default_value_currency_required)* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### client_id_rep

Enter the following for the `client_id_rep` defintion:
- `Name` - *client_id_rep*
- `Label` - Default Value for Client Id Rep Required
- `Data Type` - String
- `User Access` - View
- `Default Value` - 100

| ![data-cortex-looker-attributes-client-id][data-cortex-looker-attributes-client-id] | 
|:--:| 
| *Looker User Attributes (client_id_rep)* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

### Prerequisite Check

There is a checker to examine the readiness of the environment by performing the following actions:
- List the given bucket
- Write to the bucket
- Create a BQ dataset
- Create a BQ table in the dataset
- Write a test record

<br/>

Replace *SOURCE_PROJECT*, *DAG_TEMP_BUCKET*, *LOG_BUCKET* to check for any outstanding configurations before deploying the `Data Cortex Framework` (More information [here][data-cortex-setup-prerequisite-check]):

<br/>

`NOTE:` There a (`.`) at the end of the command. Additional [configurations][data-cortex-setup-prerequisite-parameters] can be set for the prerequisite check.

```sh
git clone https://github.com/lsubatin/mando-checker
cd mando-checker
gcloud builds submit \
   --project <SOURCE_PROJECT> \
   --substitutions _DEPLOY_PROJECT_ID=<SOURCE_PROJECT>,_DEPLOY_BUCKET_NAME=<DAG_TEMP_BUCKET>,_LOG_BUCKET_NAME=<LOG_BUCKET> .
```

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

---

## Implementation

Base on the requirements, the following are the tasks in the process:

- `Data Foundation` - Deploy the `Data Cortex Framework` into the project
- `Demand Sensing` - Using the data from `BigQuery` to forecast usage
- `Looker Block (SAP)` - The dashboards for `SAP` related data
- `Looker Block (Salesforce)` - The dashboards for `Salesforce` related data
- `Looker Block (Demand Sensing)` - The dashboards to predict future usage

<p align="right">(<a href="#top">back to top</a>)</p>

### Data Foundation

#### Setup

Run the following command to get the setup for `Data Cortex Framework`:

```sh
git clone --recurse-submodules https://github.com/GoogleCloudPlatform/cortex-data-foundation
cd cortex-data-foundation
```

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Settings

The table mappings and intervals for the generated dags can be found in the *setting.yaml*

| ![data-cortex-deploy-data-foundation-setting][data-cortex-deploy-data-foundation-setting] | 
|:--:| 
| *Data Foundation Dag Settings* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Parameters
The following are some of the common parameters for the deployment (More information [here][data-cortex-setup-foundation-parameters]):

|#|Parameter|Description|Remarks|
|--|--|--|--|
|1|`_PJID_SRC`|Project where the source dataset is and the build will run|project for landing raw data|
|2|`_PJID_TGT`|Target project for user-facing datasets (reporting and ML datasets)|project to deploy user-facing views|
|3|`_DS_CDC`|Dataset that works as a source for the reporting views, and target for the records processed DAGs|If using test data, create an empty dataset. BQ dataset to land the result of CDC processing <br/> `NOTE:` must exist before deployment|
|4|`_DS_RAW`|Used by the CDC process, this is where the replication tool lands the data from SAP|If using test data, create an empty dataset. BQ dataset to land raw data from replication <br/> `NOTE:` must exist before deployment|
|5|`_DS_REPORTING`|Name of the dataset that is accessible to end users for reporting, where views and user-facing tables are deployed|BQ dataset where Reporting views are created <br/> `NOTE:` will be created if it does not exist|
|6|`_DS_MODELS`|Name of the dataset that stages results of Machine Learning algorithms or BQML models|BQ dataset where ML views are created <br/> `NOTE:` will be created if it does not exist|
|7|`_DAG_TEMP_BUCKET`|Bucket for logs|Bucket for logs <br/> `NOTE:` Cloud Build Service Account needs access to write here|
|8|`_TGT_BUCKET`|Bucket where DAGs will be generated|Bucket for DAG scripts <br/> `NOTE:` Avoid using the actual airflow bucket - Cloud Build Service Account needs access to write here|
|9|`_TEST_DATA`|Indicates to filled tables with sample data that mimicked a replicated dataset|Set to **true** if you want the `_DS_REPORTING` and `_DS_CDC` (if `_DEPLOY_CDC` is true) to filled with sample data. <br/> Set to **false**, if `_DS_RAW` should contain the tables replicated from the SAP source. <br/> `Default:` table creation is **--noreplace** (if the table exists, the script will not attempt to overwrite it)|
|10|`_DEPLOY_CDC`|Generate the DAG files into a target bucket based on the tables in settings.yaml|If using test data, set it to **true** so data is copied from the generated raw dataset into this dataset. <br/> If set to **false**, DAGs won't be generated and it should be assumed the `_DS_CDC` is the same as `_DS_RAW`|
|11|`_GEN_EXT`|Generate DAGs to consume external data (e.g., weather, trends, holiday calendars). Some datasets have external dependencies that need to be configured before running this process|If `_TEST_DATA` is **true**, the tables for external datasets will be populated with sample data. <br/> `Default:` **true**|
|12|`_DEPLOY_SAP`|Execute SAP deployment steps in *cloudbuild.yaml*|`Default:` **true**|
|13|`_DEPLOY_SFDC`|Execute SFDC deployment steps in *cloudbuild.yaml*|`Default:` **true**|

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Deployment

Replace *SOURCE_PROJECT*, *TARGET_PROJECT*, *CDC_DATASET*, *RAW_DATASET*, *REPORT_DATASET*,*MODEL_DATASET*, *LOG_BUCKET*, *DAG_TEMP_BUCKET* in the following command to deploy Data Cortex Framework` into the project(More information [here][data-cortex-setup-foundation]) <br/>

NOTE: Run the command from the `cortex-data-foundation` folder. If the `_TEST_DATA` is set to *false*, you will need to create the corresponding table in `_DS_RAW` and `_DS_CDC` on your own.

<br/>

```sh
gcloud builds submit \
    --project <SOURCE_PROJECT> \
    --substitutions _PJID_SRC=<SOURCE_PROJECT>,_PJID_TGT=<TARGET_PROJECT>,_DS_CDC=<CDC_DATASET>,_DS_RAW=<RAW_DATASET>,_DS_REPORTING=<REPORT_DATASET>,_DS_MODELS=<MODEL_DATASET>,_DAG_TEMP_BUCKET=<LOG_BUCKET>,_TGT_BUCKET=<DAG_TEMP_BUCKET>,_TEST_DATA=true,_DEPLOY_CDC=true,_GEN_EXT=true,_DEPLOY_SAP=true,_DEPLOY_SFDC=true
```

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Dag Migration

After successful deployment, you can migrate the generated dags into the `Cloud Composer` dag folder 

```sh
gsutil cp -r  gs://<DAG_TEMP_BUCKET>/dags* gs://<DAG_BUCKET>/dags
gsutil cp -r  gs://<DAG_TEMP_BUCKET>/data/* gs://<DAG_BUCKET>/data/
```

Navigate to `Cloud Composer` and verify that the dags have been created.

| ![data-cortex-cloud-composer-dags][data-cortex-cloud-composer-dags] | 
|:--:| 
| *Cloud Composer Data Foundation Dags* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

### Demand Sensing

Once `Data Foundation` is installed,  you will be able to make use of the data in `BigQuery` for analytics purposes such as forcasting (More information [here][ref-cortex-demand-sensing-deployment-guide]) <br/>

`NOTE:` This is an optional step, however if you want to install the `Looker` block for `Demand Sensing`, you will need to follow this setup
<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Setup

Before proceeding with the deployment, there are some prerequisites to verify (More information [here][data-cortex-setup-model-prerequisite]):
- `Reporting Views` - View *SalesOrders*, *CusomtersMD*, *MaterialsMD* exists
- `External Sources` - Table *Trends*, *Weather*, *Holiday Calendar* exists
- `SAP Client (_MANDT)` - **900** if using test data, otherwise can ignore
- `SQL Flavor (_SQL_FLVOUR)` - **ECC** if using test data, otherwise can ignore

| ![data-cortex-deploy-demand-sensing-database-setting][data-cortex-deploy-demand-sensing-database-setting] | 
|:--:| 
| *Data Cortex Demand Sensing Data Prerequisite* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Parameters
The following are some of the common parameters for the deployment (More information [here][data-cortex-setup-demand-sensing-parameters]):

|#|Parameter|Description|Remarks|
|--|--|--|--|
|1|`_PJID_SRC`|Source Google Cloud Project|Project where the source data is located, from which the data models consume|
|2|`_PJID_TGT`|Target Google Cloud Project|Project where the Data Foundation for SAP predefined data models was deployed|
|3|`_DS_RAW`|Source Raw BigQuery Dataset|BigQuery dataset where the source SAP data was replicated to or where the test data was created|
|4|`_DS_CDC`|CDC BigQuery Dataset|BigQuery dataset where the CDC processed data lands the latest available records|
|5|`_DS_REPORTING`|Target BigQuery reporting dataset|BigQuery dataset where the Data Foundation for SAP predefined data models was deployed|

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Data

Base on the following diagram, the forecast will utilize data from the respective tables:
- `PromotionCalendar`
- `DemandPlan`
- `AugmentedDemandPlan`
- `AugmentedWeeklySales`
- `DemandForecast`

| ![data-cortex-deploy-demand-sensing-data-flow][data-cortex-deploy-demand-sensing-data-flow] | 
|:--:| 
| *Data Cortex Demand Sensing Data Flow* |

<br/>

If `PromotionCalendar` and `DemandPlan` is not available, it can be created with the following commands (More information [here][data-cortex-setup-demand-sensing-external-tables]):

<br/>

**PromotionCalendar** <br/>
`NOTE:` Replace *{{ project_id_src }}* and *{{ dataset_cdc_process }}* with the `_PJID_SRC` and `_DS_CDC` respectively

```sql
CREATE TABLE IF NOT EXISTS `{{ project_id_src }}.{{ dataset_cdc_processed
}}.Promotion_Calendar`
(
    StartDateOfWeek DATE,
    CustomerId STRING,
    CatalogItemId STRING,
    IsPromo BOOLEAN,
    DiscountPercent FLOAT64
)
```

<br/>

**DemandPlan** <br/>
`NOTE:` Replace *{{ project_id_src }}* and *{{ dataset_cdc_process }}* with the `_PJID_SRC` and `_DS_CDC` respectively

```sql
CREATE TABLE IF NOT EXISTS `{{ project_id_src }}.{{ dataset_cdc_processed }}.Demand_Plan`
(
    WeekStart DATE,
    CustomerId STRING,
    CatalogItemId STRING,
    DemandPlan FLOAT64
)
```

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Configuration

Run the following command to spin up a container for `Data Cortex Demand Sensing` (More information [here][data-cortex-setup-demand-sensing-setting]):

```sh
gcloud auth configure-docker gcr.io && \
docker run -it --rm \
marketplace.gcr.io/cortex-public/cortex-demand-sensing:latest
```

<br/>

The following parameters will be prompted during the installation:

|#|Parameter|Description|Remarks|
|--|--|--|--|
|1|`Source GCP Project`|source project where data replication and CDC processing datasets are|`_PJID_SRC` parameter of Data Foundation deployment|
|2|`Target GCP Project`|The target project where Reporting views are (may be the same as the source depending on your deployment of the Data Foundation)| `_PJID_TGT` parameter of Data Foundation deployment|
|3|`Raw Landing Dataset`|BigQuery dataset where SAP data is replicated|`_DS_RAW` parameter of Data Foundation deployment|
|4|`CDC Processed Dataset`|BigQuery dataset where the CDC processed data is|`_DS_CDC` parameter of Data Foundation Deployment <br/> `Note:` The additional external tables are expected to be found here if not using test data|
|5|`Reporting Dataset`|The dataset for reporting used in the Data Foundation|`_DS_REPORTING` parameter of Data Foundation Deployment|
|6|`Models Dataset`|The dataset used for ML models in the Data Foundation|`_DS_MODELS` parameter of Data Foundation Deployment <br/> `Note:` These are not Demand Sensing models|
|7|`Vertex AI BigQuery Dataset`|This dataset is used for Vertex AI to stage processing|`Default:` *VERTEX_AI_DATA* <br/> `Note:` If it doesn’t exist, you can create one *after* configuring Demand Sensing deployment, but *before* executing the deployment|
|8|`Data Foundation BigQuery Location`|BigQuery location (multi-region) used with Data Foundation|`_LOCATION` parameter of Data Foundation Deployment <br/> `Default:` *US* if not specified, otherwise taken from `Raw Landing Dataset` *location*|
|9|`Vertex AI Region`|Region where the Vertex AI pipelines will run|`Note:` Must be a **single** region (e.g.us-central1), same as you used for Vertex AI bucket. Be sure to keep within the **same** multi-region location chosen for BigQuery|
|10|`Vertex AI service account`|Service Account to run Vertex AI jobs|`Default:` *cortex-ds-vertexai-sa* in the source project.<br/>`Note:` If it doesn’t exist, you can create one *after* configuring Demand Sensing deployment, but *before* executing the deployment|
|11|`Storage Bucket for Vertex AI`|GCS bucket for Vertex AI to store intermediate assets during model training and scoring|`Default:` Default value is a combination of the `source project id`, “*-cortex-ds-vertexai-*” string and Vertex AI *region*<br/>`Note:` If it doesn’t exist, you can create one *after* configuring Demand Sensing deployment, but *before* executing the deployment. This bucket must be created in the **region** specified as `Vertex AI Region`|
|12|`Forecast Horizon (weeks)`|How far in the future you want to make predictions, up to 13 weeks|`Default:` 13 weeks<br/>`Note:` Leave the default if using test data|
|13|`Context Window (weeks)`|How far back the model looks during training (and for forecasts)|`Default:` 52 weeks<br/>`Note:` Leave the default if using test data|
|14|`Model Training Budget (hours)`|Hours that Vertex AI AutoML will spend actually training the model|`Default:` 1 hour<br/>`Note:` Leave the default if using test data|
|15|`Deploy Test Data`||**yes** if you would like to use test data with Demand Sensing (More information [here][data-cortex-setup-demand-sensing-test-data])<br/> `NOTE:` This only works if the following parameters were used to setup `Data Foundation` <br/> *`TEST_DATA`=true,`_DEPLOY_CDC`=true,`_GEN_EXT`=true*|
|16|`Mandant/Client`||Use **900** for if test data is populated. Otherwise, use the right client for your source SAP system|

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Deployment

`NOTE:` After finishing Demand Sensing deployment configuration, your Cloud Shell will stay in the
context of running Demand Sensing container. `Do not` close it. You can execute all necessary CLI
commands from there (More information [here][data-cortex-setup-demand-sensing-configuration])


```sh
cd /usr/src/app
./ds-deploy
```
<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Verification

**Cloud Build Logs**

If deployment is *successful*, source codes will be saved into `Cloud Source Repositories` and the machine learning model training will be started in `Vertex AI` pipeline (More information [here][data-cortex-setup-demand-sensing-verification])

| ![data-cortex-deploy-demand-sensing-cloud-build][data-cortex-deploy-demand-sensing-cloud-build] | 
|:--:| 
| *Demand Sensing Cloud Build* |

<br/>

**Cloud Source Repositories**

If deployment is *successful*, the source code for `Demand Sensing` will be available in the `Cloud Source Repository` (More information [here][data-cortex-setup-demand-sensing-verification])
<br/>

| ![data-cortex-deploy-demand-sensing-source-repo][data-cortex-deploy-demand-sensing-source-repo] | 
|:--:| 
| *Demand Sensing Cloud Source Repository* |

<br/>

**Vertex AI Pipeline**

If deployment is *successful*, a process will kickstart the `Vertex AI` pipeline for training a *Forecasting* model and using it to make forecast prediction (More information [here][data-cortex-setup-demand-sensing-verification])

| ![data-cortex-deploy-demand-sensing-vertex-pipeline][data-cortex-deploy-demand-sensing-vertex-pipeline] | 
|:--:| 
| *Demand Sensing Vertex AI Pipeline* |

<br/>

**Demand Forecast Table**

If the training is *successful*, the forecasted data will be saved into the `Demand_Forecast` table (More information [here][data-cortex-setup-demand-sensing-forecast-table])

| ![data-cortex-deploy-demand-sensing-forecast-table][data-cortex-deploy-demand-sensing-forecast-table] | 
|:--:| 
| *Demand Sensing Forecast Table* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

<div id="looker-block-sap-setup"></div>

### Looker Block (SAP)

To install the `Looker` block for `SAP`, we will need to install using its [repository][data-cortex-block-sap-github]

<div id="looker-block-sap-hash"></div>

#### Looker Block Hash

Run the following command to get the **hash** required to install the tool in the `Looker` *marketplace*:

```sh
git clone https://github.com/looker-open-source/block-cortex-sap
cd block-cortex-sap
git rev-parse HEAD
```

| ![data-cortex-looker-sap-hash][data-cortex-looker-sap-hash] | 
|:--:| 
| *Looker Block (SAP) Git Hash* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Install Looker Block

Navigate to `Looker` > Select the *marketplace* icon > Select Manage > Click the 3 dots (**.**) > Select Install via Git > Input the following fields and click the *Install* button:
- `Git Repository URL`: https://github.com/looker-open-source/block-cortex-sap.git
- `Git Commit SHA`: b26c2e4a97032e1d8d074fef35bbb6aa7e1220ad

`NOTE:` Update `Git Commit SHA` accordingly based on the <a href="#looker-block-sap-hash">block hash</a>

| ![data-cortex-looker-marketplace][data-cortex-looker-marketplace] | 
|:--:| 
| ![data-cortex-looker-sap-marketplace][data-cortex-looker-sap-marketplace] | 
| *Looker Block (SAP) Marketplace* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

<div id="looker-block-salesforce-setup"></div>

### Looker Block (Salesforce)

To install the `Looker` block for `Salesforce`, we will need to install using its [repository][data-cortex-block-salesforce-github]

<div id="looker-block-salesforce-hash"></div>

#### Looker Block Hash

Run the following command to get the **hash** required to install the tool in the `Looker` *marketplace*:

```sh
git clone https://github.com/looker-open-source/block-cortex-salesforce
cd block-cortex-salesforce
git rev-parse HEAD
```

| ![data-cortex-looker-salesforce-hash][data-cortex-looker-salesforce-hash] | 
|:--:| 
| *Looker Block (Salesforce) Git Hash* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Install Looker Block

Navigate to `Looker` > Select the *marketplace* icon > Select Manage > Click the 3 dots (**.**) > Select Install via Git > Input the following fields and click the *Install* button:
- `Git Repository URL`: https://github.com/looker-open-source/block-cortex-salesforce.git
- `Git Commit SHA`: aea5aad74e1f8fd56b3ce96096861a82cb392744

`NOTE:` Update `Git Commit SHA` accordingly based on the <a href="#looker-block-salesforce-hash">block hash</a>

| ![data-cortex-looker-marketplace][data-cortex-looker-marketplace] | 
|:--:| 
| ![data-cortex-looker-salesforce-marketplace][data-cortex-looker-salesforce-marketplace] | 
| *Looker Block (Salesforce) Marketplace* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

<div id="looker-block-demand-sensing-setup"></div>

### Looker Block (Demand Sensing)

To install the `Looker` block for `Demand Sensing`, we will need to install using its [repository][data-cortex-block-demand-sensing-github]

<div id="looker-block-demand-sensing-hash"></div>

#### Looker Block Hash

Run the following command to get the **hash** required to install the tool in the `Looker` *marketplace*:

```sh
git clone https://github.com/looker-open-source/block-cortex-demand-sensing
cd block-cortex-demand-sensing
git rev-parse HEAD
```

| ![data-cortex-looker-demand-sensing-hash][data-cortex-looker-demand-sensing-hash] | 
|:--:| 
| *Looker Block (Demand Sensing) Git Hash* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Install Looker Block

Navigate to `Looker` > Select the *marketplace* icon > Select Manage > Click the 3 dots (**.**) > Select Install via Git > Input the following fields and click the *Install* button:
- `Git Repository URL`: https://github.com/looker-open-source/block-cortex-demand-sensing.git
- `Git Commit SHA`: 468a96f122d701ce2f748c08914815b6291c8c61

`NOTE:` Update `Git Commit SHA` accordingly based on the <a href="#looker-block-demand-sensing-hash">block hash</a>

| ![data-cortex-looker-marketplace][data-cortex-looker-marketplace] | 
|:--:| 
| ![data-cortex-looker-demand-sensing-marketplace][data-cortex-looker-demand-sensing-marketplace] | 
| *Looker Block (Demand Sensing) Marketplace* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

---

<!-- Model -->

## Model

In the event that the forecasting model need to be retrained, the following data will be used (More information [here][data-cortex-setup-model-overview]):

<br/>

|#|Training Data|Remarks|
|--|--|--|
|1|Weekly sales per product per customer location||
|2|Weather per customer location|`Note:` uses 16-days forecast from `NOAA_GFS0P25` dataset, plus *historical weekly climate* averages up to 13 weeks ahead|
|3|Google Trends average interest per product category per customer’s location region|`Note:` Product category is taken from a higher level of product hierarchy (T179 and T179T)|
|4|Holiday Calendar||
|5|Promotion Calendar with weekly promotions per product per customer||

<br/>

The following are some variables that need to be adjusted to finetune the model training:

<br/>

|#|Variable|Description|Remarks|
|--|--|--|--|
|1|`Forecast horizon`|how far in the future you want to make predictions, up to 13 weeks|`Default:` 13 weeks<br/>`Note:` While a longer horizon gives a great opportunity to make a forecast in advance, it reduces the overall quality of the model. Depending on your supply chain, manufacturing and delivery timing, you may want to reduce the horizon|
|2|`Context window`|sets how far back the model looks during training (and for forecasts)|`Default:` 52 weeks<br/>`Note:` With enough historical data, you can set the context window up to 5 times the size of the forecast horizon|
|3|`Training time budget`|how much Vertex AI AutoML will spend actually training the model|`Default:` 1 hour<br/>`Note:` . If the model quality is not great (**r^2 metric** below *0.85*), you may want to reduce the context window in half, and increase the training time up to *6* hours, then subsequently increasing the context window up to *5* times the size of `Forecast horizon`|

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

### Model Training

#### Data
The `AugmentedWeeklySales` view, which contains weekly sales and customer location augmented with Weather, Promotion, and Trends, will be used as training data for the forecasting model (More information [here][data-cortex-setup-model-data])

| ![data-cortex-model-training-data][data-cortex-model-training-data] |
|:--:|
| *Demand Sensing AugmentedWeeklySales Table* |

<br/>

The `AugmentedDemandPlan` view, which contains demand plan data augmented with Weather, Promotions, Holidays, will be used to score the trained model (More information [here][data-cortex-setup-model-validation])

| ![data-cortex-model-validation-data][data-cortex-model-validation-data] |
|:--:|
| *Demand Sensing AugmentedDemandPlan Table* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Parameters

The following are some of the common parameters for the model training (More information [here][data-cortex-setup-model-training]):

|#|Parameter|Description|Remarks|
|--|--|--|--|
|1|`--model-command`|Forecasting Model command, *train*, *score* or *train_score*|`Note:` For training, use *train* or *train_score*, which performs both training and scoring.|
|2|`--region`|Vertex AI GCP region||
|3|`--bigquery-location`|BigQuery Location, one you used with Data Foundation||
|4|`--pipeline-bucket`|Google Cloud Storage Bucket to use with Vertex AI when needed||
|5|`--vertex-ai-dataset`|BigQuery dataset to use for storing intermediate data|`Note:` Vertex AI processing dataset|
|6|`--vertex-ai-sa`|Service Account for Vertex AI to use |`Note:` Make sure you use the principal of the account, not its name|
|7|`--source-project`|Source Data GCP project|`Note:` CDC Processed dataset should be there|
|8|`--target-project`|Target GCP project|`Note:` Target Reporting dataset should be there|
|9|`--target-reporting-dataset`|Target Reporting dataset name||
|10|`--cdc-processed-dataset`|CDC Processed dataset name||
|11|`--forecast-horizon-weeks`|Vertex AI Forecasting Horizon||
|12|`--context-window-weeks`|Vertex AI Forecasting Context Window in weeks|`Default:` *52*<br/>`Note:` Optional|
|13|`--training-node-hours`|Vertex AI Forecasting Training budget in hours|`Default:` *1*<br/>`Note:` Optional|
|14|`--score-on-recent-model`|if present, makes the pipeline use the most recent model, not the best one||

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Training

The following command can be use to retrain the models (More information [here][data-cortex-setup-model-training])

```sh
python3 src/submit_pipeline.py \
    --model-command {train,train_score} \
    --region REGION \
    --bigquery-location LOCATION \
    --pipeline-bucket PIPELINE_BUCKET \
    --vertex-ai-dataset VERTEX_AI_DATASET
    --vertex-ai-sa VERTEX_AI_SA \
    --source-project SOURCE_PROJECT --target-project TARGET_PROJECT \
    --target-reporting-dataset TARGET_REPORTING_DATASET \
    --cdc-processed-dataset CDC_PROCESSED_DATASET \
    --forecast-horizon-weeks FORECAST_HORIZON_WEEKS \
    [--context-window-weeks CONTEXT_WINDOW_WEEKS] \
    [--training-node-hours TRAINING_NODE_HOURS]
```

<br/>

*Example* (for `training and scoring` model):
```sh
python3 src/submit_pipeline.py \
    --model-command train_score \
    --region us-central1 \
    --bigquery-location US \
    --pipeline-bucket bucket_vertex_ai \
    --vertex-ai-dataset cortex_ai \
    --vertex-ai-sa cortex-ds-vertexai-sa@<PROJECT_ID>.iam.gserviceaccount.com \
    --source-project <SOURCE_PROJECT_ID> \
    --target-project <DEST_PROJECT_ID> \
    --target-reporting-dataset cortex_reporting \
    --cdc-processed-dataset cortex_cdc \
    --forecast-horizon-weeks 14 \
    --context-window-weeks 52 \
    --training-node-hours 1
```

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Validation

Once the model is trained, it will be stored in the `Model Registry` in `Vertex AI` (More information [here][data-cortex-setup-model-validation])

| ![data-cortex-model-training-validation-a][data-cortex-model-training-validation-a] |
|:--:|

| ![data-cortex-model-training-validation-b][data-cortex-model-training-validation-b] |
|:--:|
| *Demand Sensing Model Validation* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

### Model Forecast

The following command can be use to evaluate the models (More information [here][data-cortex-setup-model-forecast]):

`NOTE:` The following is the expected behaviour when the command is executed 
- `Demand_Forecast` table will be truncated with each successful forecast
- Forecast is always made with best model as evaulated by r^2 (r-squared) metric

<br/>

```sh
python3 src/submit_pipeline.py \
    --model-command {score,train_score} \
    --region REGION \
    --bigquery-location LOCATION \
    --pipeline-bucket PIPELINE_BUCKET \
    --vertex-ai-dataset VERTEX_AI_DATASET
    --vertex-ai-sa VERTEX_AI_SA \
    --source-project SOURCE_PROJECT --target-project TARGET_PROJECT \
    --target-reporting-dataset TARGET_REPORTING_DATASET \
    --cdc-processed-dataset CDC_PROCESSED_DATASET \
    --forecast-horizon-weeks FORECAST_HORIZON_WEEKS \
    [--context-window-weeks CONTEXT_WINDOW_WEEKS] \
    [--training-node-hours TRAINING_NODE_HOURS] \
    [--score-on-recent-model]
```

<br/>

*Example* (for `scoring` model):
```sh
python3 src/submit_pipeline.py \
    --model-command score \
    --region us-central1 \
    --bigquery-location US \
    --pipeline-bucket bucket_vertex_ai \
    --vertex-ai-dataset cortex_ai \
    --vertex-ai-sa cortex-ds-vertexai-sa@<PROJECT_ID>.iam.gserviceaccount.com \
    --source-project <SOURCE_PROJECT_ID> \
    --target-project <DEST_PROJECT_ID> \
    --target-reporting-dataset cortex_reporting \
    --cdc-processed-dataset cortex_cdc \
    --forecast-horizon-weeks 14 \
    --context-window-weeks 52 \
    --training-node-hours 1
```

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Troubleshooting
In any event that the *scoring* is unsuccessful, the *errors* will be written to the `VERTEX_AI_DATASET`

| ![data-cortex-model-training-troubleshooting][data-cortex-model-training-troubleshooting] |
|:--:|
| *Demand Sensing Model Troubleshooting* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Verification

Otherwise, if the *scoring* is successful, the results will be written to the `Demand_Forecast` table


| ![data-cortex-deploy-demand-sensing-forecast-table][data-cortex-deploy-demand-sensing-forecast-table] |
|:--:|
| *Demand Sensing Model Forecast* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

---

<!-- USAGE EXAMPLES -->

## Usage

There are 3 modes to verify the implementations:
- Via `Looker` Block for `SAP`
- Via `Looker` Block for `Salesforce`
- Via `Looker` Block for `Demand Sensing`

<p align="right">(<a href="#top">back to top</a>)</p>

### Via Looker Block (SAP)
`NOTE:` Assuming that the `Looker` block for `SAP` has been setup, otherwise please see <a href="#looker-block-sap-setup">here</a> before proceeding. <br/>

The following are the dashboards available in the `Looker` block for `SAP` (More information [here][data-cortex-looker-block-sap-dashboards]):

|#|Dashboard Name|Category|Description|
|--|--|--|--|
|1|`Orders Fulfillment Dashboard`|**Order to Cash**|monitor current delivery status, highlight late deliveries and compare pending deliveries with current stock|
|2|`Order Snapshot Dashboard`|**Order to Cash**|monitor the health of the orders and also how efficient our Orders vs Deliveries|
|3|`Order Details`|**Order to Cash**|information about order in one place and their status|
|4|`Sales Performance`|**Order to Cash**|review the sales performance of Products, Division, Sales organization and Distribution channel|
|5|`Billing and Pricing`|**Order to Cash**|information related to the customer and products focused on price variations|
|6|`Accounts Receivable Dashboard`|**Finance**|information regarding the companies' finance <br/> `Example:` Accounts Receivable, Overdue Receivables, Day Sales Outstanding|
|7|`Accounts Payable Dashboard`|**Finance**|information regarding the companies' finance <br/> `Example:` Accounts Payables, Accounts Payalable Turnover, Overdue Payables, Accounts Payable Aging and Cash Discount Utilization|
|8|`Vendor Performance Dashboard`|**Finance**|information regarding the Vendor Performance in terms of delivery and other important indicators <br/> `Example:` Vendor Lead time , Purchase price variance , Purchase Order status|
|9|`Spend Analysis Dashboard`|**Finance**|information regarding the major indicators <br/> `Example:` Total Spend, Spend Analysis, Total number of Suppliers to check spend across different Purchase orgs, Purchase groups, Vendor Countries, Material Types|
|10|`Inventory Management Dashboard`|**Inventory**|information about various stock categories and other important Key Performance Indicators <br/> `Example:` Inventory Turn, Days of Supply, Obsolete Inventory and Slow Moving Inventory|

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Dashboards

Once installed, the dashboards will be accessible under Blocks > *Cortex Data Foundation for SAP - Operational Dashboards*

| ![data-cortex-looker-sap-dashboards][data-cortex-looker-sap-dashboards] | 
|:--:| 
| *Looker Block (SAP) Dashboards* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### LookML Configuration

Once installed, there will be a *project_spec.lkml* that will provide the overriding of the following configurations (More information [here][data-cortex-looker-block-sap-customizations]):

|#|Configuration|Description|
|--|--|--|
|1|`Connection`|In the manifest.lkml file, update the value of the CONNECTION_NAME constant and Client ID|
|2|`GCP Project`|GCP project name where the SAP reporting dataset resides in BigQuery|
|3|`Reporting Dataset`|The deployed Cortex Data Foundation _REPORTING dataset where the SAP views reside within the GCP BigQuery project|
|4|`ClientId/Constant:`|The SAP Client number (mandt) the dashboards will utilize to display data|

<br/>

| ![data-cortex-looker-sap-customization][data-cortex-looker-sap-customization] | 
|:--:| 
| *Looker Block (SAP) Customization* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

### Via Looker Block (Salesforce)

`NOTE:` Assuming that the `Looker` block for `Salesforce` has been setup, otherwise please see <a href="#looker-block-salesforce-setup">here</a> before proceeding. <br/>

The following are the dashboards available in the `Looker` block for `Salesforce` (More information [here][data-cortex-looker-block-salesforce-dashboards]):

|#|Dashboard Name|Description|
|--|--|--|
|1|`Leads Capture & Conversion`|Understand leads generated from marketing activities, current status, and how quickly sales are converting to opportunities|
|2|`Opportunity Trends & Pipeline`|Identify open opportunities and their current stage, which are likely to close and how well sales teams are converting to wins|
|3|`Sales Activities & Engagement`|Highlight leads and opportunities which need immediate follow up and understand overall engagement trends across sales|
|4|`Case Overview & Trends`|Understand overall customer service position across all open cases and case trends|
|5|`Case Management & Resolution`|Get a pulse check on how efficient the service team is at case resolution and highlight any long running open cases for prioritization|
|6|`Accounts with Cases`|Identify which accounts have open cases and which need immediate focus|

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Dashboards

Once installed, the dashboards will be accessible under Blocks > *Cortex Data Foundation for Salesforce*

| ![data-cortex-looker-salesforce-dashboards][data-cortex-looker-salesforce-dashboards] | 
|:--:| 
| *Looker Block (Salesforce) Dashboards* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### LookML Configuration

Once installed, there will be a *project_spec.lkml* that will provide the overriding of the following configurations (More information [here][data-cortex-looker-block-salesforce-customizations]):

|#|Configuration|Description|
|--|--|--|
|1|`Connection`|In the manifest.lkml file, update the value of the CONNECTION_NAME constant and Client ID|
|2|`GCP Project`|GCP project name where the SAP reporting dataset resides in BigQuery|
|3|`SFDC Dataset`|The deployed Cortex Data Foundation _REPORTING dataset where the Salesforce views reside within the GCP BigQuery project|

<br/>

| ![data-cortex-looker-salesforce-customization][data-cortex-looker-salesforce-customization] |
|:--:|
| *Looker Block (Salesforce) Customization* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

### Via Looker Block (Demand Sensing)

`NOTE:` Assuming that the `Looker` block for `Demand Sensing` has been setup, otherwise please see <a href="#looker-block-demand-sensing-setup">here</a> before proceeding. <br/>

The following are the dashboards available in the `Looker` block for `Demand Sensing` (More information [here][data-cortex-looker-block-demand-sensing-dashboards]):

|#|Dashboard Name|Description|
|--|--|--|
|1|`Demand Sensing Summary Dashboard`|provides the Alerts details on the quantity of sales, for a particular period of time frame, Ship to location, product name and customer based on the weather forecast which is outside the statistical range|
|2|`Demand Sensing Temperature`|provides the Alerts details on the quantity of sales, for a particular period of time frame, Ship to location, product name and customer based on the weather forecast which is specific to temperature|
|3|`Demand Sensing Trends`|provides the Alerts details on the quantity of sales, for a particular period of time frame, Ship to location, product name and customer based on the Customer Trends. This trends dashboard shows us about the quantity of trends occurring in various insights of products|
|4|`Demand Sensing Forecast Outside Statistical Range`|provides the Alerts details on the quantity of sales, for a particular period of time frame, Ship to location, product name and customer based on the forecast which is outside the statistical range|
|5|`Demand Sensing Promo Differential`|provides the Alerts details on the quantity of sales, for a particular period of time frame, Ship to location, product name and customer Promo Differential shows us about the retail prices of products per unit in Syndicated Point-of-Sale Data|

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Dashboards

Once installed, the dashboards will be accessible under Blocks > *Cortex Data Foundation for Demand Sensing Dashboards*

| ![data-cortex-looker-demand-sensing-dashboards][data-cortex-looker-demand-sensing-dashboards] |
|:--:|
| *Looker Block (Demand Sensing) Dashboards* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### LookML Configuration

Once installed, there will be a *project_spec.lkml* that will provide the overriding of the following configurations (More information [here][data-cortex-looker-block-demand-sensing-customizations]):

|#|Configuration|Description|
|--|--|--|
|1|`Connection`|In the manifest.lkml file, update the value of the CONNECTION_NAME constant and Client ID|
|2|`GCP Project`|GCP project name where the SAP reporting dataset resides in BigQuery|
|3|`Reporting Dataset`|The deployed Cortex Data Foundation _REPORTING dataset where the SAP views reside within the GCP BigQuery project|
|4|`CliendId`|Input the Client ID from the dataset|
|3|`Years_Past_Data`|Control the number of years of data displayed on the dashboard|

<br/>

| ![data-cortex-looker-demand-sensing-customization][data-cortex-looker-demand-sensing-customization] | 
|:--:| 
| *Looker Block (Demand Sensing) Customization* |
<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

---

<!-- Challenges -->
## Challenges

The following are some challenges encountered:
- AI Pipeline for Demand Sensing was not running successfully
<p align="right">(<a href="#top">back to top</a>)</p>

### Challenge #1: AI Pipeline for Demand Sensing was not running successfully

**Observations**

Encountered the error messages  - There can be no more than 64 labels attached to a single resource. Label keys and values can only contain lowercase letters, numbers, dashes and underscores. Label keys must start with a letter or number, must be less than 64 characters in length, and must be less that 128 bytes in length when encoded in UTF-8. Label values must be less than 64 characters in length, and must be less that 128 bytes in length when encoded in UTF-8.

<br/>

**Resolution**

Seek help from [github][ref-cortex-demand-sensing-github-issue] and was able to resolve the issue by modifying the source code *forcasting_pipeline.py* The issue is most likely cause by additional labels that hinder the model training process.

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

---

<!-- ACKNOWLEDGMENTS -->

## Acknowledgments

- [Data Cortex Foundation Overview][data-cortex-foundation-overview]
- [Data Cortex Foundation Github][data-cortex-foundation-github]
- [Data Cortex Block SAP Github][data-cortex-block-sap-github]
- [Data Cortex Block Salesforce Github][data-cortex-block-salesforce-github]
- [Data Cortex Block Demand Sensing Github][data-cortex-block-demand-sensing-github]

- [Data Cortex Foundation Deployment Video][ref-cortex-deployment-video]
- [Data Cortex Demand Sensing Deployment Guide][ref-cortex-demand-sensing-deployment-guide]

- [BigQuery Create Dataset][ref-bigquery-create-dataset]
- [Install Looker Block from Git][ref-looker-install-tool-from-git]
- [Demand Sensing Github Issue][ref-cortex-demand-sensing-github-issue]
- [Skillsboost Lab for Data Cortex][ref-cortex-sap-skillsboost-lab]

- [Readme Template][template-resource]

<p align="right">(<a href="#top">back to top</a>)</p>

---

<!-- MARKDOWN LINKS & IMAGES -->
[template-resource]: https://github.com/othneildrew/Best-README-Template/blob/master/README.md

[ref-bigquery-create-dataset]: https://cloud.google.com/bigquery/docs/datasets#bq
[ref-looker-install-tool-from-git]: https://cloud.google.com/looker/docs/marketplace#installing_a_tool_from_a_git_url:~:text=from%20the%20Marketplace.-,Installing%20a%20tool%20from%20a%20Git%20URL,-You%20can%20also
[ref-cortex-demand-sensing-github-issue]: https://github.com/GoogleCloudPlatform/cortex-data-foundation/issues/27
[ref-cortex-deployment-video]: https://www.youtube.com/watch?v=pxfxOYPQw9E
[ref-cortex-demand-sensing-deployment-guide]: https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf
[ref-cortex-sap-skillsboost-lab]: https://www.cloudskillsboost.google/quests/211

[data-cortex-foundation-overview]: https://cloud.google.com/solutions/cortex
[data-cortex-foundation-github]: https://github.com/GoogleCloudPlatform/cortex-data-foundation
[data-cortex-block-sap-github]: https://github.com/looker-open-source/block-cortex-sap
[data-cortex-block-salesforce-github]: https://github.com/looker-open-source/block-cortex-salesforce
[data-cortex-block-demand-sensing-github]: https://github.com/looker-open-source/block-cortex-demand-sensing

[looker-install-tool-from-git]: https://cloud.google.com/looker/docs/marketplace#installing_a_tool_from_a_git_url

[data-cortex-foundation-architecture]: ./images/data-cortex-foundation-overview.png
[data-cortex-demand-sensing-architecture]: ./images/data-cortex-demand-sensing-overview.png

[data-cortex-setup-api-services]: https://github.com/GoogleCloudPlatform/cortex-data-foundation#enable-required-components:~:text=Enable%20Required%20Components
[data-cortex-setup-service-account-permission]: https://github.com/GoogleCloudPlatform/cortex-data-foundation#enable-required-components:~:text=Grant%20permissions%20to%20the%20executing%20user
[data-cortex-setup-service-account-impersonation]: https://github.com/GoogleCloudPlatform/cortex-data-foundation#optional-create-a-service-account-for-deployment-with-impersonation:~:text=%5BOptional%5D%20Create%20a%20Service%20Account%20for%20deployment%20with%20impersonation
[data-cortex-setup-cloud-bucket]: https://github.com/GoogleCloudPlatform/cortex-data-foundation#enable-required-components:~:text=iam.serviceAccountTokenCreator%22-,Create%20a%20Storage%20bucket,-A%20storage%20bucket
[data-cortex-setup-prerequisite-check]: https://github.com/GoogleCloudPlatform/cortex-data-foundation#enable-required-components:~:text=Setup-,Check%20setup,-You%20can%20now
[data-cortex-setup-prerequisite-parameters]: https://github.com/GoogleCloudPlatform/cortex-data-foundation#enable-required-components:~:text=Parameters%20for%20Checker

[data-cortex-setup-foundation]: https://github.com/GoogleCloudPlatform/cortex-data-foundation#enable-required-components:~:text=Clone%20the%20Data%20Foundation%20repository
[data-cortex-setup-foundation-parameters]: https://github.com/GoogleCloudPlatform/cortex-data-foundation#enable-required-components:~:text=Gather%20the%20parameters%20for%20deployment
[data-cortex-setup-composer-connection]: https://github.com/GoogleCloudPlatform/cortex-data-foundation#:~:text=the%20target%20table.-,Gathering%20Cloud%20Composer%20Settings,-If%20Cloud%20Composer
[data-cortex-setup-looker-pdts]: https://github.com/looker-open-source/block-cortex-sap#:~:text=Other%20considerations
[data-cortex-setup-looker-user-attributes]: https://github.com/looker-open-source/block-cortex-sap#:~:text=to%20display%20data.-,User%20Attributes,-%E2%9D%95
[data-cortex-setup-looker-locale]: https://cloud.google.com/looker/docs/model-localization#assigning_users_to_a_locale

[data-cortex-setup-model-prerequisite]: https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf#page=5
[data-cortex-setup-demand-sensing-parameters]: https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf#page=6
[data-cortex-setup-demand-sensing-service-account-permission]: https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf#page=7
[data-cortex-setup-demand-sensing-external-tables]:https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf#page=8
[data-cortex-setup-demand-sensing-setting]: https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf#page=11
[data-cortex-setup-demand-sensing-test-data]: https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf#page=12
[data-cortex-setup-demand-sensing-configuration]: https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf#page=14
[data-cortex-setup-demand-sensing-deploy]: https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf#page=15
[data-cortex-setup-demand-sensing-cloud-build]: https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf#page=16
[data-cortex-setup-demand-sensing-verification]: https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf#page=17
[data-cortex-setup-demand-sensing-forecast-table]: https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf#page=18

[data-cortex-setup-model-overview]: https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf#page=24
[data-cortex-setup-model-data]: https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf#page=25
[data-cortex-setup-model-training]: https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf#page=26
[data-cortex-setup-model-validation]: https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf#page=28
[data-cortex-setup-model-forecast]: https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf#page=28
[data-cortex-setup-model-monitoring]: https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf#page=31

[data-cortex-deploy-data-foundation-setting]: ./images/data-cortex-data-foundation-setting.png
[data-cortex-deploy-data-foundation-service-account]: ./images/data-cortex-cloud-build-sa.png
[data-cortex-deploy-data-foundation-service-account-permissions]: ./images/data-cortex-cloud-build-sa-permissions.png

[data-cortex-deploy-demand-sensing-database-setting]: ./images/data-cortex-demand-sensing-setting.png
[data-cortex-deploy-demand-sensing-data-flow]: ./images/data-cortex-demand-sensing-data-flow.png

[data-cortex-deploy-demand-sensing-cloud-build]: ./images/data-cortex-demand-sensing-cloud-build.png
[data-cortex-deploy-demand-sensing-source-repo]: ./images/data-cortex-demand-sensing-source-repository.png
[data-cortex-deploy-demand-sensing-vertex-pipeline]: ./images/data-cortex-demand-sensing-vertex-pipeline.png
[data-cortex-deploy-demand-sensing-forecast-table]: ./images/data-cortex-demand-sensing-forecast-data.png

[data-cortex-looker-attributes-default-currency]: ./images/data-cortex-looker-attributes-default-currency.png
[data-cortex-looker-attributes-client-id]: ./images/data-cortex-looker-attributes-client-id.png

[data-cortex-cloud-composer-dependencies]: ./images/data-cortex-composer-dependencies.png
[data-cortex-cloud-composer-dags]: ./images/data-cortex-composer-dags.png
[data-cortex-cloud-composer-connections]: ./images/data-cortex-composer-connections.png

[data-cortex-looker-bigquery-sa-key]: ./images/data-cortex-looker-bigquery-sa-key.png
[data-cortex-looker-bigquery-connection-a]: ./images/data-cortex-looker-bigquery-connection-a.png
[data-cortex-looker-bigquery-connection-b]: ./images/data-cortex-looker-bigquery-connection-b.png

[data-cortex-looker-block-sap-dashboards]: https://github.com/looker-open-source/block-cortex-sap#:~:text=What%20does%20this%20Looker%20Block%20do%20for%20me%3F
[data-cortex-looker-block-salesforce-dashboards]: https://github.com/looker-open-source/block-cortex-salesforce#:~:text=What%20insights%20are%20possible%3F
[data-cortex-looker-block-demand-sensing-dashboards]: https://github.com/looker-open-source/block-cortex-demand-sensing#:~:text=What%20does%20this%20Looker%20Block%20do%20for%20me%3F

[data-cortex-looker-block-sap-customizations]: https://github.com/looker-open-source/block-cortex-sap#:~:text=Required%20Customizations
[data-cortex-looker-block-salesforce-customizations]: https://github.com/looker-open-source/block-cortex-salesforce#:~:text=Required%20Customizations
[data-cortex-looker-block-demand-sensing-customizations]: https://github.com/looker-open-source/block-cortex-demand-sensing#:~:text=What%20does%20this%20Looker%20Block%20do%20for%20me%3F

[data-cortex-looker-sap-hash]: ./images/data-cortex-looker-sap-hash.png
[data-cortex-looker-salesforce-hash]: ./images/data-cortex-looker-salesforce-hash.png
[data-cortex-looker-demand-sensing-hash]: ./images/data-cortex-looker-salesforce-hash.png

[data-cortex-looker-marketplace]: ./images/data-cortex-looker-marketplace.png
[data-cortex-looker-sap-marketplace]: ./images/data-cortex-looker-sap-marketplace.png
[data-cortex-looker-salesforce-marketplace]: ./images/data-cortex-looker-salesforce-marketplace.png
[data-cortex-looker-demand-sensing-marketplace]: ./images/data-cortex-looker-demand-sensing-marketplace.png

[data-cortex-looker-sap-dashboards]: ./images/data-cortex-looker-sap-dashboards.png
[data-cortex-looker-salesforce-dashboards]: ./images/data-cortex-looker-salesforce-dashboards.png
[data-cortex-looker-demand-sensing-dashboards]: ./images/data-cortex-looker-demand-sensing-dashboards.png

[data-cortex-looker-sap-customization]: ./images/data-cortex-looker-sap-customization.png
[data-cortex-looker-salesforce-customization]: ./images/data-cortex-looker-salesforce-customization.png
[data-cortex-looker-demand-sensing-customization]: ./images/data-cortex-looker-demand-sensing-customization.png

[data-cortex-model-training-data]: ./images/data-cortex-demand-sensing-model-data.png
[data-cortex-model-training-validation-a]: ./images/data-cortex-demand-sensing-model-validation-a.png
[data-cortex-model-training-validation-b]: ./images/data-cortex-demand-sensing-model-validation-b.png
[data-cortex-model-validation-data]: ./images/data-cortex-demand-sensing-validation-data.png
[data-cortex-model-training-troubleshooting]: ./images/data-cortex-demand-sensing-troubleshooting.png
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
            <li><a href="#demand-sensing">Demand Sensing</a></li>
            <li><a href="#looker-block-sap">Looker Block (SAP)</a></li>
            <li><a href="#looker-block-salesforce">Looker Block(Salesforce)</a></li>
            <li><a href="#looker-block-demand-sensing">Looker Block (Demand Sensing)</a></li>
        </ul>
    </li>
    <li>
        <a href="#model">Model</a>
        <ul>
            <li><a href="#model-training">Model Training</a></li>
            <li><a href="#model-validation">Model Validation</a></li>
            <li><a href="#model-forecast">Model Forecast</a></li>
            <li><a href="#model-monitoring">Model Monitoring</a></li>
        </ul>
    </li>
    <li><a href="#usage">Usage</a>
        <ul>
            <li><a href="#via-looker-block-sap">Via Looker Block (SAP)</a></li>
            <li><a href="#via-looker-block-salesforce">Via Looker Block (Salesforce)</a></li>
            <li><a href="#via-looker-block-demand-sensing">Via Looker Block (Demand Sensing)</a></li>
        </ul>
    </li>
    <li><a href="#challenges">Challenges</a></li>
    <li><a href="#acknowledgments">Acknowledgments</a></li>
</ol>
<!-- </details> -->

---

<!-- ABOUT THE PROJECT -->

## About The Project

This project is created to showcase how to integrate 3rd party SAS (E.g SAP) into `Google Cloud` using the `Google Cloud Cortex Framework`.

The following are some of the requirements:

- Setup `Google Cloud Cortex Framework` in `Google Cloud` Platform
- Setup `Looker` visualizations for data stored in `BigQuery`

<p align="right">(<a href="#top">back to top</a>)</p>

---

<!-- Architecture -->

## Architecture

Base on the requirements, the following are the architectures involved in the `Google Cloud Cortex Framework`:
- `Data Foundation Architecture` - Connect with 3rd party applications and provide quicker insights for the data
- ``

<p align="right">(<a href="#top">back to top</a>)</p>

### Data Foundation Architecture

The architecture allow for easy connectivity of data from 3rd party applications (E.g SAP) into `BigQuery` and provide templates for data processing and visualization to allow user to have quicker insights on their data. (More information [here][data-cortex-foundation-overview])

| ![data-cortex-foundation-architecture][data-cortex-foundation-architecture] | 
|:--:| 
| *Data Cortex Foundation Architecture* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

### Demand Sensing Architecture

The architecture allow for analysing the data in `BigQuery` to dervie a demand plan via machine learning models to allow user to gain new insights and highlight potential impacts in the near term. (More information [here][])

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

Grant the following permissions to the `Cloud Build` service account (More information [here][data-cortex-setup-service-account-permission]):
- Service Usage Consumer
- Storage Object Creator
- Storage Object Viewer for the `Cloud Build` default bucket or bucket for logs
- Object Admin for `Cloud Build` logs
- Object Writer to the output buckets
- Cloud Build Editor
- Project Viewer or Storage Object Viewer
- BigQuery Data Editor
- BigQuery Job User

| ![data-cortex-cloud-build-service-account][data-cortex-cloud-build-service-account] | 
|:--:| 
| *Cloud Build Service Account* |

<br/>

`NOTE:` While the image shows that the *service account* is provided with *admin* rights, the above mentioned privileges should suffice.
| ![data-cortex-cloud-build-service-account-permissions][data-cortex-cloud-build-service-account-permissions] | 
|:--:| 
| *Cloud Build Service Account Permissions* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

### BigQuery

Create the following dataset for the data migration from 3rd party application (E.g `SAP`, `Salesforce`) into `BigQuery`: <br/>
`NOTE`: The region of the dataset should be the same as the buckets in `Cloud Storage`

- `CDC_DATASET` - Dataset that works as a source for the reporting views, and target for the records processed DAGs
- `RAW_DATASET` - Used by the CDC process, this is where the replication tool lands the data from SAP
- `REPORT_DATASET` - Name of the dataset that is accessible to end users for reporting, where views and user-facing tables are deployed
- `MODEL_DATASET` - Name of the dataset that stages results of Machine Learning algorithms or BQML models
- `LOOKER_PDT_DATASET` - Dataset that allows persistence data for Looker

```bash
bq --location=<REGION> mk -d <TARGET_PROJECT>.<CDC_DATASET>
bq --location=<REGION> mk -d <TARGET_PROJECT>.<RAW_DATASET>
bq --location=<REGION> mk -d <TARGET_PROJECT>.<REPORT_DATASET>
bq --location=<REGION> mk -d <TARGET_PROJECT>.<MODEL_DATASET>
bq --location=<REGION> mk -d <TARGET_PROJECT>.<LOOKER_PDT_DATASET>
```

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

### Cloud Storage

Replace *REGION*, *DAG_TEMP_BUCKET*, *LOG_BUCKET* to create buckets in the **same region** as the `BigQuery` dataset for the following usage (More information [here][data-cortex-setup-cloud-bucket]): <br/>
`NOTE`: The region of the buckets should be the same as the datasets in `BigQuery`

- `LOG_BUCKET` - Store the logs for the `Cloud Build` process
- `DAG_TEMP_BUCKET` - Store the generated scripts generated for `Cloud Composer`

```bash
gsutil mb -l <REGION> gs://<LOG_BUCKET>
gsutil mb -l <REGION> gs://<DAG_TEMP_BUCKET>
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
- `Name` - `default_value_currency_required`
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
- `Name` - `client_id_rep`
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

There is a checker to check fo the readiness of the environment by performing the following actions:
- List the given bucket
- Write to the bucket
- Create a BQ dataset
- Create a BQ table in the dataset
- Write a test record

<br/>

Replace *SOURCE_PROJECT*, *DAG_TEMP_BUCKET*, *LOG_BUCKET* to check for any outstanding configurations before deploying the `Google Cloud Cortex Framework` (More information [here][data-cortex-setup-prerequisite-check]):

`NOTE: ` There a (`.`) at the end of the command. Additional [configurations][data-cortex-setup-prerequisite-parameters] can be set for the prerequisite check.

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

- `Data Foundation` - Deploy the `Google Cloud Cortex Framework` into the project
- `Demand Sensing` - Using the data from `BigQuery` to forecast usage
- `Looker Block (SAP)` - The dashboards for `SAP` related data
- `Looker Block (Salesforce)` - The dashboards for `Salesforce` related data
- `Looker Block (Demand Sensing)` - The dashboards to predict future usage

<p align="right">(<a href="#top">back to top</a>)</p>

### Data Foundation

#### Setup

Run the following command to get the setup for `Google Cloud Cortex Framework`:

```sh
git clone --recurse-submodules https://github.com/GoogleCloudPlatform/cortex-data-foundation
cd cortex-data-foundation
```

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Settings

The table mappings and intervals for the generated dags can be found in the *setting.yaml*

| ![data-cortex-data-foundation-setting][data-cortex-data-foundation-setting] | 
|:--:| 
| *Data Foundation Dag Settings* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Parameters
The following are some of the common parameters for the deployment (More information [here][data-cortex-setup-deploy-foundation-parameters]):

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

Replace *SOURCE_PROJECT*, *TARGET_PROJECT*, *CDC_DATASET*, *RAW_DATASET*, *REPORT_DATASET*,*MODEL_DATASET*, *LOG_BUCKET*, *DAG_TEMP_BUCKET* to deploy `Google Cloud Cortex Framework` into the project (More information [here][data-cortex-setup-deploy-foundation]) <br/>
`NOTE:` Run the following command in the *cortex-data-foundation* folder

```sh
gcloud builds submit \
    --project <SOURCE_PROJECT> \
    --substitutions _PJID_SRC=<SOURCE_PROJECT>,_PJID_TGT=<TARGET_PROJECT>,_DS_CDC=<CDC_DATASET>,_DS_RAW=<RAW_DATASET>,_DS_REPORTING=<REPORT_DATASET>,_DS_MODELS=<MODEL_DATASET>,_DAG_TEMP_BUCKET=<LOG_BUCKET>,_TGT_BUCKET=<DAG_TEMP_BUCKET>,_TEST_DATA=true,_DEPLOY_CDC=true,_GEN_EXT=true,_DEPLOY_SAP=true,_DEPLOY_SFDC=true
```

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

#### Dag Migration

After successful deployment, you can migrate the generated dags in the `DAG_TEMP_BUCKET` into the `Cloud Composer` dag folder (`DAG_BUCKET`)

```sh
gsutil cp -r  gs://<DAG_TEMP_BUCKET>/dags* gs://<DAG_BUCKET>/dags
gsutil cp -r  gs://<DAG_TEMP_BUCKET>/data/* gs://<DAG_BUCKET>/data/
```

Wait for awhile and verify that the dags are now in the `Cloud Composer`

| ![data-cortex-cloud-composer-dags][data-cortex-cloud-composer-dags] | 
|:--:| 
| *Cloud Composer Data Foundation Dags* |

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

### Demand Sensing

`NOTE:` This is an optional step, however if you want to install the `Looker` block for `Demand Sensing`, the you will need to install `Demand Sensing`
<br/>

Once `Data Foundation` is installed,  you will be able to make use of the data in `BigQuery` for analytics purposes such as forcasting (More information [here][ref-cortex-demand-sensing-deployment-guide])

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

<div id="looker-block-sap-setup"></div>

### Looker Block (SAP)

To install the `Looker` block for `SAP`, we will need to install using its [repository][data-cortex-block-sap-github]

<br/>

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

<br/>

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

<br/>

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

### Model Training

(More information [here][data-cortex-model-training])

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

### Model Validation

(More information [here][data-cortex-model-validation])

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

### Model Forecast

(More information [here][data-cortex-model-forecast])

<br/>

<p align="right">(<a href="#top">back to top</a>)</p>

### Model Monitoring

(More information [here][data-cortex-model-monitoring])

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

Seek help from [github][ref-demand-sensing-github-issue] and was able to resolve the issue by modifying the source code *forcasting_pipeline.py* The issue is most likely cause by additional labels that hinder the model training process.

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
- [Demand Sensing Github Issue][ref-demand-sensing-github-issue]

- [Readme Template][template-resource]

<p align="right">(<a href="#top">back to top</a>)</p>

---

<!-- MARKDOWN LINKS & IMAGES -->
[template-resource]: https://github.com/othneildrew/Best-README-Template/blob/master/README.md

[ref-bigquery-create-dataset]: https://cloud.google.com/bigquery/docs/datasets#bq
[ref-looker-install-tool-from-git]: https://cloud.google.com/looker/docs/marketplace#installing_a_tool_from_a_git_url:~:text=from%20the%20Marketplace.-,Installing%20a%20tool%20from%20a%20Git%20URL,-You%20can%20also
[ref-demand-sensing-github-issue]: https://github.com/GoogleCloudPlatform/cortex-data-foundation/issues/27
[ref-cortex-deployment-video]: https://www.youtube.com/watch?v=pxfxOYPQw9E
[ref-cortex-demand-sensing-deployment-guide]: https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf

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
[data-cortex-setup-cloud-bucket]: https://github.com/GoogleCloudPlatform/cortex-data-foundation#enable-required-components:~:text=iam.serviceAccountTokenCreator%22-,Create%20a%20Storage%20bucket,-A%20storage%20bucket
[data-cortex-setup-prerequisite-check]: https://github.com/GoogleCloudPlatform/cortex-data-foundation#enable-required-components:~:text=Setup-,Check%20setup,-You%20can%20now
[data-cortex-setup-prerequisite-parameters]: https://github.com/GoogleCloudPlatform/cortex-data-foundation#enable-required-components:~:text=Parameters%20for%20Checker
[data-cortex-setup-deploy-foundation]: https://github.com/GoogleCloudPlatform/cortex-data-foundation#enable-required-components:~:text=Clone%20the%20Data%20Foundation%20repository
[data-cortex-setup-deploy-foundation-parameters]: https://github.com/GoogleCloudPlatform/cortex-data-foundation#enable-required-components:~:text=Gather%20the%20parameters%20for%20deployment
[data-cortex-setup-composer-connection]: https://github.com/GoogleCloudPlatform/cortex-data-foundation#:~:text=the%20target%20table.-,Gathering%20Cloud%20Composer%20Settings,-If%20Cloud%20Composer
[data-cortex-setup-looker-pdts]: https://github.com/looker-open-source/block-cortex-sap#:~:text=Other%20considerations
[data-cortex-setup-looker-user-attributes]: https://github.com/looker-open-source/block-cortex-sap#:~:text=to%20display%20data.-,User%20Attributes,-%E2%9D%95
[data-cortex-setup-looker-locale]: https://cloud.google.com/looker/docs/model-localization#assigning_users_to_a_locale

[data-cortex-model-training]: https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf#page=26
[data-cortex-model-validation]: https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf#page=27
[data-cortex-model-forecast]: https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf#page=28
[data-cortex-model-monitoring]: https://storage.googleapis.com/cortex-public-documents/Cortex%20Demand%20Sensing%20-%20User%20Guide.pdf#page=31

[data-cortex-cloud-build-service-account]: ./images/data-cortex-cloud-build-sa.png
[data-cortex-cloud-build-service-account-permissions]: ./images/data-cortex-cloud-build-sa-permissions.png

[data-cortex-cloud-composer-dependencies]: ./images/data-cortex-composer-dependencies.png
[data-cortex-cloud-composer-dags]: ./images/data-cortex-composer-dags.png
[data-cortex-cloud-composer-connections]: ./images/data-cortex-composer-connections.png

[data-cortex-looker-bigquery-sa-key]: ./images/data-cortex-looker-bigquery-sa-key.png
[data-cortex-looker-bigquery-connection-a]: ./images/data-cortex-looker-bigquery-connection-a.png
[data-cortex-looker-bigquery-connection-b]: ./images/data-cortex-looker-bigquery-connection-b.png

[data-cortex-looker-attributes-default-currency]: ./images/data-cortex-looker-attributes-default-currency.png
[data-cortex-looker-attributes-client-id]: ./images/data-cortex-looker-attributes-client-id.png

[data-cortex-data-foundation-setting]: ./images/data-cortex-data-foundation-setting.png

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
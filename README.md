# Udacity nd00333 Project 2: Operationalizing Machine Learning

This project is part of the requirements for the Udacity Machine Learning Engineer with Azure Nanodegree Program.  The Bank Marketing dataset was used in Azure Machine Learning, to configure, deploy, and consume a cloud-based production model. A pipeline was created, published, and consumed using the Azure ML SDK for Python.


## Table of Contents

* [Architectural Diagram](#arch-diagram)
* [Key Steps](#key-steps)
    * [Authentication](#authentication)
    * [Automated ML Experiment](#automl-exp)
    * [Deploy the Best Model](#deploy-best-model)
    * [Enable Logging (Application Insights)](#app-insights)
    * [Swagger Documentation](#swagger)
    * [Consume Model Endpoints](#consume-enpoints)
    * [Create, Publish and Consume a Pipeline](#prog-pipeline)
* [Screencast / Documentation](#screencast)
* [Standout Suggestions](#future-work)
* [References](#refs)






## Architectural Diagram <a name="arch-diagram"></a>

![Architectural Diagram](/screenshots/architectural_diagram.png)

## Key Steps <a name="key-steps"></a>


### Step 1: Authentication <a name="authentication"></a>

I'm using own Azure account, so I needed to install the `az` command (Azure CLI) and the Azure Machine Learning Extension (which allows to interact with Azure Machine Learning Studio, part of the `az` command). Then, a **Service Principal** account was created and associated with the current workspace. Below are the short screencasts to document these steps:

```
az

az extension list

az login

az ad sp create-for-rbac --sdk-auth --name ml-auth
```
[![az login and create SP](https://img.youtube.com/vi/1NdR1J7hDfg/0.jpg)](https://www.youtube.com/watch?v=1NdR1J7hDfg)


The following warning means I will have to rework this in the future:

Warning:  Option `--sdk-auth` has been deprecated and will be removed in version '3.0.0'.
In the future release, this command will not create a 'Contributor' role assignment by default.  If needed, use `--role` argument to explicitly create a role assignment.


[![az ad sp show](https://img.youtube.com/vi/Stkrftaw4NM/0.jpg)](https://www.youtube.com/watch?v=Stkrftaw4NM)

`--sdk-auth` has been deprecated and will be removed in version '3.0.0'

Giving access with `az ml workspace share` is not possible anymore, so `az role assignment ` was used instead:
[![az role assignment (former az ml workspace share)](https://img.youtube.com/vi/QTQpVrIDnXI/0.jpg)](https://www.youtube.com/watch?v=QTQpVrIDnXI)


This is a screenshot of the role assignments in the **Access control (IAM)** section of my `ml-od` Machine Learning resource, North Europe region, in the `ml1` resource group, where the new ml-auth (
Enterprise Application) is both `Contributor` and `Owner`:
![Access control (IAM)](/screenshots/s1.jpg)

![ml-auth](/screenshots/ml-auth.png)

### Step 2: Automated ML Experiment <a name="automl-exp"></a>

Registered Bank Dataset:
![Registered Bank Dataset](/screenshots/registered_bank_dataset.png)

Completed Experiment:
![Completed Experiment](/screenshots/completed_experiment.png)

![Best Model](/screenshots/completed_run_with_best_algo.png)

`bank-exp-2` is the Automated ML experiment, created in the Azure Machine Learning Studio GUI.

`ml-bankmarketing-exp1` is the programmatic one, created with Azure Machine Learning SDK, see Notebook `aml-pipeline-with-AutoMLStep.ipynb`.


### Step 3: Deploy the Best Model <a name="deploy-best-model"></a>

The model was deployed with Key-based authentication enabled, see screenshot in step 4 below.


### Step 4: Enable Logging (Application Insights) <a name="app-insights"></a>

![Endpoint with Apps Insights](/screenshots/endpoint_updated_app_insights.png)

The best model is chosen for deployment and "Authentication" is enabled while deploying the model using Azure Container Instance (ACI). 

![logs py running](/screenshots/execute_logs_py.png)

The executed code in logs.py enables Application Insights. "Application Insights enabled" is disabled by default, before executing logs.py.  

This can be done with the Python SDK too, or from the Azure Machine Learning GUI while deploying.

### Step 5: Swagger Documentation <a name="swagger"></a>

To run the container provided, I had to install Docker first.  The easiest to do this on Ubuntu 20.04 was using:

`sudo apt install docker.io`

or from the [Snap](https://snapcraft.io/install/docker/ubuntu) Store with  `sudo snap install docker`


Here's a screenshot after running the `swagger.sh` and `serve.py` scripts:

![Swagger Docker serve py](/screenshots/swagger2.png)


Two additional screenshots are available in the /screenshots folder: 
run_swagger_sh.png,
swagger_at_last.png

On linux you minght need to type `0.0.0.0` instead of `localhost` in the browser




### Step 6: Consume Model Endpoints <a name="consume-enpoints"></a>



![Test endpoint py](/screenshots/test_endpoint_py.png)

Endpoints can be tested from inside the Azure Machine Learning.  These is a default JSON payload, but one can edit it.
I had to modify the `endpoint.py`, not only by adding key/token and URL, but also by adding the 
`"method": "predict"`
to the JSON payload.

 
Benchmark with Apache Benchmark:
![Swagger Docker serve py](/screenshots/apache_benchmark_run.png)

Apache Benchmark should be installed.  On Ubuntu this is done by 
`sudo apt install apache2-utils `




### Step 7: Create, Publish and Consume a Pipeline <a name="prog-pipeline"></a>

![Pipeline created with dataset](/screenshots/pipeline_endpoint.png)


![Registered Bank Dataset](/screenshots/registered_ds1.png)

Once adde using the Azure ML Python SDK, datasets can be used with both programmatic pipelines and from for Automated ML experimets.


The `RunDetails` widget imported from `azureml.widgets` works great for displaying the run progress and results in the Azure Machine Learning environment, but might be a challenge to install locally (it's an experimental version for VS Code on Linux): 

![RunDetails Widget picture1](/screenshots/autoML-widget1.png)
![RunDetails Widget picture2](/screenshots/autoML-widget2.png)

 

Pipeline scheduled run in Azure Machine Learning Studio -- Pipelines --> Pipeline endpoints
![Pipeline scheduled run](/screenshots/pipeline_scheduled_run.png)


## Screen Recording / Documentation <a name="screencast"></a>

Here's the link to a screen recording of the project summary:

[![Summary screencast](https://img.youtube.com/vi/EShMf_PmUFs/0.jpg)](https://youtu.be/EShMf_PmUFs)

Screencast attempts to demonstrate:
- working deployed ML model
- deployed pipeline
- available AutoML Model 
- successful API requests to the endpoint with a JSON payload

## Standout Suggestions <a name="future-work"></a>

* Apache Benchmark was done using the provided script (see above), but needs more detailed review

* update the Authentication commands (or a new script) to prepare for the new version of Azure Machine Learning CLI:
> Warning:  Option `--sdk-auth` has been deprecated and will be removed in version '3.0.0'.
In the future release, this command will not create a 'Contributor' role assignment by default.  If needed, use `--role` argument to explicitly create a role assignment.


## References <a name="refs"></a>

These are usefull, from the Azure docs:
 

https://docs.microsoft.com/en-us/azure/machine-learning/concept-ml-pipelines 

 

https://docs.microsoft.com/en-us/azure/machine-learning/how-to-setup-authentication

 

https://docs.microsoft.com/en-us/azure/machine-learning/reference-azure-machine-learning-cli

 

 
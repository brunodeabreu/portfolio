---
title: "Deploy and consume models "
date: 2020-12-22T08:06:25+03:00
hero: /posts/img/dp100.png
description: notas curso de data science certificação
menu:
  sidebar:
    name: 4. DP-100 Deploy and consume models 
    identifier: ds-cert-study-posts-dp100-part4
    weight: 4
    parent: ds-cert-study-posts
---



# Intro

Este é o quarto post com anotações do curso preparatório para certificação **DP-100** e aborta tópicos criação de compute targets e o que eles precisam ter para funcionar como um serviço, deploy de modelos as a service, criação de pipeline para batch inferencing e Publish as a designer pipeline as a web service.


## Deploy and consume models 



### Deploy models with Azure ML

Podemos fazer deploy do modelo como web service ou como um azure IoT device, o workflow é similar.

![deploy-workflow](/posts/DS-cert/img/deploy_workflow.png)


**Register model**

* O modelo registrado é um container lógico composto por um ou mais arquivos

* Depois de registrar o arquivo ou modelo, podemos fazer download ou realizar o deploy do modelo registrado

* Os modelos de Machine Learning são registrados no AzureML

* O modelo pode vir do AzureML ou outra plataforma.


**Register a model from an experiment run**

* Usando SDK
  
  - Quando usamos o SDK para treinar o modelo recebemos um objeto `Run` ou um `AutoMLRun` , estes objetos podem ser usado para treinar o modelo
  
<br>  

* Usando CLI

  - Podemos usar comandos de CLI como : 
  
  ```
  az ml register -n sklearn_mnist --asset-path outputs/sklearn_mnist_model.pkl --experiment...
  ```
  
  
<br>

* Usando VS Code

  - No VS Code temos uma extensão que nos auxilia a realizar o registro do modelo
  - Podemos realizar o deploy do modelo como web service para: 
    - Azure Container Instance (**ACI**) 
    - Azure Kubernetes Service (**AKS**)
  
<br>


* Usando SDK and ONNX
  
  - ONNX é um pacote open source criado pela microsoft que permite realizar deploy usando C#.
  




### Choose a compute target


* _**Azure Kubernetes Service `AKS`**_ : Usado para deploy's de produção em alta escala, oferece reposta rápida e auto-escalável de serviços. Cluster autoscaling não é suportado pelo AzureML SDK, para modificar nodes no AKS cluster é preciso usar a UI no Azure portal.

* _**Azure Container instance `ACI`**_ : Utilizado para low-scale CPU-based workloads que requerem menos de 48GB de RAM

* _**Azure Machine Learning Compute clusters**_ : Para rodar Batch scoring em serverless compute. Suporta VM's de worksload normal ou de baixa prioridade.

* _**Azure Functions**_: Utilizado para preparar deploy, install SDK, criar imagens e realizar deploy de imagens as web app


* _**Azure IoT Edge**_ : Realiza deploy de modelos ML em IoT devices, converte modelos em azure containers.

* _**Azure Data Box Edge**_ : Realiza deploy de modelos ML em IoT devices. Utiliza Azure Stack Edge (AI-enabled edge computing) um Hardware-as-a-service que a microsoft oferece como cloud managed device with built-in  FPGA (_Field Programmable Gate Array_) que possibilita acelerar AI-inferencing.



### Deploy to AKS - _Usando SDK_

Para realizar deploy de modelos no Azure Kubernetes Service, criamos o deployment config com todas as configurações necessárias


```
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws, 'myaks')

# if deploying to a cluster config for de/test, ensure thatit was created with enouth cores and memory 
# to handle this deployment config. Note that memory is also used by things sucha as dependence  
# and AML comonents

deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1 , memory_gb = 1)
service = Model.deploy(ws, 'myservice', [model], inferece_config, deployment_config, aks_target)
service.wait_for_deployment(show_output=True)
print(service.state)
print(service.get_logs())
```


### Deploy to ACI - _Using SDK_

Para realizar deploy do modelo em Azure Container Instance **ACI** criamos o deploy config como no exemplo abaixo:

```
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1 , memory_gb = 1)
service = Model.deploy(ws, 'aciservice', [model], inferece_config, deployment_config)
service.wait_for_deployment(show_output=True)
print(service.state)
print(service.get_logs())
```


### FPGA inference
_FPGAs vs. CPU, GPU and ASIC_


![hardware-fpga](/posts/DS-cert/img/fpga_inf.png)

Podemos reconfigurar FPGAs para diferentes tipos de modelos de machine leraning, essa flexibilidade e favorece aplicações otimizadas para computação numérica e modelos que utilizam muita memória.


### What' Supported on Azure

* FPGAs 
  - Image Classification and recognition scenarios
  - TensorFlow deployment
  - Intel FPGA
  
<br>

* DNN models : 
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG
  
<br>

* FPGAs per region
  - East US
  - Southeast Asia
  - West Europe
  - west US 2

  
<br>


### How to create a Kubernetes cluster with GPUs

```
from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for cluster
aks_name = 'aks-gpu'

# Check to see if the cluster already exists

try:
  aks_target = Computetarget(workspace = ws, name= aks_name)
  print('Found existing compute target')

except ComputeTargetException:
  print('Creating a new compute target ...')
  
  # Provision AKS cluster with gpu
  prov_config = AksCompute.provisioning_configuration(vm_size='Standard_NC6')
  
  # create the clsuter
  aks_target = ComputeTarget.create(
      workspace = ws, 
      name = aks_name,
      provioning_configuration = prov_config
  )
  
  aks_target.wat_for_completion(show_output=True)
```

### Define the deployment configuration

O deployment configuration abaixo define Azure Kubernet Service enviroment para rodar o web service:

```
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas = 3,
                                                    cpu_cores = 2,
                                                    memory_gb = 4)
```



### Deploy the model

```
from azureml.core.model import Model

# Name of web service that is deployed
aks_service_name = 'aks-dnn-mnist'

# Get the registered model
model = Model(ws, 'tf-dnn-mnist')

# Deploy the model
aks_service = Model.deploy(ws, models = [model],
                           inference_config = inference_config,
                           deployment_config = gpu_aks_config,
                           deploument_target = aks_target,
                           name = aks_service_name)
                           
aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```



## Batch Inferecing

> Processar grande quantidade de dados _(Big Data)_ utilizando computação paralela


### ML Processing of Big Data

* Processamento para grandes volumes de workload
* Designed para processamento em Batch e otimizado para high throughput
* Divide os dados em pequenas partes para processamento paralelo
* Suporta dados estruturados e não estruturados
* Auto-parallelism via Automated ML SDK


### 3 Steps for Batch Inferencing

1. The dataset is partitioned (_dado pode ser processado ao mesmo tempo_)
2. Delegate the batch inferences to scaled compute
3. Output is stored in defined storage


### Batch Inference Pipelines

![batch_inf_pipeline](/posts/DS-cert/img/batch_inf_pipeline.png)

![batch_inf_pipeline2](/posts/DS-cert/img/batch_inf_pipeline_2.png)


## Deploy To Compute Instances


1. O primeiro passo é registrar o modelo, podemos utilizar SKD ou CLI para realziar o deploy do modelo, sendo que o deploy pode ser em um único endpoint ou multi-model endpoints


2. O segundo passo é submeter dado de teste para o serviço que esta rodando como no exemplo

![test_service](/posts/DS-cert/img/test_service.png)



### Use an existing model with AzureML Register model

Registrando o modelo permite que controle verões e track de metada sobre o modelo na workspace

```
from azureml.core.model import Model

# Tip : When model_path is set to a directory, you can use the child_paths parameter
#       to include only some of the files from the dir

model = Model.register(model_path = './models',
                       model_name = 'sentiment',
                       description = 'Sentiment analysis model trained outside Azure ML',
                       workspace = ws)
```

### Define Inference Configuration

* An entry script:
  - O arquivo `score.py` faz o load do modelo quando o serviço de deploy inicia, ele também é responsavel por receber os dados e passar ao modelo e assim retornar a resposta

<br>  


* An Azure Machine Learning Environment
  - Um ambiente define as dependencias de softwares necessárias para rodar o modelo e o _entry script_



### Define deployment

O webservice package contem todas as classes usadas para o deployment.


```
from azureml.core.webservice import LocalWebservice
deployment_config = LocalWebservice.deploy_configuration()
```


### Deploy the model

Durante o processo de deploy, _instance configuration_ e _deployment configuration_ são usados para criar e configurar o _service environment_ 


```
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print('scoring URI : ' + service.scoring_uri)
```


### Request-reponse comnsumption

Exemplo de um cliente que submeteu dados para o servi,o e mostra a resposta

```
import requests
improt json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type': 'application/json'}

test_data = json.dumps({'test': 'today is a great day!'})

response = requests.pot(scoring_uri, data = test_data, headers = headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```


## Consume an Azure Machine Learning model deployed as a web Service


**General Workflow**

1. Use SDK to get the connection information
2. Determine the type of request data used by the model
3. Create an application that calls the webs service.



**Connection information**

* Secured web service : Se realizar o deploy utilizando web service SSL certificate, podemos utilizar https para conectar ao web service usando scoring ou swagger URI.

* Authentication for services : Usa duas formas de controlar acesso com auth keys and tokens




**Resquest data**

REST API esperam receber um documento JSON com estrutura pre definida `{"data" : [<model-specific-data-structure>] }`

Pode ser utilizado em C#, Go, Java, Python, PowerBI, etc.


## Enable Data collection

É possível que seja necessário coletar informações do modelo toda vez que ele é executado, para habilitar tal feature é preciso habilitar o `data collector` editando o scoring file :

```
from azureml.monitoring import ModelDataCollector

# declarar a data collection variable na função init
```


### Using Python to turn on Azure Application Insights

Após ter o modelo treinavo e atualizado o service podemos habilitar o Azure Application Insights:

```
from azureml.core.webservice import Webservice
aks_service = Webservice(ws, 'my-service-name')

aks_service.update(enable_app_insights = True)
```



## Pratical Questions:


> You plan on deploying a model and have it analyse a very big dataset. You have impelmented batch inferencing so that it ill process the data in parallel. What is the optimal compute compoenents for this implementation ? 

A) GPUs

B) FPGA

C) CPU

D) ASIC

**Resposta : ** A  _GPU designed for parallel processing_

---

> Which code will load the explainer 'named_explainer'

![q_explainer](/posts/DS-cert/img/q_explainer.png)


**Resposta : ** A

---

> You want to install your model as a web service. Select all deployment options which support this


A) Azure Machine Learning compute clusters

B) Deploy to the Web using a URL

C) Deploy to ACI

D) Deploy to a AKS Container

**Resposta :** C, D

---

> I want to turn on logging so for future deployments using the Azure Machine Learning Workspace. Where do I go and what I need to configure ? 

A) Go to Models, select model and update the properties

B) Go to Properties, select the model and turn on logging

C) Go to deployments, select deployment and turn on Application Insights

D) Go to experiments, seelct the experiments and turn on application insights

**Resposta :** C






## Learning Path

> [Criar soluções de IA com o Azure Machine Learning](https://docs.microsoft.com/pt-br/learn/paths/build-ai-solutions-with-azure-ml-service/)

* [Microsoft Learn - Azure Machine Learning Labs](https://tinyurl.com/amlvhol)
* [Microsoft Certifications](aka.ms/MSCert)

* [Exam details page](https://docs.microsoft.com/en-us/learn/certifications/exams/dp-100)



## Lab 

> Hands on Lab [Azure Machine Learning Exercises](https://microsoftlearning.github.io/mslearn-dp100/)

* [MicrosoftDocs/mslearn-aml-labs](https://github.com/MicrosoftDocs/mslearn-aml-labs/tree/master/labdocs)
  - [Monitoring Models](https://github.com/MicrosoftDocs/mslearn-aml-labs/blob/master/labdocs/Lab12.md)
  - [Monitoring Data Drift](https://github.com/MicrosoftDocs/mslearn-aml-labs/blob/master/labdocs/Lab13.md)



<br>

* [MicrosoftLearning/DP100](https://github.com/MicrosoftLearning/DP100)







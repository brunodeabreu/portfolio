---
title: "Run experiments and train models"
date: 2020-12-18T08:06:25+03:00
hero: /posts/img/dp100.png
description: notas curso de data science certificação
menu:
  sidebar:
    name: 2. DP-100 Run experiments and train models 
    identifier: ds-cert-study-posts-dp100-part2
    weight: 2
    parent: ds-cert-study-posts
---



# Intro

Este segundo post com anotações feitas no curso preparatório para certificação **DP-100** e aborta tópicos de experimentos e treinamento de modelos.


<br>

## Run experiments and train models  

_Tópicos:_
* Criando modelos usando Azure Machine Learning
* Executando training scripts no Azure ML Workspace
* Gerando métricas a partir dos experimentos
* Automatizando o processo de treinamento do modelo

<br>

### Azure Machine Learning SDK for Python

> Azure ML Python SDK permite criar e executar machine learning workflows no ambiente _Azure Machine Learning_

* Podemos utiliar para: 
  - Interactive python session
  - Jupyter notebooks
  - Visual Studio Code
  - outras IDE's
  
<br>

* Utilizando essas ferramentas podemos **explorar**,  **preparar**, **gerenciar** todo lifecycle dos datasets a serem utilizados nos experimentos de machine learning

* Com Azure ML podemos administrar os recursos da cloud para **monitor**, criar **logging** e **organizar** todos os experimentos de machine learning

* Podemos treinar modelos localmente ou usando recursos da cloud, incluindo GPU's

* Azure oferece _Automate Machine Learning_ que podemos configurar uma série de parâmetros para treinar os modelos. Esta tool controla toda iteração com algorítimos, hyper-parâmetro e seleciona o melhor modelo para realizar as predições

* Por fim, podemos realizar deploy em web services convertendo os modelos em RESTful services que podem ser consumidos por qualquer aplicação

<br>

### Run Configuration

Um Job genérico de treinamento pode ser definido com **RunConfiguration**, ele é usado com scripts para treinar modelos em _compute target_, porém é possível iniciar um run configuration na máquina local e depois fazer o switch para um compute target na cloud, um detalhe é que quando mofidicado o compute target podemos apenas modificar o run configuration a ser utilizado oferencendo mais flexibilidade para os jobs de machine learning.

<br>

### Automated Machine Learning

Estes são os steps do **Automated Machine Learning**, inciando por definir iterações, hyperparametros e configurações, realizando o treinamento e assim como qualquer boa tool de `automl` irá lhe oferecer o melhor modelo baseado nos estimators que ele trabalha.


![auto_ml](/posts/DS-cert/img/auto_ml.png)

<br>

### Estimators


É uma forma fácil de treinar modelos utilizando os mais populares frameworks de machine learning, Azure também oferece o _estimator class_ que auxilia a construir run configurations. 

Podemos utilizar diversos frameworks como **Pytorch**, **TensorFlow** e **scikit-learn**, todos são oferecidos na plataforma além de possibilitar criar estimadores genéricos.

<br>

### Pipeline

Criar pipelines organizados auxilia o ciêntista de dados a reproduzir e agilizar o processo de análise de dados.

![pipeline](/posts/DS-cert/img/pipeline_ml.png)

<br>

* Pipeline consiste em datasets e modelos analíticos que se conectam para diferentes usos como :

  - Criar pipeline para treinar um ou mais modelos
  - Criar pipeline para realizar predições em real-time ou batch
  - Criar pipline para realizar limpeza de dados
 
> Pipeline oference a possibilidade de reusar códigos e organizar os projetos
 

Quando editamos o pipeline no designer ele é salvo como **pipeline draft**, um pipeline válido tem as seguintes características:

* Datasets pode conectar somente em módulos
* Módulos pode conectar somente em datasets e em outros módulos
* Todas as portas _(input ports)_ dos módulos precisam ser conectadas com o data flow
* Todos parâmetros dos módulos precisam ser configurados


Toda vez que um pipeline é executado a configuração do pipeline é armazenada na workspace, em _pipeline run_, onde pode ser investigado, auditado e contém todos os logs para troubleshooting. Todos os _pipeline runs_ são agrupados em _experimentos_ para organizar.


Podemos definir um **Compute Target** para todo o pipeline, assim todos os módulos irão usar o mesmo compute target por default, ou podemos definir __compute targets__ específicos para módulos.


<br>

### Machine Learning Designer

Tool criada pela microsoft que facilita a criação e estruturação do pipeline e experimento, onde é possível por _"drag and drop"_ montar todo pipeline de análise de dados.

![designer](/posts/DS-cert/img/designer_ml.png)


<br>

Este é um canvas que descreve a utilização do designer:

* Drag-and-drop datasets e modelos no canvas
* Conectar módulos e criar um pipeline draft
* Submeter o pipeline run usando recursos computacionais do Azure Machine Learning workspace
* Converter o training pipeline em inference pipeline
* Publicar o pipeline em _"REST pipeline endpoint"_ para submeter novos pipline runs com diferentes parâmetros e datasets
* Publicar, treinar pipeline e reusar endpoints para treinar múltiplos modelos
* Publicar batch inference pipeline para realizar predições em novos dados usando o modelo
* Realizar deploy em "real-time inference" para endpoint e realizando predições em novos dados em tempo real.

![designer-canvas](/posts/DS-cert/img/canvas_design.png)



<br>


### CLI

Azure CLI é uma extenção que possibilita trabalhar com Azure Machine Learning utilizando comandos CLI cross-plataform, possibilitando automatizar tarefas como treinamento de modelos, entre outras.



### Azure Machine Learning Workspace - Python create and connect

Para carregar as configurações a partir de um arquivo `config.json` que contem workspace name, subscription e resource group, precisamos : 

```
from azureml.core import Workspace
ws = Workspace.from_config()
```

<br>

Para criar um experimento na workspace:

```
from azureml.core import Experiment
experiment = Experiment(workspace = ws, name='<experiment_name>')
```

<br>

### Track and Monitor Experiments

Na Azure ML podemos utilizar as seguintes métricas para acompanhar os experimentos.


**Escolhendo uma opçao de Logging**

* `Run.start_logging` : Adiciona _logging functions_ no script de treinamento e inicia um logging _"interativo"_" na sessão



<br>

* `ScriptRunConfig` : Adiciona logging functions no script de treinamento e faz load da script folder



![script-runconfig](/posts/DS-cert/img/script_run_config.png)


<br>

### MLflow

Outra forma de fazer track do modelo ou processo de ML é utilizando a biblioteca open source [MLflow](https://mlflow.org/) que oferece a possibilidade de administrar todo o ciclo de vida de um experimento em machine learning, com ela podemos, fazer track do experimento, obter métricas e armazenar artefatos(_stats_) do modelo na Azure Machine Learning Workspace


* Local runs:

```
# instalar MLflow
pip install azureml-mlflow

...
...

# importar libs
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()
mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())

...
...

experiment_name = 'experiment_with_mlflow'
mflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```



<br>

* Track remote runs:

```
from azureml.core.enviroment import Enviroment
from azureml.core.conda_depedencies improt CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace', name = 'my_experiment')

cd = CondaDependencies.create(pip_package=['mlflow', 'azureml-mlflow'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory = './my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.enviroment = mlflow_env

```

<br>

* Logging the metrics

```
import mlflow

with mlflow.start_run():
    mlflow.log_metrics('sample', 1.23)
    
    
run = exp.submit(src)

```



### Start a run and its logging process

**Usando SDK**
```
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Expriment(workspace = ws, name = 'explore-runs')


notebook_run = exp.start_logging()
notebook_run.log(name = 'message', value = 'Hello from run!"")

```
<br>

**Usando CLI**

```
az ml run submit-script -c sklearn -e testexperiment train.py
```

<br>

**Usando Azure Machine Learning Studio**

1. Configure _compute target_ para o pipeline
2. Selecine _Run_  no pipeline canvas
3. Selecione um experiment para agrupar os pipeline runs



### Monitoring the status

**Usando SDK**

```
# get the status of a run with get_status()
print(notebook_run.get_status())

# to get the un ID, excution time and all the details
print(notebook_run.get_details())


# check if complete successfully
print(notebook_run.get_status())

# using with...as design pattern, the run will auto mark complete
with exp.start_logging() as notebook_run:
  notebook_run.log(name = 'message', value= 'Hello run!')
  print(notebook_run.get_status())

print(notebook_run.get_status())
```


### Cancel or fail runs

**Usando SDK** 

```
run_config = ScriptRunConfig(source_directory = '.', script='hello_with_delay.py')
local_print_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```
<br>

**Usando CLI** 

```
az ml run cancel -r runid -w workspace_name -e experiment_name
```

<br>

## Pratical Questions:

> What 5 steps are included in creating a Pipeline ? 


A. Prepare Data

B. Create Datastore

C. Train Data

D. Construct the pipeline

E. Create a Workflow

F. Define Compute output

G. Define Training Compute

H. Define a Pipeline data object

I. Run an Estimator

J. Run an Evaluator

**Resposta** : H, A, I, C, D,



> Why do you need to register the model in an Azure ML Workspace ? 

A. In order to be able to load the model when needed

B. The model is registered to configure the deployment compute

C. Part of the Model.deploy script

D. Needed to be able to construct the pipeline

E. Part of the real time inferencing service

**Resposta** : A



> Which python SDK code will deploy a model ? 

![Q reploy](/posts/DS-cert/img/question_deploy_model.png)




**Resposta** : C



> Which logging function would you use to create an interactive log in a jupyter notebook ? 

A. ScriptRunConfig

B. Log.start

C. run.getmetrics()

D. Run.start_logging


**Resposta** : D



## Learning Path

> [Criar soluções de IA com o Azure Machine Learning](https://docs.microsoft.com/pt-br/learn/paths/build-ai-solutions-with-azure-ml-service/)

* [Microsoft Learn - Azure Machine Learning Labs](https://tinyurl.com/amlvhol)
* [Microsoft Certifications](aka.ms/MSCert)

* [Exam details page](https://docs.microsoft.com/en-us/learn/certifications/exams/dp-100)


## Lab

> Hands on Lab [Azure Machine Learning Exercises](https://microsoftlearning.github.io/mslearn-dp100/)


* [MicrosoftDocs/mslearn-aml-labs](https://github.com/MicrosoftDocs/mslearn-aml-labs/tree/master/labdocs)
  - [Getting Started with Azure Machine Learning](https://github.com/MicrosoftDocs/mslearn-aml-labs/blob/master/labdocs/Lab05.md)
  - [Creating a Pipeline](https://github.com/MicrosoftDocs/mslearn-aml-labs/blob/master/labdocs/Lab05.md)
  - [Deploying a Model as a Real-Time Service](https://github.com/MicrosoftDocs/mslearn-aml-labs/blob/master/labdocs/Lab06.md)
  - [Creating a Batch Inferencing Service](https://github.com/MicrosoftDocs/mslearn-aml-labs/blob/master/labdocs/Lab07.md)
  - [Tuning Hyperparameters](https://github.com/MicrosoftDocs/mslearn-aml-labs/blob/master/labdocs/Lab08.md)


<br>

* [MicrosoftLearning/DP100](https://github.com/MicrosoftLearning/DP100)























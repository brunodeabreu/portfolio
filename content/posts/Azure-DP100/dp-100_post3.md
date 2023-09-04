---
title: "Optimize and manage models "
date: 2020-12-21T08:06:25+03:00
hero: /posts/img/dp100.png
description: notas curso de data science certificação
menu:
  sidebar:
    name: 3. DP-100 Optimize and manage models
    identifier: ds-cert-study-posts-dp100-part3
    weight: 3
    parent: ds-cert-study-posts
---

 
# Intro

Este terceiro post com anotações feitas no curso preparatório para certificação **DP-100** e aborta tópicos de administração e otimização de modelos: 

* Com a grande diversidade de algoritmos e técnicas computacionais e estatísticas utilizar **AutoML** proporciona ao ciêntista de dados experimentar e avaliar diversos espaços de hipóses com vários algoritmos. Outra facilidade do Azure ML é o **Hyperdriver** que auxilia na etapa de tune de hiper-parâmetros. 

* Outro detalhe importante é a possibilidade de explicar e interpretar os modelos, _"-Como o modelo foi criado?"_, - _"Como ele chegou a essa decisão ?"_, etc. 

* Por fim a administração de modelos, com o tempo os modelos podem se tornar obsoletos, começar a fazer predições ruins, como se monitora a performance do modelo ? Como se versiona o modelo ? 


Todos esses tópicos são abordados neste tópico de estudo.

<br>

## Optimize and manage models  


### Machine Learning process

![ml_process](/posts/DS-cert/img/ml_process.png)

Tipicamente o processo de aprendizado de máquina segue um roteiro como :

1. `Preparação dos dados` : Limpeza e preparação de datasets, tratar outliers, analise descritiva para entender as distribuições e o dataset, tratamento de missing values ou valores não condizentes, etc.

2. `Experimentos` : Na segunda etapa de experimentos assim como na anterior utilizamos ferramentas IDE como VSCode ou Jupyter notebooks em caso de python ou Rstudio para R para criar modelos, realizar treinamento e teste em conjunto de dados não treinados, quando o modelo estiver criado e validado o analista deve registrar o modelo para o deploy


3. `Deploy` : Criação do container para disponibilizar para utilização, assim o modelo pode ser consumido por aplicações, nesta etapa temos opção de utilizar Container instances ACI ou AKS Kubernetes services.



Esse processo se torna um loop no ambiente de **DevOps** para Data Science, poís o cenário de análise de dados em que o modelo se engloba muda e com isso tanto o modelo com as demais etapadas precisam ser refeitas, gerando um ciclo a ser repido diversas vezes tanto na fase de desenvolvimento como em produção.

![auto_ml](/posts/DS-cert/img/loop_data_science.png)


<br>

### Required to train the model



* **Ambiente Computacional** : É preciso ter um ambiente que seja fácil escalar com VMs e auto scaling scale-out cluster, com utilização por demanda.


* **Ambiente Pre-configurado** : Idelamente o ambiente deve ser pré-configurado, ou seja, já incluir as versões de framework de machine learning que você planeja trabalhar, bibliotecas, etc. O que irá facilitar e agilizar o trabalho do analista de dados.


* **Administração de JOBs** : Em ambiente corporativo com vários ciêntistas de dados e analistas na mesma equipe, o ideal não é cada um ter seu ambiente e sim um ambiente compartilhado de experimentos orientados por job's e o time possa iniciar, monitorar e gerenciar os jobs de analise de dados e os projetos e conhecimento seja compartilhado.



* **AutoML e Seleção de parâmetros automáticos** : Uma solução de AutoML que aplica os melhores algoritmos de ML e testa os principais hiper-parâmetros ofere uma produtividade muito grande para o analista em projeto que podem ser aplicados.

<br>




### Requirements to Register and manage the models

* **Containerização** : Azure ML automaticamente converte os modelos em Docker containers o que facilita o deploy em um ambiente de execução.


* **Versionamento** : Um detalhe muito importante é ter versões dos modelos para acompanhar modificações ao longo do tempo, identificar problemas, recuperar versões específicas de deploys, rollbacks, etc


* **Repositório de Modelos** : Azure oferece um repositório de modelos compartilhado que te habilita a integrar pipelines de CI/CD ao ambiente de Data Science. 


* **Acompanhar Experimentos** : Ter um ambiente com detalhes de cada experimento é super importante para auditoria, verificar as modificações ao longo do tempo e habilitar a colaboração em membros do time.


<br>

### Automated Hyperparameter tuning on Distributed compute using Azure ML Service

O processo manual de criação de modelos, treinar diferentes algoritmos, configurar hiper-parâmetros, gerenciar infraestrutura é um comprocesso que demanda tempo do analista de dados e muito repetitivo, oferecendo diversas possibildades de erros e falhas durante o processo.

Utilizando **AutoML** Azure ML criar diversos modelos e otimizados com os melhores parâmetros utilizando `hyperdrive`, onde você especificar quais parâmetros quer testar e qual o range de valores. A ferramenta irá disparar jobs paralelos testando todas as configurações escolhidas pelo ciêntista de dados, por fim sugere o melhor modelo.

Para o processo de hiper-parâmetro a tool hyperdriver suporta os seguintes algoritmos de tuning : 

<br>

* **Grid Sampling**

```
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling({
    "num_hidden_layers": choice(1,2,3)
    "batch_size" : choice(16,32)
 }
)
```

<br>

* **Random Sampling**

```
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling({
    "learning_rate": normal(10,3),
    "keep_probability" : uniform(0.05, 0.1),
    "batch_size" : choice(16,32, 64)
 }
)
```


<br>

* **Bayesian Optimization**

```
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling({
    "learning_rate": uniform(0.05, 0.1),
    "batch_size" : choice(16,32, 64)
 }
)
```
<br>

![sampling_model](/posts/DS-cert/img/sampling_model.png)



<br>

### Hyperparameters Type

<br>

* **Discrete Hyperparameters** : Realiza a busca do parâmetro escolhendo em um range de valores discretos

```
{
  "batch_size" : choice(16, 32, 64, 128),
  "number_of_hidden_layers": choice(range(1,5))

}
```

<br>

* **Continuous Hyperparameters** : Realiza a busca em uma distribuição continua como `uniform(low, high)`, `loguniform(low, high)` , `normal(mu, sigma)` e `lognormal(mu, sigma)`

```
{
  "learning_rate" : normal(10, 3),
  "keep_probability": uniform(0.05, 0.1)

}
```
<br>

### Primary metric

<br>

* Para cada treinamento devemos especificar a forma de avaliação do modelo a `primary metric`.

```
primary_metric_name = 'accuracy',
primary_metric_goal = PrimaryMetricGoal.MAXMIZE
```
<br>

* Log metric para hyperparameter tuning
_Quando se quer adicionar no script de treino a funcionalidade de logar as métricas relevantes_
```
from azureml.core.run import Run
run_logger = Run.get_contect()
run_logger.log('accuracy', float(val_accuracy))
```

<br>

### Allocate resources

Para controlar o gasto com recursos em realizar o tuning em hiper-parâmetros em experimentos é importante especificar o número máximo de `training runs`.

* `max_total_runs` : Número máximo de _training runs_ que será criado

* `max_duration_minutes` : Duração máxima em minutos do tuning de hiper-parâmetros em um experimento

* `max_concurrent_runs` : Número máximo de `runs` para rodar ao mesmo tempo.

```
max_total_runs = 20
max_concurrent_runs= 4
```
<br>

### Configure experiment

Para configurar o **_hyperparameter tuning experiment_** é utilizado o **hyperparamter search space**, **early termination policy**, **primary metric** e **resource allocation**.

```
from azureml.train.hyperdrive import HyperDriveConfig

hyperdrive_run_config = HyperDriveConfig(estimator = estimator,
                          hyperparameter_sampling = param_sampling,
                          policy = early_termination_policy,
                          primary_metric_name = 'accuracy',
                          primary_metric_goal = PrimaryMetricGoal.MAXMIZE,
                          max_total_runs = 100,
                          max_concurrent_runs = 4)
```



### Submit experiment

```
from azureml.core.experiment import Experiment

experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```


### Visualize Experiment

Azure ML SDK provide a **Notebook widget** that visualize the progress of your training runs

```
from azureml.widgets import RunDetails

RunDetails(hyperdrive_run).show()
```

### Find the best model

Uma vez que todos os hyperparameters tuning foram executados, temos como _best model_ a melhor configuração correspondente aos hiper-parâmetros.

```
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metric = best_run.get_metrics()
parameters_values = best_run.get_details()['runDefinition']['Arguments']

print('Best run Id: ' , best_run.id)
print('\n Accuracy: ' , best_run_metrics['accuracy'])
print('\n Learning rate: ', parameter_value[3])
print('\n keep probability: ', parameter_value[5])
print('\n Batch size : ', parameter_value[7])
```


<br>

## Azure Automated Machine Learning

Automated Machine learning é o processo que irá auxiliar o ciêntista de dados a identificar e utilizar o melhor algoritmo de machine learning para solução do problema de negócios.


![automl-process](/posts/DS-cert/img/auto_ml_process.png)

AutoML seleciona o melhor algoritmo e hiper-parâmetro e gera o modelo pronto para o deploy, este modelo pode ser customizado e modificado de acordo com a necessidade de negócio, este processo auxilia na produtividade do time, documentação e deixa para o analista tomar as melhores decisões para solucionar o problema.

Com Azure ML podemos utilizar automl via interface gráfica ou por SDK python para classification, regression e forecasting.


### Preprocessing Step:

Estas são algumas das técnicas que podem ser aplicadas para normalizar, reduzir dimensões e transformar os dados antes de submeter a criação do modelo.

![automl-pre-processing](/posts/DS-cert/img/pre-processing_steps_automl.png)

### Advanced Preprocessing step:


* Drop high cardinality or no variance features
* Impute missing values
* Generate additional features
* Transform and encode
* Word embeddings
* Target encodings
* Text target encoding
* Weight of Evidence (WoE)
* Cluster Distance


### Prevent over-fitting

> A melhor maneira de previnir over-fitting é seguir as melhores práticas de Data science incluindo :  

* Usar mais dados de treino, eliminando _"statistical bias"_
* Previnir _target leakage_ 
* Usar poucas features
* Utilizar Regularização e otimiza,ão de hiper-parâmetros
* Limitar o uso de modelos complexos
* Utilizar _Cross-validation_



### Time-Series forecasting

> Podemos usar automl e combinar técnicas e abordagens para obeter o melhor forecast possível, incluindo : 

* holiday detection and featurezation
* time-series and DNN learners(auto-ARIMA, Prophet, ForecastTCN)
* many models support through grouping
* rolling-origin cross validation
* configurable lags
* rolling window aggregate features

Um _automated time-series_  experiment é considerado como um problema de regressão com multiplas variáveis
, valores de _"past time"_ são manipulados com a técnica de _"pivot"_ e se transforma em uma dimensão adicional para o regressor.


### Ensemble Models

Automated Azure ML suporta métodos **Ensemble** para `voting` ou `stacking` : 

* `Voting` : Predicts based on the weighted average of predicted class probabilities (for classification) or predicted regression targets(for regression).


* `Stacking` : Staking combines heterogeneous models and trains a meta-model based on the output from the individual models. The current default meta-models are `LogisticRegression` for classification and `ElasticNet` for regression and forecast tasks.


### Imbalanced Data

Conjunto de dados desbalanceados é um cenário comum em projetos de machine learning, Azure Auto ML tem a capacidade de lidar muito bem com dados desbalancedos, utilizando **weight column** o input irá fazer com que a columa seja ajustada quanto ao peso, o que levará a classe ser considerada mais ou menos importante. 



**Recursos para identificar dados desbalanceados**

* `Confusion matrix` : Evaluate the correctly classified labels against the actual labels of the data
* `Precision-recall` : Evaluate the ratio of correct label against the ration of found label instance of the data
* `ROC Curves` : Evaluate the ration of correct labels against the ration of false-positive labels



**Lidando com dados desbalanceados**

Quais técnicas utilizar quando temos dados desbalanceados ? 

* Resampling to even the class imbalance, either by up-sampling the smaller classes or down-sampling the large classes.
* Use a performance metric that deals better with imbalanced data.



### Steps of use case for Auto ML Experiment:

1. Download and prepare the data and package
2. Prepare the sample dataframe to work
3. Cleanse data, describe and analyze the data, perform EDA
4. Split the data into train and test sets `train_test_split` from sklearn
5. Automatically train a model
   
   * Define settings for the experiment run
   * Attach your training data to the configuration, and modify setting that control the training process
   * Submit the experiment for model tuning
   * After submitting the experiment, the process iterates through different ML algorithms and hyperparameters setting, adhering to your defined constraints
   * It chooses the best-fit model by optimizing an accuracy metric


> Submeter o treinamento com parâmetros default irá demorar de 5 a 20 min um experimento, caso queira reduzir esse tempo reduza o parâmetro `experiment_timeout_minutes`

6. Depois de iniciar o experimento ele funciona como um container
7. Explore results
8. Retrieve the best model
```
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```
<br>

  9. Use the best model to run predictions on `test` data 
  10. Use `automl_setup_model_explanations` para interpretar o modelo

```
from azureml.train.automl.runtime.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

auto_explainer_setup_obj = automl_setup_model_explanations(fitted_model, 
                                        x = X_train, 
                                        X_test = X_test, 
                                        y = y_train, task = 'classification)
```
<br>

11. Register the model and use scoring explainer to analyze the results, we can use `TreeScoringExplainer` to create the scoring explainer. The score explainer uses the `feature_map` to return the raw feature importance.

12. Create the conda dependencies for setting up the service (create the env dependencies in the container to deploy the model)
  
13. Deploy the service usando conda and scoring file

14. Inference with test data, to see the predicted value from automl model

15. Visualize the feature importance on Azure ML Studio


## Azure AutoML Current Capabilities (12/2020)

![automl-capabilities](/posts/DS-cert/img/azure_auto_ml_capabilities.png)




## Pratical Questions:


> In the Experiment section of the Azure ML Workspace there are several child runs created when the experiment was run. What is a likely reason for having child runs

A) The model type was parent which creates child runs

B) The experiment was a hyperdrive experiment

C) this was an Automated ML Experiment

D) This is standard for a training experiment

E) Child runs are a optional part of Model Maintenance


**Resposta :**  B and C


> Which 3 types of machine learning tasks can be used for Automated Machine Learning ? 

A) Anomaly Detection

B) Classification

C) Clustering

D) Regression

E) Time Series Forecasting


**Resposta :** B, D, E


> Which automl_config definition uses Azure ML for the compute, has a classification task, and will run for 12 iterations and will validate the results based upon a weighed AUC Result ?

![automl-q](/posts/DS-cert/img/q_automl_3.png)



**Resposta :**  D    _"compute cannot be local"_




> Which data sources can be used with Auto ML ? 

A) Azure SQL

B) Blob Storage

C) HDInsight

D) CosmosDB

E) All of the above


**Resposta :*  B


> Which method is not used to prevent overfitting ? 


A) Using more training data

B) Eliminating statistical bias

C) Training of one dataset for multiple runs

D) Preventing target leakage

E) Using Fewer features

**Resposta :** C



## Learning Path

> [Criar soluções de IA com o Azure Machine Learning](https://docs.microsoft.com/pt-br/learn/paths/build-ai-solutions-with-azure-ml-service/)

* [Microsoft Learn - Azure Machine Learning Labs](https://tinyurl.com/amlvhol)
* [Microsoft Certifications](aka.ms/MSCert)

* [Exam details page](https://docs.microsoft.com/en-us/learn/certifications/exams/dp-100)



## Lab 

> Hands on Lab [Azure Machine Learning Exercises](https://microsoftlearning.github.io/mslearn-dp100/)

* [MicrosoftDocs/mslearn-aml-labs](https://github.com/MicrosoftDocs/mslearn-aml-labs/tree/master/labdocs)
  - [Automated Machine Learning](https://github.com/MicrosoftDocs/mslearn-aml-labs/blob/master/labdocs/Lab09.md)
  - [Interpreting Models](https://github.com/MicrosoftDocs/mslearn-aml-labs/blob/master/labdocs/Lab10.md)
  - [Analyzing and Mitigating Unfairness in Models](https://github.com/MicrosoftDocs/mslearn-aml-labs/blob/master/labdocs/Lab11.md)




<br>

* [MicrosoftLearning/DP100](https://github.com/MicrosoftLearning/DP100)


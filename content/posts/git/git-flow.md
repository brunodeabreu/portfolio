---
title: "GitLab Flow"
date: 2021-05-02T08:06:25+03:00
hero: /posts/img/git.png
description: Notas, Git Lab Flow
menu:
  sidebar:
    name: Git Flow
    identifier:  git-posts-basic-part2
    weight: 2
    parent: git-posts
---


## Processo recomendado pelo GitLab

Este processo é recomendado pelo GitLab para times DevOps trabalharem com GitLab.


#### Step 1 - Criando um Issue

Todas modificações no GitLab se iniciam com a crição de um Issue, os issues permitem que times trabalharem de forma colaborativa.

O issue pode ser utilizado para  : 

* Discussão de novas ideias

* Acompanhar status do trabalho

* Aceitar ou rejeitar sugestões de modificaçoes, realizar perguntas, solicitar suporte ou reportar um bug

* Elaborar implementações de novos códigos



#### Step 2 - Criar um novo Merge Request

Logo após criar o issue, você e desenvolvido uma solução o próximo passo é a criação de um Merge Request e início do processo CI/CD, este processo no GitHub é conhecido como Pull Request.


#### Step 3 Commit Changes

Assim que o Merge request é submetido iremos comitar as modificações e iniciar o pipeline de CI/CD



#### Step 4 CI Pipleline Runs

Durante este step é iniciado o build, testes automáticos  e deploy no ambiente de staging



#### Step 5 Review Apps

Oferece uma maneira rápida e prática de revisar as modicações propostas no Merge Request


#### Step 6 Peer Review and Discussion

Neste passo é feita a revissão da modificação para garantir que não tenha conflitos antes do commit final

#### Step 7 Approve Change

Assim que o processo de revisão é concluido um recurso com permissão irá aprovar essa modidicação.

#### Step 8 Merge ; Issue closed

Assim que o Request é aprovado inicia-se o Merge e o issue é fechado.



#### Step 9 CD Pipeline Runs

O processo de Continuous Delivery(CD) irá realizar o deploy em produção e a modificação é colocada em live.


#### Step 10 Monitor

Durante esse processo inicia-se a etapa de monitorameto para garantir que não foi introduzido nenhum issue.


## Aula sobre GitFlow do Jeferson LinuxTips



<iframe width="560" height="315" src="https://www.youtube.com/embed/dJjVr6Ya7B8" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>






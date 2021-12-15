---
title: "Git - Básico"
date: 2021-04-29T08:06:25+03:00
hero: /posts/img/git.png
description: Notas, Comandos e estudos sobre git, github e gitlab
menu:
  sidebar:
    name: Git Basic
    identifier:  git-posts-basic
    weight: 1
    parent: git-posts
---

## Sobre o post

Este post é uma coleção de anotações de estudos sobre Git, Gilab e Github

Porque usar o [git](https://git-scm.com/) ? 
- Versinamento de código, 
- Gerencia de documentos,
- Trabalho colaborativo

Esta sequência de posts foi preparada durante os estudos no curso  [GitLab Certified Associate Self-Study + Exams](https://gitlab.edcast.com/pathways/cy-test-pathway-associate-study-exam/cards/1286380)


### Porque o GitLab

1. GitLab é uma plataforma de **DevOps open-source**, que oferece a oportundiade de empresas sairem do modelo de DevOps Sequência para o modelo Concorrente onde diversos times trabalham ao mesmo tempo de forma colaborativa para realizar o design, build, test, delivery e monitorar modificações no códigos.

2. Optimizada para Kubernets

3. Built In Security e Compliance

4. Deploy software em qualquer lugar, etc.




### Básico




### Como instalar o git no linux ? 

Detalhes de como instalar o git esta no link [Getting Started - Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) no meu caso que utilizo uma distribuição baseda em ubuntu este é o comando:

```
sudo apt-get install git
```

Logo após instalar recomendável realizar algumas [configurações básicas](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup).


### Clonando um projeto

Depois de criar um projeto / repo no Gitlab podemos clonar trazendo todo conteúdo para nossa máquina, lembrando que neste caso como tenho configurado minha chave ssh no gitlab não preciso passar a senha

```
$ git clone git@gitlab.com:brunodeabreu/learning.git
Cloning into 'learning'...
The authenticity of host 'gitlab.com (172.65.251.78)' can't be established.
ECDSA key fingerprint is SHA256:HbW3g8zUjNSksFbqTiUWPWg2Bq1x8xdGUrliXFzSnUw.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'gitlab.com,172.65.251.78' (ECDSA) to the list of known hosts.
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (3/3), done.
(base) bruno@turing ~/Documents/projects/git_learning $ 
```

<br>
<br>

### Git Status


O comando git status verifica o que tem de diferente entre o seu repositório e sua replica local.

_Neste caso o git verificou que minha replica esta completamente sincronizada com o repo_

```
$ git status
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean

```
<br>

_Neste segundo exemplo foi identifico que temos arquivos que nao estao sendo monitorados pelo git e que devemos usar o comando **git add <file>** caso queira adicionar ao repo_

```
 $ git status
On branch master
Your branch is up to date with 'origin/master'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	cap1/
	file2.txt

nothing added to commit but untracked files present (use "git add" to track)

```

<br>
<br>

### Fases do repositório **local**

1. Working Dir 

 * O arquivo `file2.txt` e a pasta `cap1` ainda não são controlados pelo git, logo se encontram no **Working Dir**
 
 * Para que o git possa controlar esses arquivos precisamos mover ele para o `Index` ou `Stage` utilizando `git add <file>` como mostra o último output
 
```
$ git add file2.txt cap1/
$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	new file:   cap1/file1.txt
	new file:   file2.txt

```

2. Index ou Stage

Como podemos ver os arquivos estão no stage e podemos restaurar o arquivo utilizando o `git restore --staged <file>` assim ele saira do stage, ou podemos fazer o `commit` para passar os arquivo para o `HEAD` e depois enviar para o servidor.

```
$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	new file:   cap1/file1.txt
	new file:   file2.txt

```


3. HEAD

Utilizando git commit para enviar os arquivos do Index ou Stage para o HEAD


```
$ git commit -m "Adicionando arquivo file2.txt" file2.txt 
[master be3c68e] Adicionando arquivo file2.txt
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 file2.txt

```
<br>
<br>

Agora só temos mais um arquivo no Index

```
$ git status
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	new file:   cap1/file1.txt

```

<br>
<br>

Adicionando o segundo arquivo `cap1/file1.tx` no HEAD

```
$ git commit -m "adicionando file1" cap1/file1.txt 
[master fcf1c01] adicionando file1
 1 file changed, 1 insertion(+)
 create mode 100644 cap1/file1.txt
$ git status
On branch master
Your branch is ahead of 'origin/master' by 2 commits.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean

```


<br>
<br>

4. Enviando o arquivo para o REPO utilizando o `git push`, todos arquivo da HEAD foram para o repositório


```
$ git push
Enumerating objects: 8, done.
Counting objects: 100% (8/8), done.
Delta compression using up to 8 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (7/7), 629 bytes | 314.00 KiB/s, done.
Total 7 (delta 0), reused 0 (delta 0)
To gitlab.com:brunodeabreu/learning.git
   4af4053..fcf1c01  master -> master

```



<br>

<br>

### Git Log

Caso que queira analisar os commits o git oferece a opção de analisar os logs e conferir o que aconteceu : `git log` 

```
$ git log
commit fcf1c01ca0f1f1df52a2b511d308b1668638e880 (HEAD -> master, origin/master, origin/HEAD)
Author: Bruno Machado <brunodeabreu@gmail.com>
Date:   Thu Apr 29 15:08:44 2021 -0300

    adicionando file1

commit be3c68ea2e9ff6a25716450e24b09e5478c1bd42
Author: Bruno Machado <brunodeabreu@gmail.com>
Date:   Thu Apr 29 15:06:15 2021 -0300

    Adicionando arquivo file2.txt

commit 4af40539b0d7aa33fc4ed29491a9e6f7059a21ad
Author: Bruno A. Machado <brunodeabreu@gmail.com>
Date:   Thu Apr 29 17:07:07 2021 +0000

    Initial commit

```

<br>
<br>

Outras opções:
* `git log -3` : para mostrar as ultimas 3 linhas
* `git log --oneline`  : mostra somente o hash e o comentário
* `git log --author="Bruno"` : filtrando por autor
* `git log --graph --decorate` : mostra em uma estrutura 


<br>
<br>

### Git pull

Baixando arquivos do REPO para LOCAL

Neste caso o file2.txt foi removido e meu repo local esta diferente do repositorio do Gitlab, podemos utilizar o git pull para puxar arquivos 

```
$ rm file2.txt 
$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	deleted:    file2.txt

no changes added to commit (use "git add" and/or "git commit -a")

```

<br>
<br>

### Git rm

Utilizado para remover arquivo do controle do git, ele funciona com o add precisa comitar para ter efeito.

```
$ git rm file2.txt 
rm 'file2.txt'

$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	deleted:    file2.txt

$ git commit -m "remove"
[master 28a0c54] remove
 1 file changed, 0 insertions(+), 0 deletions(-)
 delete mode 100644 file2.txt

$ git status
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean
$ 

```

<br>
<br>

### Branch e Git checkout


Nunca é legal fazer o commit direto na `master` , podemos imaginar a master como uma linha do tempo a versão principal do repositório.


![branch](/posts/git/img/branch.png)
*_Fonte_: https://noic.com.br/git-e-github/


Primeiro passo e criar a branch temporaria `temp123`:

```
bruno @ ~/Documents/projects/git_learning/learning - [master] $ git checkout -b temp123
Switched to a new branch 'temp123'
bruno @ ~/Documents/projects/git_learning/learning - [temp123] $ 


```

Para ilustrar esse flow, adicionei uma imagem `file_branch.txt` e fizemos o commit : 



```
bruno @ ~/Documents/projects/git_learning/learning - [temp123] $ git status
On branch temp123
Untracked files:
  (use "git add <file>..." to include in what will be committed)
	file_branch.txt

nothing added to commit but untracked files present (use "git add" to track)
bruno @ ~/Documents/projects/git_learning/learning - [temp123] $ 


bruno @ ~/Documents/projects/git_learning/learning - [temp123] $ git add file_branch.txt 

bruno @ ~/Documents/projects/git_learning/learning - [temp123] $ git commit -m "adicionando file 4" *
[temp123 362c9e7] adicionando file 4
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 file_branch.txt
bruno @ ~/Documents/projects/git_learning/learning - [temp123] $ 


```

Agora chegou a parte de enviar o arquivo para o servidor remoto do GitLab `git push origin temp123`:


``` 
bruno @ ~/Documents/projects/git_learning/learning - [temp123] $ git push origin temp123
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Delta compression using up to 8 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 276 bytes | 276.00 KiB/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: 
remote: To create a merge request for temp123, visit:
remote:   https://gitlab.com/brunodeabreu/learning/-/merge_requests/new?merge_request%5Bsource_branch%5D=temp123
remote: 
To gitlab.com:brunodeabreu/learning.git
 * [new branch]      temp123 -> temp123
bruno @ ~/Documents/projects/git_learning/learning - [temp123] $ 

```

Automaticamente ele criou um merge request, uma solicitação para modificar a MASTER, no Github é chamado Pull Request(PR) no GitLab Merge Request(MR), esse request irá para revisão, aprovação e pode ser feito o merge, uma opção é marcar para deletar a branch temporária assim que o merge for feito.


Para voltar para master `git checkout master`

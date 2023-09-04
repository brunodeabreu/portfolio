---
title: "Deep Learning - prep work"
date: 2023-09-04T08:06:25+03:00
hero: /posts/img/DL_hero.jpeg
description: Pre-Work
menu:
  sidebar:
    name: 1. Pre-work
    identifier: dl-fastai-study
    weight: 1
    parent: dl-fastai-study
---


### 1. Terminal


On Windows we can install PowerShell and WSL using, the first time you

```
wsl --install
```

<br>

* Tips :
    * Terminal Full Screen : `<Alt+Enter>`
    * Switch between users : `sudo -u <user> -i`
    * Check version and python location : `which python`
    * Install everything in `homedir` to do not mix the system python/files with our version of python used to DEV and experiment
    
    
<br>

    
### 2. Install python

  * Github for conda mini-forge and mamba-forge installer [conda-forge/miniforge](https://github.com/conda-forge/miniforge)
  * Linux Manbaforge install

  ```
        wget https://github.com/conda-forge/miniforge/releases/latest/download/Mambaforge-Linux-x86_64.sh
  ```
<br>  
    
  * Install Mambaforge, this going to install several libsn
    
  ```{bash}
    bash Mambaforge-Linux-x86_64.sh 
  ```
    
  * The command `which python` should show right now `/home/bruno/mambaforge/bin/python`
    

<br>


### 3. Setup `fastai`


#### 3.1 Setup conda install

To setup fastai in our notebook [Github-Fastsetup](https://github.com/fastai/fastsetup)

* Run the wget to donwload the setupconda.sh and install

```
wget https://raw.githubusercontent.com/fastai/fastsetup/master/setup-conda.sh

bash setup-conda.sh

```
<br>


### 4. Install other packages using mamba


**Conda** and **Mamba** is two ways of doing the same thing, however  today mamba is very fast


* Install ipython : `mamba install ipython`

* Pytorch install :  [pytorch  get-started](https://pytorch.org/get-started/locally/)

  * CPU : `mamba install pytorch torchvision torchaudio cpuonly -c pytorch`
  
  * CUDA : `mamba install pytorch torchvision torchaudio pytorch-cuda=11.7 -c pytorch -c nvidia`


  * Test : ipython -> import torch
  
  
* Install Jupyter Lab  :   `mamba install jupyterlab`

  * Create a alias to _jupyter lab --no-browser_  : `alias jl=jupyter lab --no-browser`

* Install ipywidgets :  `mamba install ipywidgets`




### 5. Git







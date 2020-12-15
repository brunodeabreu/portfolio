---
title: "Python Básico"
date: 2020-06-08T08:06:25+06:00
hero: /posts/img/python.jpg
description: Anotações de cursos de python
menu:
  sidebar:
    name: Python Básico
    identifier: python-posts-basic
    weight: 10
    parent: python-posts
---




Em meus estudos muitas vezes me pego revendo conceitos básicos, na minha opinião isso é importante para que o conceito seja enraizado e aquele tópico se torne simples, o cursos [Introduction to Python](https://learn.datacamp.com/courses/intro-to-python-for-data-science) e [Intermediate Python](https://learn.datacamp.com/courses/intermediate-python) na Datacamp você encontra todos esses tópicos 

- Tipos de variáveis
- Listas    
- Funções e Pacotes
- [Numpy](https://numpy.org/)
- [Matplotlib](https://matplotlib.org/)
- Discionários
- [Pandas](https://pandas.pydata.org/)


Nos dois primeiros cursos da DataCamp já somos apresentados a alguns pacotes utilizados em ciência de dados, `numpy`, `matplotlib` e `pandas`, esses pacotes são fantásticos se este é seu primeiro contato deixa eu adicionar algumas informações sobre eles para você:

* [Numpy](https://numpy.org/doc/stable/) : Foi criado para trabalhar com tabelas de elementos, chamados de array's multidimensionais que precisam ter o mesmo tipo, ou seja, é um pacote que iremos utilizar para computação matemática, aqui esta um [quickstart](https://numpy.org/learn/).

* [Matplotlib](https://matplotlib.org/contents.html) : É o principal pacote para visualização de dados, ou seja, elaboração de gráficos de diversos tipos, veja essa [galeria](https://matplotlib.org/gallery/index.html).

Um exemplo simples de utilização do matplotlib, com 3 arrays contendo GDP per capita `gdp_cap`, population `pop` e life Expectancy `life_exp` podemos criar o gráfico abaixo

```
# Scatter plot
plt.scatter(x = gdp_cap, y = life_exp, s = np.array(pop) * 2, c = col, alpha = 0.8)

# Previous customizations
plt.xscale('log') 
plt.xlabel('GDP per Capita [in USD]')
plt.ylabel('Life Expectancy [in years]')
plt.title('World Development in 2007')
plt.xticks([1000,10000,100000], ['1k','10k','100k'])

# Additional customizations
plt.text(1550, 71, 'India')
plt.text(5700, 80, 'China')

# Add grid() call
plt.grid(True)

# Show the plot
plt.show()
```

![](/posts/Python/g1.svg)


* [Pandas](https://pandas.pydata.org/docs/): É descrita com a ferramenta de manipulação de dados mais rápida, flexível e fácil de usar.



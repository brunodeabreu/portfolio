---
title: "Python - Data Science Toolkit"
date: 2020-12-15T08:08:25+03:00
hero: /posts/img/Data_Mining.svg
description: Python Data Science tookit 
menu:
  sidebar:
    name: Python Data Science Tookit
    identifier: python-posts-tookit
    parent: python-posts
    weight: 2
---

## O que é o toolkit ? 

O toolkit apresentado no curso [Python Data Science toolbox](https://learn.datacamp.com/courses/python-data-science-toolbox-part-1)  da [Datacamp](datacamp.com) é uma série de ferramentas do python que suportam o trabalho do ciêntista de dados. Ferramentas como funções built-in do python, criação de funções, lambda, iterators, list comprehensions e generators.

Em python temos muitas ferramentas prontas mas é muito importante que o analista saiba usar o core da linguagem como mostra Hugo Bowne-Anderson no blog [Dask and the PyData Stack](https://coiled.io/blog/pydata-dask/) na figura abaixo, python é a primeira camada do pydata stack.



![PyData Stack](https://mk0coiled27knr0w73eb.kinstacdn.com/wp-content/uploads/jake-pydata-eco-pycon-2017.png)


---

## Python - funções

Python, a base do pydata stack oferece diversos recursos que pdoemos utilizar, são normalmente chamados de `built-in functions`:
<br>

**Built-in functions**

* str()
* min()
* max()
* sum()
* type(), etc...

## Criando suas próprias funções

A syntax para criar sua própria função é bem simples, outras refências no site [Python Functions](https://www.w3schools.com/python/python_functions.asp)

```
def function_name(value1, value2):   # Function header
  """ Doc about what the function does """
  ...
  ...
  ...
  return ...

function_name(param1, param2) 
```


Alguns detalhes importantes sobre funções: 

 * **Nested functions**:  funções dentro de funções, utilizando essa função é importante se atentar ao espoco das variáveis.
 
 _Exemplo de Nested Function_
 ```
 # Define echo
def echo(n):
    """Return the inner_echo function."""

    # Define inner_echo
    def inner_echo(word1):
        """Concatenate n copies of word1."""
        echo_word = word1 * n
        return echo_word

    # Return inner_echo
    return inner_echo

# Call echo: twice
twice = echo(2)

# Call echo: thrice
thrice = echo(3)

# Call twice() and thrice() then print
print(twice('hello '), thrice('hello '))


# output > hello hello  hello hello hello
 ```
 
 * **Parâmetros flexíveis**: Sobre os argumentos podemos ter casos especiais como ao utilizar valores default que é definido na criação da função como por exemplo `x=5`, neste caso caso não seja informado o valor de `x` ele recebe por default `5` , em outros casos não sabemos quantos parâmetros deveremos passar definimos assim a função com `*args` nesse caso todos os parametros são atribuido em uma tupla no corpo da função, aprofundando mais temos a opção de passar `**kwargs` argumentos seguidos de identificadores.
 
 
_Exemplo de *args apresentado no curso datacamp_
```
 # Define gibberish
def gibberish(*args):
    """Concatenate strings in *args together."""

    # Initialize an empty string: hodgepodge
    hodgepodge = ''

    # Concatenate the strings in args
    for word in args:
        hodgepodge += word

    # Return hodgepodge
    return hodgepodge

# Call gibberish() with one string: one_word
one_word = gibberish('luke')

# Call gibberish() with five strings: many_words
many_words = gibberish("luke", "leia", "han", "obi", "darth")

# Print one_word and many_words
print(one_word)
print(many_words)
```
 
---

_Exemplo de **kwargs apresentado no curso datacamp_

```
 # Define report_status
def report_status(**kwargs):
    """Print out the status of a movie character."""

    print("\nBEGIN: REPORT\n")

    # Iterate over the key-value pairs of kwargs
    for key, value in kwargs.items():
        # Print out the keys and values, separated by a colon ':'
        print(key + ": " + value)

    print("\nEND REPORT")

# First call to report_status()
report_status(name='luke', affiliation='jedi', status='missing')

# Second call to report_status()
report_status(name='anakin', affiliation='sith lord', status='deceased')
```

---


## Lambda, Map/Reduce

Lambda é uma recurso poderoso que pode ser utilizado para criar funções anônimas. Separei o vídeo abaixo de [Joe James](https://github.com/joeyajames), o nodebook pode ser encontrado no link [Python Lambda Functions](https://github.com/joeyajames/Python/blob/master/Lambda%20Functions/Python%20Lambda%20Functions.ipynb)


<br>

### Python LAMBDA Functions Explained

{{< youtube Ob9rY6PQMfI >}}

<br>


## Iterators

Como destacado por Hugo no curso [Python Data Science toolbox](https://learn.datacamp.com/courses/python-data-science-toolbox-part-2) não temos razão para temer iterators, já usamos em for loops quando interagimos em cada caracter de uma string, ou em uma sequencia de números, são objetos "iterables" : `lists`, `strings` , `dicts` e  `file connections` .

* Iterators team um método chamado `next()` que retorna o valor seguinte até o final uma mensagem `StopIteration`

* utilizando `*` podemos imprimir todos os objetos do iterator

```
word = 'Data'
it = iter(word)
print(*it)

# output : D a t a

```
<br>

* Para iterar em um dicionário utilizamos o método `<dict name>.items()`

* Para iterar em um file connection  criamos o objeto `it = iter()` e utilizamos `next(it)`


### enumerate()


A função enumerate nos oferece a oportunidade de adicionar um contator a cada _"iterable"_ 


```
# Create a list of strings: mutants
mutants = ['charles xavier', 
            'bobby drake', 
            'kurt wagner', 
            'max eisenhardt', 
            'kitty pryde']

# Create a list of tuples: mutant_list
mutant_list = list(enumerate(mutants))

# Change the start index
for index2, value2 in enumerate(mutants, start=1):
    print(index2, value2)
    
    
# Ouput:
1 charles xavier
2 bobby drake
3 kurt wagner
4 max eisenhardt
5 kitty pryde
```


### zip()

Já a função zip nos permite unir um número de objetos iteráveis, por exemplo, podemos fazer o zip de duas listas, que resulta em um iterator de tuplas do tipo zip, com isso podemos converter em uma lista.

```
# Create a list of tuples: mutant_data
mutant_data = list(zip(mutants, aliases, powers))

# Print the list of tuples
print(mutant_data)

# output: 
[('charles xavier', 'prof x', 'telepathy'), ('bobby drake', 'iceman', 'thermokinesis'), ('kurt wagner', 'nightcrawler', 'teleportation'), ('max eisenhardt', 'magneto', 'magnetokinesis'), ('kitty pryde', 'shadowcat', 'intangibility')]

```


## List comprehensions

> "_List comprehensions_" for loops em uma linha

Loops for são computacionalmente custosos para grandes volumes de dados, uma forma inteligente de substituir um simple for loop é utilizando list comprehensions

Syntax:

```
[<output> for n in <iterable object>]
```

.

Uma dica para ler é come,ar do `for`, como neste exemplo:
```
# Create list comprehension: squares
squares = [i**2 for i in range(0,10)]

In [1]: squares
Out[1]: [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

```


## generators 

Basicamente se trocarmos `[]` de uma list comprehension teremos um objeto `generator` a diferença principal entre list comprehensions e generators é que **generator** não armazena o objeto em memória, o que é muito útil com grandes listas de dados.

Em caso de Generator como funções a diferença é que a função retorna um `yield`.

Em resumo list comprehension retorna uma lista de outputs enquanto generator retorna um objeto generator.


_Exemplo_:

```
# Create a list of strings
lannister = ['cersei', 'jaime', 'tywin', 'tyrion', 'joffrey']

# Define generator function get_lengths
def get_lengths(input_list):
    """Generator function that yields the
    length of the strings in input_list."""

    # Yield the length of a string
    for person in input_list:
        yield len(person)

# Print the values generated by get_lengths()
for value in get_lengths(lannister):
    print(value)
    
#Output:
6
5
5
6
7
```


### Thank you!!!!


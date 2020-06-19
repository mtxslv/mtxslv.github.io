---
title: "A Naive Bayes Implementation"
date: 2020-06-19
tags: [Machine Learning, Naive Bayes, Python]
excerpt: "Machine Learning, Naive Bayes, Python"
toc: true
author_profile: true
mathjax: true
---
$$\DeclareMathOperator*{\argmax}{argmax}$$

The Naive Bayes is a method for constructing classifier: that is, a computer program that assign class labels to problem instances. 

# Theorical Explanation

The naive Bayes learner, often called the _naive Bayes classifier_, has a performance comparable to that of neural network and decision tree learning. 
Pretty good, isn't it? But what's the problem framework to apply naive Bayes?

> The naive Bayes classifier applies to  learning tasks where each instance x is described by a  conjunction of attribute values and where  the  target  function $$f(x)$$ can take on any value from some finite set V. A set of training examples of the target function is provided, and a new instance is presented, described by the tuple of attribute values $$<a_1, a_2, ..., a_n>$$. The  learner is asked to predict the target value, or classification, for this new instance.  - Mitchell, p.177

Good. But how are we going to classify new instances? Well, the _most probable target value_ $$v_{map}$$, given the attribute values that describe the instance is that one which leads to the greater posterior probability:

$$ \argmax_{v_i \in V} P(v_j \vert a_1,...,a_n) $$

How to compute such posterior probability? We can use Bayes theorem to rewrite the expression:

$$v_{map} =  P(v_j \vert a_1,...,a_n) = \argmax_{v_i \in V} \frac{P(a_1,...,a_n \vert v_j)P(v_j)}{P(a_1,a_2,...,a_n)}$$

and, since the denominator does not affect the argmax:

$$v_{map} = \argmax_{v_i \in V} P(a_1,...,a_n \vert v_j)P(v_j) $$

We compute $$P(v_j)$$ by counting the frequency with which each target value $$v_j$$ occurs in the training data. To estimate $$P(a_1,...,a_n \vert v_j)$$ is not that feasible, then we are going to make some assumptions. Actually, we are going to make **a naive** assumption.

> (...) given the target value of the instance, the probability of observing the  conjunction $$a_1, a_2, ..., a_n$$, is just the product of the  probabilities for the individual attributes: $$P(a_1, a_2m . . ., a_n  \vert  v_j) = \prod _i P(a_i  \vert  v_j)$$.  - Mitchell, p.177

Doing this substitution, we get the **Naive Bayes Classifier**:

$$v_{nb} = \argmax_{v_j \in V} P(v_j) \prod _i P(a_i|v_j)$$

# Code

The first step is to import some modules:

{% raw %}
```python
import numpy as np
import pandas as pd
```
{% endraw %}

The function will receive to parameters:
- dataset: a pandas dataframe, with the classes allocated in the last column. Each of the other columns are the attributes, and the rows contains the training examples
- instance: a python list. A list of the instance attribute.

and then it returns $$v_{nb}$$.

{% raw %}
```python
def naive_bayes(dataset,instance):
  """
  Parameters: the dataset (a pandas dataframe, with classes as the last column) and the instance to be classified (a list)
  Returns: v_map (the most probable class of the instance, given its attributes)
  """
  v_nb = 'idk'

  existing_classes = list(set(dataset.iloc[:,-1].tolist())) # this is the v_j \in V
  classes_probability = []

  for v in existing_classes:
    P_vj = dataset.iloc[:,-1].values.tolist().count(v) / dataset.iloc[:,-1].shape[0]
    prod = 1
    subset = dataset[dataset[dataset.columns.tolist()[-1]] == v]
    for it in range(0, len(instance)):
      counting_occurence = subset.iloc[:,it].tolist().count(instance[it])
      prod = prod *(counting_occurence/subset.shape[0])
    classes_probability.append(P_vj*prod)

  v_nb = existing_classes[np.argmax(classes_probability)]

  return v_nb
```
{% endraw %}

The first step of the code is to collect all the classes from the last Pandas Data Serie. Then, we allocate an empty list to collect the probability of each class. 

For each possible class _v_ we compute the following process:
- compute $$P(v_j)$$ by counting how many times class _v_ appears in the dataset
- get the subset of the pandas dataframe for which the class is _v_. Allocate a productory starting with 1.
- Start a new loop over each subset column, and count how many times the respective instance attribute value appears.
- Append each probability value in the ```classes_probability``` list.

At the end, we return ```v_nb``` as the class _v_ with the greatest respective probability.

# Example

In Mitchell, p.71, we see a table related to the "Play Tennis" problem. In p.190 we are given the instance ['Sunny', 'Cool','High','Strong']. We are then ask to predict if it should to play tennis or not (to classify the instance).

First of all, we implement the table of page 71:

{% raw %}
```python
import pandas as pd

d = {'Outlook':['Sunny','Sunny','Overcast','Rain','Rain','Rain','Overcast','Sunny','Sunny','Rain','Sunny','Overcast','Overcast','Rain'],
     'Temperature':['Hot','Hot','Hot','Mild','Cool','Cool','Cool','Mild','Cool','Mild','Mild','Mild','Hot','Mild'],
     'Humidity':['High','High','High','High','Normal','Normal','Normal','High','Normal','Normal','Normal','High','Normal','High',],
     'Wind':['Weak','Strong','Weak','Weak','Weak','Strong','Strong','Weak','Weak','Weak','Strong','Strong','Weak','Strong'],
     'PlayTennis':['No','No','Yes','Yes','Yes','No','Yes','No','Yes','Yes','Yes','Yes','Yes','No']}
df = pd.DataFrame(data= d)   
df  
``` 
{% endraw %}

Such dataframe can be seen below:

| Outlook | Temperature |Humidity | Wind | PlayTennis |
| :---: | :---: | :---: | :---: | :---: |
| Sunny | Hot |	High |	Weak |	No |
| Sunny | Hot 	|High| 	Strong| 	No |
| Overcast | Hot 	|High 	|Weak 	|Yes |
| Rain |	Mild 	|High 	|Weak 	|Yes |
| Rain | Cool |	Normal 	|Weak 	|Yes |
| Rain | Cool |	Normal |	Strong 	|No |
| Overcast | Cool 	|Normal |	Strong 	|Yes |
| Sunny | Mild| 	High |	Weak 	|No |
| Sunny | Cool 	|Normal 	|Weak 	|Yes |
| Rain | Mild |	Normal |	Weak 	|Yes |
| Sunny | Mild| 	Normal 	|Strong| 	Yes |
| Overcast | Mild 	|High |	Strong| 	Yes |
| Overcast | Hot| 	Normal |	Weak 	|Yes |
| Rain | Mild 	|High 	|Strong 	|No |

The instance can be coded as:
```python
instance = ['Sunny', 'Cool','High','Strong']
```

Since I already coded the _Naive Bayes Classifier_, we just need to apply it:
{% raw %}
```python
classificacao = naive_bayes(df, instance)
```
{% endraw %}

As we can seen, the answer is 'No'.

# References

- [Creating a Pandas Dataframe](https://pandas.pydata.org/pandas-docs/version/0.23.4/generated/pandas.DataFrame.html)
- [Counting Occurences in pandas series](https://stackoverflow.com/questions/35277075/python-pandas-counting-the-occurrences-of-a-specific-value)
- [Counting Occurences in lists](https://www.guru99.com/python-list-count.html)
- [Getting Pandas Dataframe Column Name](https://www.geeksforgeeks.org/how-to-get-column-names-in-pandas-dataframe/)
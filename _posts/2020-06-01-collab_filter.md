---
title: "A Collaborative Filtering Implementation"
date: 2020-06-01
tags: [Machine Learning, k Nearest Neighbor, Python]
excerpt: "Machine Learning, k Nearest Neighbor, Python"
toc: true
author_profile: true
mathjax: true
---

Imagine you are the owner of a movie renting website (Netflix, maybe?) and have a huge database of users' ratings on some of the movies. You have a bunch of new users (let's call each of them _active user_) and want to sugest options to them. How to do it?

Well... you can suppose these people would like to watch movies they themselves give high rates. Ok, but how to "estimate" such rates, if they have never watched them?

If you have read about _k Nearest Neighbors_ already, you must have thought "Collaborative Filtering!!".

The lazy algorithm _k Nearest Neighbors_ is a Machine Learning method for estimating values for a test instance using examples from a training set. It is based on the idea similar examples should be classified similarly. Clever, isn't it?

> Lazy algorithms: algorithms that does nothing in _training time_. It waits to generalize only during _test time_. It is oposed to eager algorithms (like Decision Trees and Neural Networks), which consumes the training set during training time to find the best hypothesis that fits the data.  

What does make an instance similar to another? If examples are represented by real valued vectors, they can be seen as points in a multidimensional Euclidean space. If it holds, the _spacially closer_ the vector is to another, the similar they are.

But how to do it in the context of ratings estimation? First, we use the concept of _distance weighting_: weight all examples accordingly to their distance to the query instance (we use all training examples, the closer they are, the stronger their influence on the query estimation).

Ok, this was a very fast explanation about the concepts related to the problem and its solution. Let's dive in the mathematical expressions and the python code that implements them.

# Mathematical Expressions

 The paper the expressions below come from can be found [here](https://courses.cs.washington.edu/courses/csep546/17au/psetwww/2/algsweb.pdf) and the implemented mathematical formulas for making the predictions are equations 1 and 2. The following explanations are excerpts from the mentioned paper.

The user database consists of a set of votes $$v_{i,j}$$ corresponding to the vote for user _i_ on item _j_. If $$I_i$$ is the set of items on which user _i_ has voted, we first define the mean vote for user _i_ as:

$$\overline{v_i} = \frac{1}{|I_i|} \sum_{j \in I_i}^{}v_{i,j}$$

We predict the votes of the active user (indicated with a subscript _a_) based on some partial information regarding the active user and a set of weights calculated from the user database. We assume the predicted vote of the active user for item _k_ $$p_{a,k}$$, is a weighted sum of the votes of the other users:

$$p_{a,k} = \overline{v_a} + \kappa \sum_{i=1}^{n} w(a,i)(v_{i,k} - \overline{v_i})$$

where _n_ is the number of users in the collaborative filtering database with nonzero weights. The weights _w(i,a)_ we use are the correlations between each user _i_ and the active user _a_. $$\kappa$$ is a normalization factor such that the absolute values of the weights sum to unity (then $$\kappa = (\sum \vert w(a,i) \vert )^{-1}$$).

For the weights, I use the Pearson correlation coefficient. The correlation between users _a_ and _i_ is:

$$w(a,i) = \frac{ \sum_{}^{}\mathop{}_{\mkern-5mu j} (v_{a,j} - \overline{v_a} )(v_{i,j} - \overline{v_i}) }{ \sqrt{ \sum_{}^{}\mathop{}_{\mkern-5mu j} (v_{a,j} - \overline{v_a} )^2  \sum_{}^{}\mathop{}_{\mkern-5mu j} (v_{i,j} - \overline{v_i} )^2 } }$$

The two expressions described above are enough for the collaborative filtering project.

# Code

coming soon ;)
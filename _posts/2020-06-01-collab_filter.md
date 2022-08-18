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

We predict the votes of the active user (indicated with a subscript _a_) based on some partial information regarding the active user and a set of weights calculated from the user database. We assume the predicted vote of the active user for item _k_ , $$p_{a,k}$$, is a weighted sum of the votes of the other users:

$$p_{a,k} = \overline{v_a} + \kappa \sum_{i=1}^{n} w(a,i)(v_{i,k} - \overline{v_i})$$

where _n_ is the number of users in the collaborative filtering database with nonzero weights. The weights _w(i,a)_ we use are the correlations between each user _i_ and the active user _a_. $$\kappa$$ is a normalization factor such that the absolute values of the weights sum to unity (then $$\kappa = (\sum \vert w(a,i) \vert )^{-1}$$).

For the weights, I use the Pearson correlation coefficient. The correlation between users _a_ and _i_ is:

$$w(a,i) = \frac{ \sum_{}^{}\mathop{}_{\mkern-5mu j} (v_{a,j} - \overline{v_a} )(v_{i,j} - \overline{v_i}) }{ \sqrt{ \sum_{}^{}\mathop{}_{\mkern-5mu j} (v_{a,j} - \overline{v_a} )^2  \sum_{}^{}\mathop{}_{\mkern-5mu j} (v_{i,j} - \overline{v_i} )^2 } }$$

where the summations over _j_ are over the items for which both users _a_ and _i_ have recorded votes.

The two expressions described above are enough for the collaborative filtering project.

# Algorithm To Be Used

Notice some points to be satisfied by the algorithm during the calculation of $$p_{a,k}$$:
1. The summation factor in $$p_{a,k}$$ should not account for users that did not vote on item _k_, because item $$v_{i,k}$$ is not defined. Then we must ignore such users.
2. There can exist an extreme case where the user rated item _k_ but has no other movie in common with the active user, so $$w(a,i)$$ is not defined. 
3. There can exist an even more extreme case where no $$w(a,i)$$ could be defined. If this happens, I define the estimated rate ($$p_{a,k}$$) as the mean vote of the active user: $$\overline{v_a}$$.

The achieved algorithm to estimate the rating that considers the points above can be read below:

```
CollaborativeFiltering(active_user,item_to_be_rated, training_set)

* _estimation_ <- the mean of _active user_ votes
* usersID <- all users' ID from the training set 
* For each user in usersID, do:
    - Get user votes (set of logs)
    - If user voted in item_to_be_rated, do:
        - Get Items both users (active one and training one) voted in
        - If Items is not empty:
          - calculate w(a,i) and the difference between the user vote on item_to_be_rated and his/her average rate.
* If w(a,i) is not empty:
    - Compute normalizing factor _k_.
* If w(a,i) and differences are both non empty:
    - Add weighting factor to the estimate of active user rate on item_to_be_rated.
* Return _estimation_
```

# Codes

I will suppose the dataset is a set of logs. Each row has the format: _movieID_ , _customerID_ , _rating_. The first element is a movie identifying number, the second is the user identifying number and the last one is the rating. 

The training set is made of all "training users" rates on all available movies (products). A query instance is the set of ratings _active user_ did and the movie we want to estimate the rate (_k_).

Let's set the information of row format. Since the dataset is a numpy array or a list of lists (0 based indexing), we have: 
{% raw %}
```python
import numpy as np

movie_column = 0
user_column = 1
ratings_column = 2
```
{% endraw %}

 The project is then divided in three functions: ```mtxslv_user_ratings()```, ```intersection()``` and ```mtxslv_collab_filter()```. Let's comment each of them.

## Geting the User Ratings

Since the algorithm described in the previous section needs to iterate over each training user, we need to get the training subset that collects all training user rates (notice this subset is $$I_i$$).

{% raw %}
```python
def mtxslv_user_ratings(user_id, dataset):
  subset = [] 
  for it in range(0,np.shape(dataset)[0]):
    if (dataset[it,user_column] == user_id):
      subset.append(dataset[it,:].tolist())  
  return np.array(subset)
```
{% endraw %}

The function receives the ```dataset``` and look for all occurences of ```user_id``` in its ```user_column``` and returns such subset. Notice if no ```user_id``` occurence is found, the function returns an empty numpy array. 

## Getting the Intersection Between Votes

The Collaborative Filtering Algorithm  needs to know what movies both users voted also. Since we have lists for rated movies (one list per user), we just need to get the intersection of such sets. I used a GeekForGeeks code and you can know more reading [their amazing post](https://www.geeksforgeeks.org/python-intersection-two-lists/).

{% raw %}
```python
# Python program to illustrate the intersection 
# of two lists in most simple way 
# from https://www.geeksforgeeks.org/python-intersection-two-lists/
def intersection(lst1, lst2): 
    return list(set(lst1) & set(lst2)) 
```
{% endraw %}

## The Collaborative Filtering Code

The Collaborative Filtering Code receives the ```instance``` (set of active user logs), the ```product_id``` (what movie the rating must be predicted) and the ```training_set``` (set of instances). These parameter are all numpy arrays. It returns an estimation of the active user vote. 

{% raw %}
```python
def mtxslv_collab_filter(instance, product_id,training_set):

  training_set_list = training_set.tolist()
  average_rating_active_user = np.mean(instance[:,ratings_column])
  estimation =  average_rating_active_user

  w = [] 
  k = 0  
  user_vote_minus_average = []

  user_ids = set(training_set[:,user_column])

  for user in user_ids:
    user_subset = mtxslv_user_ratings(user,training_set)
    if(user_subset[:,movie_column].tolist().count(product_id)): 
      user_average = np.mean(user_subset[:,ratings_column])  
      movies_both_users_voted = intersection(user_subset[:,movie_column].tolist(),
                                             instance[:,movie_column].tolist()) 
      if not( not movies_both_users_voted ):
        user_index_product_id = user_subset[:,movie_column].tolist().index(product_id) 
                                                                     
        w_numerator = 0  
        w_active_denominator_factor = 0 
        w_training_usr_denominator_factor = 0 

        for movie in movies_both_users_voted:
          
          training_user_index_for_movie = user_subset[:,movie_column].tolist().index(movie)
          active_user_index_for_movie = instance[:,movie_column].tolist().index(movie)    

          w_numerator = w_numerator + (instance[active_user_index_for_movie,ratings_column]-average_rating_active_user)*(user_subset[training_user_index_for_movie,ratings_column]-user_average)
          w_active_denominator_factor = w_active_denominator_factor + np.power((instance[active_user_index_for_movie,ratings_column]-average_rating_active_user),2)
          w_training_usr_denominator_factor = w_training_usr_denominator_factor + np.power( (user_subset[training_user_index_for_movie,ratings_column]-user_average) ,2)

        w.append(w_numerator/np.sqrt(w_active_denominator_factor*w_training_usr_denominator_factor))
        user_vote_minus_average.append(user_subset[user_index_product_id,ratings_column]-user_average)
 
  if not(not w):
    k = 1 / np.sum( np.abs(w) )

  if not(not w or not user_vote_minus_average):  
    estimation =  estimation + k* np.dot(w,user_vote_minus_average) 

  return estimation
```
{% endraw %}

The next step is to get the list of logs from the numpy training set. The mean of the column ```ratings_column``` is the average rating of the active user ($$\overline{v_a}$$). Let's, initially, suppose the active user estimate rating equals his/her average rating.

I allocate an empty list ```w```, which will collect all weights/correlation measures; the normalizing term ```k``` and an empty list ```user_vote_minus_average``` ($$v_{i,k} - \overline{v_i}$$).

Now I get all user ids, so I can iterate user by user. Then, for each _training set user_ I get his/her subset and, if this user has voted/rated ```product_id```, I begin two main calculations:

1. Compute this ```user_average``` ratings;
2. Get the intersection of movie lists, that is, ```movies_both_users_voted```. If such list is not empty, we do the following...

Get the index in ```user_subset``` where ```product_id``` appears (```user_index_product_id```).

Allocate the following values: ```w_numerator``` ($$ \sum_{}^{}\mathop{}_{\mkern-5mu j} (v_{a,j} - \overline{v_a} )(v_{i,j} - \overline{v_i})$$), ```w_active_denominator_factor``` ($$\sum_{}^{}\mathop{}_{\mkern-5mu j} (v_{a,j} - \overline{v_a} )^2$$) and ```w_training_usr_denominator_factor``` ($$\sum_{}^{}\mathop{}_{\mkern-5mu j} (v_{i,j} - \overline{v_i} )^2$$).

Now it begins another iterating process (for each _movie_ in ```movies_both_users_voted```): get the ratings and compute ```w_numerator```, ```w_active_denominator_factor``` and ```w_training_usr_denominator_factor```.

Now append the computation of $$w(a,i)$$ in ```w``` list and append the computation of $$v_{i,k} - \overline{v_i}$$ in ```user_vote_minus_average```.

All the previous processes are nested loops. After they are over, I check if ```w``` is empty: if not, ```k``` equals the inverse of the sum of the absolute ```w``` values.

If ```w``` and ```user_vote_minus_average``` **are non empty at the same time** I update the ```estimation``` value due to the addition of the scaled (multiplied by ```k```) dot product of ```w``` and ```user_vote_minus_average``` (this dot product is the same thing as $$\sum_{i=1}^{n} w(a,i)(v_{i,k} - \overline{v_i})$$).

The function returns ```estimation```.

# A Brief Example

For an example, let's suppose the following data:
* There are 6 products (R1,R2,R3,R4,R5 and R6);
* There are 3 "training set" users (Bob, Chris and Diana);
* The query instance is Alice's ratings on the 5.

| User/Product | R1 | R2 | R3 | R4 | R5 | R6 |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | 
| Alice  | 2  | - | 4 | 4 | - | 5 |
| Bob | 1 | 5 | 4 | - | 3 | 4 |
| Chris  | 5 | 2 | - | 2 | 1 | - |
| Diana | 3 | - | 2 | 2 | - | 4 |

But the data does not come in this table format. Actually, let's imagine a log file where each row are: _movieID_, _customerID_,_rating_. For simplicity, let's suppose Alice's ID 1, Bob's ID 2, Chris' ID 3 and Diana's 4.

Our _training data_, then, come in the following format. Notice since Alice is the query instance, she does not appear in the training data.

| _movieID_ | _customerID_ | _rating_ |
| :---: | :---: | :---: |
| 1 | 2 | 1 |
| 2 | 2 | 5 |
| 3 | 2 | 4 |
| 5 | 2 | 3 |
| 6 | 2 | 4 |
| 1 | 3 | 5 |
| 2 | 3 | 2 |
| 4 | 3 | 2 |
| 5 | 3 | 1 |
| 1 | 4 | 3 |
| 3 | 4 | 2 |
| 4 | 4 | 2 |
| 6 | 4 | 4 |

Query instance (Alice's logs) is:

| _movieID_ | _customerID_ | _rating_ |
| :---: | :---: | :---: |
| 1 | 1 | 2 |
| 3 | 1 | 4 |
| 4 | 1 | 4 |
| 6 | 1 | 5 |

What we want to know (to predict) is Alice's rate on movie 5. We can easily estimate this value using ```mtxslv_collab_filter()```. Let's define training and testing data:

```python
training_set_example = np.array([[1,2,1],[2,2,5],[3,2,4],[5,2,3],
                         [6,2,4],[1,3,5],[2,3,2],[4,3,2],
                         [5,3,1],[1,4,3],[3,4,2],[4,4,2],
                         [6,4,4]])

testing_instance = np.array([[1,1,2],[3,1,4],[4,1,4],[6,1,5]])
```

Now apply the function!

```python
estimativa = mtxslv_collab_filter(testing_instance,5,training_set_example)
```
The manually calculated value is _4.336075363_. 
Try it yourself: ```estimativa``` equals _4.336096188777122_.

Notice this value makes sense, since Alice's tendencies roughly follow Bob's. Actually, Alice's ratings apparently is one unity above Bob's.

# References

The learning process often needs the reading of material other people has produced, to understand things we didn't or to find out better ways of doing things we do the old way. Here are the references I accessed  during the designing process:

* [How to know if a list is empty (Geeks For Geeks)?](https://www.geeksforgeeks.org/python-check-whether-list-empty-not/)

* [Pop a List out of another (StackOverFlow)](https://stackoverflow.com/questions/21860605/python-remove-lists-from-list-of-lists-similar-functionality-to-pop)

* [Intersection of Two Lists (Geeks For Geeks)](https://www.geeksforgeeks.org/python-intersection-two-lists/)

* [Python Lists Indexing (Programiz)](https://www.programiz.com/python-programming/methods/list/index)

* [Collaborative Filtering Paper](https://courses.cs.washington.edu/courses/csep546/17au/psetwww/2/algsweb.pdf)

* [Python List Index Method with Examples (Scaler Topics)](https://www.scaler.com/topics/python-list-index-method/)

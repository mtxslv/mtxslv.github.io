---
title: "An Introduction do Neural Networks: Solving the XOR problem"
date: 2020-09-03
tags: [Deep Learning, TensorFlow, Python]
excerpt: "Deep Learning, TensorFlow, Python"
toc: true
author_profile: true
mathjax: true
---

When I started AI, I remember one of the first examples I watched working was MNIST(or CIFAR10, I don't remember very well). Looking for online tutorials, this example appears over and over, so I suppose it is a common practice to start DL courses with such idea. Although a very exciting starting point (come on, we are literally seeing a classifer recognizing images!), it kind of masks the math behind it and makes it harder to understand what is going under the hood (if you are a beginner). That is why I would like to "start" with a different example.

This example may actually look too simple to us all because we already know how to tackle it, but in reality it stunned very good mathematitians and AI theorists some time ago.

# The 2-Variable XOR Problem

What is the XOR logical gate? Imagine two inputs that can assume only binary values each (0 or 1). The output goes to 1 only when both inputs are different. That is:

| x1 | x2 | y |
| :--: | :--: | :--: |
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

Now we must propose an artificial neural network that can _overfit_ the dataset. That is, we should design a network that takes ```x1``` and ```x2``` as inputs and successfully outputs ```y```.

Following the development proposed by [Ian Goodfellow et al](https://www.deeplearningbook.org/contents/mlp.html), let's use the mean squared error function (just like a regression problem) for the sake of simplicity.

We already know what we should do. Now we should choose the model f($$x$$;$$\theta$$).

# Theoretical Modelling (Let's think for a while...)
## Only one Neuron (A Linear Model)

Our first attempt will consider a linear model. Such model implements the following equation:

$$ŷ = f(\vec{x};\vec{w},b) = \vec{x}^{T}\vec{w}+b$$.

Seems nice, isn't it? Will it work? Well... unfortunatelly, no. 

How can I now it beforehand? Let's take a closer look to the expression. It is implementing a linear relation. Imagine _f_ is a surface over the $$\vec{x}$$ plane, and its height equals the output. The surface must have height equalling 1 over the points $$[0, 1]$$ and $$[1, 0]$$ and 0 height (it would be touching the plane) at points $$[0, 0]$$ and $$[1, 1]$$. Could a hyperplane behave this way? No, it cannot.

Another way of think about it is to imagine the network trying to separate the points. The points labeled with 1 must remain together in one side of line. The other ones (labelled with 0) stay on the other side of the line.

Take a look at the following image. I plotted each point with a different color. Notice the artificial neural net has to output '1' to the green and black point, and '0' to the remaining ones. In other words, it need to separate the green and black points from the purple and red points. It cannot do such a task. The net will ultimately fail.

<figure>
  <img src="/images/posts_images/2020-09-03-xor_neural_net/original_space.png" alt="Original Space with example points">
  <figcaption>Original Space with example points</figcaption>
</figure>>

## More than only one neuron (network)

Let's add a linear hidden layer with only two neurons. 

$$ŷ = f^{(2)}(f^{(1)}(\vec{x}))$$ 

or

$$ŷ = f^{(2)}(\vec{h};\vec{w},b)$$ , such that $$\vec{h} = f^{(1)}(\vec{x};W,\vec{c})$$.

Sounds like we are making real improvements here, but a linear function of a linear function makes the whole thing still linear.


### We are going nowhere!

Notice what we are doing here: we are stacking linear layers. What does a linear layer do? How can we visualize its effect mathematically?

Before I explain the layer, let's simplify a little bit by ignoring the bias term in each neuron ($$\vec{c}$$ and $$b$$), alright?

Ok, now consider the following image (which [can be found here](https://thenounproject.com/term/partial-neural-network/961659/)):

<figure>
  <img src="/images/posts_images/2020-09-03-xor_neural_net/partial_neural_net.png" alt="Partial Neural Net">
  <figcaption>A Partial Neural Net</figcaption>
</figure>>

It is not our own net. Remember: We stacked layers with 2 neurons only, and here we have a hidden layer with 3 neurons. Even though it is not our neural network, it'll be useful to mathematically visualize what's going on.

Let's focus only on the input and hidden layers. We can be sure this network was designed to a 2D input (like our example data), because there is two neurons in the input layer. Let's call our inputs neurons using the following subscripts: $$i_{1}$$ and $$i_{2}$$. That means the first and the second _input_ neurons. Watch out! When I say "the first" I mean "the higher", "the second" then means "the lower", ok? 

The architecture consideration of the hidden layer chose three neurons. That is ok. There is not too much to talk about this choose. I will call the output of the three hidden neurons: $$h_1$$,$$h_2$$ and $$h_3$$. And again, $$h_1$$ is the output of the highest hidden layer neuron, $$h_2$$ is the output of the hidden layer neuron in the middle and $$h_3$$ is the output of the last hidden layer neuron.

> I am repeating myself several times about the neurons' positions because I want to be clear about which neuron I'm talking about.

Now let's see the output of the first hidden layer neuron, that is, let's see $$h_1$$. We now $$h_1$$ is a weighted sum of the inputs, which are written as $$\vec{x}$$ in the original formulation, but we'll use $$i$$ so we can relate to _input_. In one equation:

$$h_1 = w_{1,1} * i_1 + w_{1,2} * i_2$$.

> don't you forget we're ignoring the bias!

In this representation, the first subscript of the weight means "what hidden layer neuron output I'm related to?", then "1" means "the output of the first neuron". The second subscript of the weight means "what input will multiply this weight?". Then "1" means "this weight is going to multiply the first input" and "2" means "this weight is going to multiply the second input".

The same reasoning can be applied to the following hidden layer neurons, what leads to:

$$h_2 = w_{2,1} * i_1 + w_{2,2} * i_2$$

and

$$h_3 = w_{3,1} * i_1 + w_{3,2} * i_2$$.

Now we should pay attention to the fact we have 3 linear equations. If you have ever enrolled in a Linear Algebra class, you know we can arrange these equations in a grid-like structure. If you guessed "a matrix equation", you're right!

The matrix structure looks like this:

$$
\begin{bmatrix}
h_1 \\
h_2 \\
h_3 \\
\end{bmatrix}
=
\begin{bmatrix}
w_{1,1} & w_{1,2} \\
w_{2,1} & w_{2,2} \\
w_{3,1} & w_{2,3} \\
\end{bmatrix}
\begin{bmatrix}
i_1 \\
i_2 \\
\end{bmatrix}
$$

To simplify even further, let's shorten our equation by representing the hidden layer output vector by $$\vec{h}$$, the input vector by $$\vec{i}$$ and the weight matrix by $$W$$:

$$\vec{h} = W \vec{i} $$.

If we connect the output neuron to the hidden layer, we have: 

$$\vec{o} = M \vec{h}$$,
where $$\vec{o}$$ is a 2D vector (each position contains the output of the output neurons) and $$M$$ is the matrix that maps the hidden layer representation to the output values (the $$\vec{w}$$ in the original formulation). Here, $$ŷ = \vec{o}$$. Expanding it we have:

$$\vec{o} = M W \vec{i}$$,

where $$MW$$ gives another matrix, because this is just a matrix multiplication. Let's call it _A_. Then:

$$\vec{o} = A \vec{i}$$

Now suppose a different neural network, like the following (you can find it [here](https://thenounproject.com/term/dueling-neural-networks/1714867/)):

<figure>
  <img src="/images/posts_images/2020-09-03-xor_neural_net/two_hidden_layer_neural_net.png" alt="A Neural Net with 2 Hidden Layers">
  <figcaption>A Neural Net with 2 Hidden Layers</figcaption>
</figure>>

This network has only one output neuron and two hidden layers (the first one with 4 neurons and the second one with three neurons). The input is a 6-D vector. Again we are ignoring bias terms. Let's see the shortened matrix equation of this net:

$$o = M H_1 H_2 \vec{i}$$.

Here , the output _o_ is a scalar (we have only one output neuron), and two hidden layers ($$H_2$$ is the matrix of weights that maps the input to the hidden layer with 4 neurons and $$H_1$$ maps the 4 neurons output to the 3 hidden layer neurons outputs). M maps the internal representation to the output scalar.

Notice $$M H_1 H_2$$ is a matrix multiplication that results in a matrix again. Let's call it _B_. Then:

$$o = B \vec{i}$$.

Can you see where we're going? It doesn't matter how many linear layers we stack, they'll always be matrix in the end. To our Machine Learning perspective, it means it doesn't mean how many layers we stack, we'll never learn a non linear behaviour in the data, because the model can only learn linear relations (the model itself is a linear function anyway).

I hope I convinced you that stacking linear layers will get us nowhere, but trust me: all is not lost. We just need another mechanism to learn non-linear relationships in the data. This "mechanism" I'll introduce is called _Activation Functions_. 

> If you want to read another explanation on why a stack of linear layers is still linear, please access this [Google's Machine Learning Crash Course page](https://developers.google.com/machine-learning/crash-course/introduction-to-neural-networks/anatomy).



### Activation Functions!

"Activation Function" is a function that generates an output to the neuron, based on its inputs. The name comes from the neuroscience heirloom. Although there are several activation functions, I'll focus on only one to explain what they do. Let's meet the ReLU (Rectified Linear Unit) activation function.

<figure>
  <img src="/images/posts_images/2020-09-03-xor_neural_net/800px-ReLU_and_Nonnegative_Soft_Thresholding_Functions.svg.png" alt="Meet the ReLU">
  <figcaption>Meet the ReLU!</figcaption>
</figure>>

In the [figure above](https://search.creativecommons.org/photos/8e35394e-5b00-4560-8160-1b58909e6452) we have, on the left (red function), the ReLU. As can be seen in the image, it is defined by the ```max``` operation between the input and '0'. It means the ReLU looks to the input and thinks: is it greater than '0'? If yes, the output is the input itself. If it is not, the output is zero. That said, we see every input point greater than '0' has an height equaling its distance to the origin of the graph. That's why the positive graph's half is a perfect diagonal straight line. When we look to the other half, all _x's_ are negative, so all the outputs are zero. That's why we have a perfect horizontal line.

Now imagine that we have a lot of points distributed along a line: some of them lie on the negative side of the line, and some of them lie on the positive side. Suppose I apply the ReLU function on them. What happens to the distribution? The points on the positive side remains in the same place, they don't move because their position is greater than 0. On the other hand, the points from the negative side will crowd on the origin.   

Another nice property of the ReLU is its slope (or derivative, or even _tangent_). If you have a little background on Machine/Deep Learning, you know this concept is fundamental for the neural nets algorithms. On the graph's left side we have an horizontal line: it has no slope, so the derivative is 0. On the other side we've got a perfect diagonal line: the slope is 1 (tangent of 45º).

Here we have sort of a problem... what's the slope at _x=0_? Is it 0 (like on the left side) or 1 (right side slope)? That's called a **non-differentiable point**. Due to this limitation, people developed the ```softplus function```, which is defined as $$\ln(1+e^{x})$$. The softplus function can be [seen below](https://commons.wikimedia.org/wiki/File:Rectifier_and_softplus_functions.svg):

<figure>
  <img src="/images/posts_images/2020-09-03-xor_neural_net/495px-Rectifier_and_softplus_functions.svg.png" alt="ReLU and softplus comparison">
  <figcaption>ReLU and softplus comparison</figcaption>
</figure>>

> Empirically, it is better to use the ReLU instead of the softplus. Furthermore, the dead ReLU is a more important problem than the non-differentiability at the origin. Then, at the end, the pros (simple evaluation and simple slope) outweight the cons (dead neuron and non-differentiability at the origin).

Ok... so far we've discussed the 1D effect of ReLU. What happens when we apply ReLU to a set of 2D points?   

<figure>
  <img src="/images/posts_images/2020-09-03-xor_neural_net/pontos_antes_relu.png" alt="points position before relu">
  <figcaption>2D Points (original)</figcaption>
</figure>>

First, consider this set of 8 colorful points. Pay attention to their _x, y_ positions: the blue ones have positive coordinates both; the green, red and orange ones have negative x coordinates; the remaining ones have positive x coordinates, but negative y ones. Suppose we applied ReLU to the points (to each coordinate). What happens?

<figure>
  <img src="/images/posts_images/2020-09-03-xor_neural_net/pontos_depois_relu.png" alt="points position after relu">
  <figcaption>2D Points (after ReLU)</figcaption>
</figure>>

As we can see, the blue points didn't move. Why? Because their coordinates are positive, so the ReLU does not change their values. The pink and yellow points were moved upwards. It happened because their negative coordinates were the _y_ ones. The red and green points were moved rightwards. It happened due to the fact their _x_ coordinates were negative. What about the orange point? Did it disappear? Well... no. Note every moved coordinate became zero (ReLU effect, right?) and the orange's non negative coordinate was zero (just like the black's one). The black and orange points ended up in the same place (the origin), and the image just shows the black dot.

The most important thing to remember from this example is the points didn't move the same way (some of them did not move at all). That effect is what we call "non linear" and that's very important to neural networks. Some paragraphs above I explained why applying linear functions several times would get us nowhere. Visually what's happening is the matrix multiplications are moving everybody _sorta_ the same way (you can find more about it [here](https://youtu.be/kYB8IZa5AuE?t=156)). 

Now we have a powerful tool to help our network with the XOR problem (and with virtually every problem): nonlinearities help us to bend and distort space! The neural network that uses it can move examples more freely so it can learn better relationships in the data!

> You can read more about "space-bender" neural networks in [Colah's amazing blog post](https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/)

<figure>
  <img src="{{ '/images/posts_images/2020-09-03-xor_neural_net/1472552733-doctor-strange-benedict-cumberbatch.gif' | relative_url }}" alt="doctor strange tripping">
  <figcaption>Neural Nets Bend Space? Huh??</figcaption>
</figure>

## More than only one neuron , the return (let's use a non-linearity)

Ok, we know we cannot stack linear functions. It will lead us anywhere. The solution? ReLU activation function. But maybe something is still confusing: _where it goes_? 

I believe the [following image](https://commons.wikimedia.org/wiki/File:ArtificialNeuronModel_english.png) will help. This is the artificial neuron "classic" model (_classic_ here means _we always see it when we start doing Machine/Deep Learning_):

<figure>
  <img src="/images/posts_images/2020-09-03-xor_neural_net/800px-ArtificialNeuronModel_english.png" alt="classic artificial neuron">
  <figcaption>Our good ol' Artificial Neuron</figcaption>
</figure>>

Recall our previous formulation:
$$ŷ = f^{(2)}(\vec{h};\vec{w},b)$$ , such that $$\vec{h} = f^{(1)}(\vec{x};W,\vec{c})$$.

Here, a "neuron" can be seen as the process which produces a particular output $$h_i$$ or $$ŷ_i$$. Let's focus on the $$h_i$$. Previously it was explained that, in our context, it equals :

$$h_i = w_{i,1} * i_1 + w_{i,2} * i_2$$. 

Here, $$w_{i,j}$$ are the weights that produces the _i-th_ hidden-layer output. The _i_ elements are the inputs (the _x_ in the image). The ```transfer function``` comprises the two products and the sum. Actually, it can be written as $$h_i = \vec{w_i} \vec{i}$$ either, which means _the inner product between the i-th_ weights and the input (here is clearer the transfer function is the inner product itself). The input $$net_j$$ is $$h_i$$, and we'll finally deal with the activation function!

In the original formulation, there's no non-linear activation function. Notice I wrote:
$$\vec{o} = ŷ = M * \vec{h}$$ . 

The transformation is linear, right? What we are going to do now is to add the ReLU, such that: $$\vec{o} = ŷ = M * ReLU( \vec{h} )$$. Here, the threshold $$\theta_j$$ does not exist.

So far it was said the activation function occurs after each inner product. If we think the layer as outputing a vector, the activation funcion is applied point-wise.

# Visualizing Results (Function Composition)

The model we chose to use has a hidden layer followed by ReLU nonlinearity. It implements the global function (considering the bias):

$$f(\vec{x};W,\vec{c},\vec{w},b) = \vec{w}^{T} max{0,W^{T}\vec{x}+\vec{c}}+b$$ .

A specified solution to the XOR problem has the following parameters:

$$
W = 
\begin{bmatrix}
h_1 \\
h_2 \\
h_3 \\
\end{bmatrix}
=
\begin{bmatrix}
1 & 1 \\
1 & 1 \\
\end{bmatrix}
,
c = 
\begin{bmatrix}
0 \\
-1 \\
\end{bmatrix} , 

\vec{w} = 
\begin{bmatrix}
1 \\
-2 \\
\end{bmatrix}
$$,

and b = 0.

Let's visualize what's going on step-by-step.

## First Transformation for Representation Space

> What means Representation Space in the Deep Learning context? We can think the hidden layer as providing features describing $$\vec{x}$$, or as providing a new representation for $$\vec{x}$$.

When we apply the transformation $$W^{T}\vec{x}$$ to all four inputs, we have the following result

<figure>
  <img src="/images/posts_images/2020-09-03-xor_neural_net/first_transformation_for_representation_space.png" alt="First Transformation for Representational Space">
  <figcaption>First Transformation for Representational Space</figcaption>
</figure>>

Notice this representation space (or, at least, this step towards it) makes the points look different. The most important thing to notice is two points (those labelled with '1') colapsed into only one. 

## Second Transformation for Representation Space

Now let's add vector $$\vec{c}$$. What do we obtain? We then have this:
<figure>
  <img src="/images/posts_images/2020-09-03-xor_neural_net/second_transformation_for_representation_space.png" alt="First Transformation for Representational Space">
  <figcaption>First Transformation for Representational Space</figcaption>
</figure>>

Again, the position of points changed! But it's not ended.

## Final Representation Space

We now apply the nonlinearity **ReLU**. It will gives us "the real Representation Space".

<figure>
  <img src="/images/posts_images/2020-09-03-xor_neural_net/final_representation_space.png" alt="First Transformation for Representational Space">
  <figcaption>First Transformation for Representational Space</figcaption>
</figure>>

Notice now we can draw a line to separate the points!

## Last Linear Transformation in Representational Space

The last layer 'draws' the line over representation-space points.

<figure>
  <img src="/images/posts_images/2020-09-03-xor_neural_net/last_linear_transformation_in_representational_space.png" alt="First Transformation for Representational Space">
  <figcaption>First Transformation for Representational Space</figcaption>
</figure>>

This line means "here label equals 0". As we move downwards the line, the classification (a real number) increases. When we stops at the collapsed points, we have classification equalling 1.

# Visualizing Results (Iterative Training)

We saw how to get the correct classification using function composition. Although useful, Deep Learning practice is about backprop and gradient descent, right?

Let's see what happens when we use such learning algorithms.

> For our training, we initialize all parameters to values near 1.

<figure>
  <img src="/images/posts_images/2020-09-03-xor_neural_net/kernel_first_layer_plot.png" alt="First Transformation for Representational Space">
  <figcaption>First Layer Kernel Values Evolution</figcaption>
</figure>>

Notice how the first layer kernel values changes, but at the end go back to approximately one. I believe they do so because the gradient descent is going around a hill (a n-dimensional hill, actually), over the loss function.

<figure>
  <img src="/images/posts_images/2020-09-03-xor_neural_net/first_layer_bias_plot.png" alt="First Transformation for Representational Space">
  <figcaption>First Layer Bias Values</figcaption>
</figure>>


<figure>
  <img src="/images/posts_images/2020-09-03-xor_neural_net/second_layer_values_plot.png" alt="First Transformation for Representational Space">
  <figcaption>Second Layer Kernel and Bias Values Evolution</figcaption>
</figure>>

<figure>
  <img src="/images/posts_images/2020-09-03-xor_neural_net/Loss_handwritten.png" alt="First Transformation for Representational Space">
  <figcaption>Loss Evolution</figcaption>
</figure>>


<figure>
  <img src="{{ '/images/posts_images/2020-09-03-xor_neural_net/xor_net_evolution.gif' | relative_url }}" alt="Representational Space Evolution">
  <figcaption>Representational Space Evolution</figcaption>
</figure>
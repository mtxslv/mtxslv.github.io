---
title: "An Introduction do Neural Networks: Solving the XOR problem"
date: 2020-09-03
tags: [Deep Learning, TensorFlow, Python]
excerpt: "Deep Learning, TensorFlow, Python"
toc: true
author_profile: true
---

The Deep Learning "Hello World" code is often solving datasets like MNIST or even CIFAR10. But I would like to take a step back so I can discuss some Deep Learning concepts that I think are fundamental to a deep (pun not intended) understanding of what's going on under the hood. 

I propose to solve an even simpler dataset that stunned very important Machine Learning practioners (at least in the past). It is an example that can give us very important intuition (even visually) of what a neural net does. Let's try to solve 2-Variable XOR dataset.

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

## Only one Neuron (A Linear Model)

Our first attempt will consider a linear model. Such model implements the following equation:

$$ŷ = f(\vec{x};\vec{w},b) = \vec{x}^{T}\vec{w}+b$$.

Seems nice, isn't it? Will it work? Well... unfortunatelly, no. 

How can I now it beforehand? Let's take a closer look to the expression. It is implementing a linear relation. Imagine _f_ is a surface over the $$\vec{x}$$ plane, and its height equals the output. The surface must have height equalling 1 over the points $$[0 1]$$ and $$[1 0]$$ and 0 height (it would be touching the plane) at points $$[0 0]$$ and $$[1 1]$$. Could a hyperplane behave this way? No, it cannot.

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

## More than only one neuron , the return (let's use a non-linearity)

Ok, we know we cannot stack linear functions. It will lead us anywhere. What then? Let's use a nonlinear function called _activation function_ in the hidden layer. Let's define

$$\vec{h} = g(W^{T}\vec{x} + \vec{c})$$.

The hidden layer will use the **ReLU** (Rectified Linear Unit) function. How does it work? It applies the following relationship:

$$g(z) = max{0,z}$$.

# Visualizing Results (Function Composition)

The model we chose to use has a hidden layer followed by ReLU nonlinearity. It implements the global function:

$$f(\vect{};W,\vect{c},\vect{w},b) = \vect{w}^{T}max{0,W^{T}\vect{x}+\vect{c}}+b$$ .

A specified solution to the XOR problem has the following parameters:

$$W = \begin{matrix} 1 & 1\\
      1 & 1
      \end{matrix}

\vec{c} = [0 -1]^{T}
\vec{w} = [1 -2]^{T}
$$

and b = 0.

Let's visualize it step by step.

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

# Using Training

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
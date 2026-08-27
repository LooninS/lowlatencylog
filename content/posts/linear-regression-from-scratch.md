+++
title = "What's Linear Regression"
date = 2026-08-24
description = "Linear regression from pure mathematics: model, cost function, and gradient descent from scratch."
tags = ["ML-from-scratch", "linear-regression", "gradient-descent", "math"]
categories = ["Machine-Learning"]
math = true
+++

Machine Learning works by training a set of parameters and using them to predict future data. But what are these parameters and how  do they work? Why are there so many of these algorithms?

Each ML model is essentially a mathematical function whose parameters are optimized to best fit our data

In this blog, I demonstrate how a linear regression model works, why we use it and understand how it predict data and what's it's "learning".

## Linear Regression

$$
y=wx+b
$$
This is all we need to understand _linear regression_. The y and x are output/input data while (w, b) are the parameter. 

Linear regression assumes the relationship between $x$ and $y$ is approximately linear and that the errors are roughly centered around zero.

> [!NOTE]
> Linear Regression is just one of the many models we use to predict data.  It will not work if the data is following a logarithmic or any other pattern.
> That's why before writing our model we perform something EDA or Exploratory Data Analysis to try and understand the shape of data and it's patterns.

But what should our parameters be? How do we find the parameters for the best line?

One option is to find the difference in the predicted  values and actual values of the model and minimize it wrt (w, b). 

So, We can calculate the errors like so:

$$
E = \frac1m(\overbrace{\hat{y_i}}^{f(x_i)}-y_i)
$$
where, $y_i$: true value and $\hat{y_i}$: predicted value

But, there are two issues with it:
- Some of the errors will be negative. This will cause issue when we try and find the mean of the data.
- We should try to penalize outlier and extreme errors.

> [!NOTE]
> Squaring the errors penalizes large mistakes more heavily. In some problems (e.g., robust regression), we may prefer other loss functions that are less sensitive to outliers. The choice depends on how we want the model to treat extreme points

This is where the main part of ML starts. For any model, most likely, we define a cost function and minimize it with respect to our parameters. Now, how we minimize it depends on the type of algorithm used. For _linear regression_ we will use a gradiant descent function but this depends entirely on how the cost function is constructed.

## Cost Function
The cost function is simply the function we will be minimizing with respect to our parameters.

Since we are trying to minimize our errors, lets start there. 
So, we can calculate the error for one example like so:
$E_i=f(x_i)-y_i$
and because we need to take all errors across our dataset, we take the sum:
$$
	C_1(w,b) = \sum_{i=1}^{m}(f(x_i)-y_i)
$$
But this uses raw errors (not squared), so positive and negative errors can cancel out, giving a misleadingly small total.

Hence, use this cost function.
$$
C(w,b)= \frac{1}{2m}\sum_{i=1}^{m}(f(x_i)-y_i)^2
$$
Here the errors are squared, so all terms are non‑negative and large errors are penalized more.
We are using the $2m$ for simpler calculation.
## Minimizing the Cost Function
Gradient Descent is used to minimize the cost function. This is what it does:
- Start with some initial $(w=0,b=0)$
- Repeatedly update $w,b$ in small steps to reduce cost until we get minimum(lowest possible cost).

at each iteration:
$$
w=w-\overbrace{\alpha}^{\text{learning rate}}\underbrace{\frac{\partial{C(w,b)}}{\partial w}}_{\text{slope(C(w,b))}}
$$
and similarly for b.
- If $\alpha$ is too large → gradient descent may overshoot and diverge.
- If $\alpha$ is too small → convergence is very slow.

## Gradient function
We differentiate the cost function with respect to each parameter:

$$
\frac{\partial{C(w,b)}}{\partial{w}} = \frac{\partial}{\partial w} \left[\frac{1}{2m}\sum_{i=1}^m( (wx_i+b)-y_i )^2 \right]=\frac 1{m} \sum_{i=1}^{m}x_i \left((wx_i+b) -y_i\right)
$$
And
$$
\frac{\partial{C(w,b)}}{\partial{b}} = \frac{\partial}{\partial b} \left[\frac{1}{2m}\sum_{i=1}^m( (wx_i+b)-y_i )^2 \right]=\frac 1{m} \sum_{i=1}^{m}\left((wx_i+b) -y_i\right)
$$

## Gradient descent
Now, the final results are as follows:
$$
w = w-\alpha\frac 1{m} \sum_{i=1}^{m}x_i \left((wx_i+b) -y_i\right)
$$
AND
$$
b = b - \alpha \frac 1{m} \sum_{i=1}^{m}\left((wx_i+b) -y_i\right)
$$

## Conclusion
Most ML algorithms will have a similar flow.
- finding the parameters
- creating a cost function
- minimizing it with a suitable method
- simplifying the formula to make them easy to code
***
## Code?
That's all for now.  You can now code this in a jupyter notebook without much issues
> [!TIP]
> Here $x$ is a single number (one feature). For multiple features, $x$ becomes a vector $\mathbf{x}$, $w$ becomes a weight vector $\mathbf{w}$, and the model is 
> $f(\mathbf{x}) = \mathbf{w}^\top \mathbf{x} + b$.
> The same cost function and gradient descent idea apply, just with vectors and matrices.
> The code below is also for only one feature.

```python
import numpy as np

data = np.genfromtxt(
    "train.csv",
    delimiter=",",
    names=True,
    dtype=float
)

x_train = data["Input"]
y_train = data["Output"]

def cost_function(x,y,w,b):

    m = len(x)
    cost_sum = 0

    for i in range(m):
        f = w*x[i] + b
        cost = (f-y[i])**2
        cost_sum += cost
    total_cost = (1/(2*m)) * cost_sum
    
    return total_cost
    

def gradient_function(x, y, w, b):
    m = len(x)
    dc_dw = 0.0
    dc_db = 0.0
    for i in range(m):
        f = w * x[i] + b
        error = f - y[i]
        dc_dw += error * x[i]
        dc_db += error
    dc_dw /= m
    dc_db /= m
    return dc_dw, dc_db
    

def gradient_descent(x,y,alpha,iterations):
    w, b = 0.0, 0.0
    for i in range(iterations):
        dc_dw, dc_db = gradient_function(x,y,w,b)

        w = w - alpha*dc_dw
        b = b - alpha*dc_db
		if i % 1000 == 0:
	        print(f"ITERATION: {i}, Cost: {cost_function(x,y,w,b)}")
    return w,b
    
    
learning_rate = 0.01
iterations = 10000

final_w, final_b = gradient_descent(x_train,y_train,learning_rate,iterations)

print(f"\nw: {final_w:.4f}\nb: {final_b:.4f}")
```

If your data roughly follows a straight line, you should see the cost decreasing each iteration and finally settling at some small value. The printed $w$ and $b$ define the line $y=wx+b$ that best fits your data in the least‑squares sense.

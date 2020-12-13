---
layout: post
title:  "The Simplicity of Linear Regression"
date:   2019-05-29 14:00:00 +0200
categories: jekyll update
---
Anyone who has ever taken an introductory algebra course has been given basic understanding of linear regression, though the notion of "regression" itself may not have been explained. We'll do so right now.

Let's say we're on the Euclidean plane. This is a two-dimensional space, and mathematicians like to call it $$\mathbb{R}^2$$. Suppose that on this plane we've got two points $$p_1$$ and $$p_2$$. These two points can be described using planar coordinates. We'll say that $$x_1$$ and $$y_1$$ are the coordinates for point $$p_2$$ and that $$x_2$$ and $$y_2$$ are the coordinates for point $$p_2$$.

In this case, we can fit a line to these two points. The answer is:

\\[
y - y_1 = m(x-x_1),
\\]

where $$m = (y_2-y_1)/(x_2-x_1)$$. This is the *point-slope formula*, which is mercilessly force-fed to students in schools the world over.

The formula is significant because of three reasons: that it can be produced in the first place, that the formula is uniquely determined by the points, and that it is the simplest possible example of a linear regression. The latter reason is the subject of this post.


## When Linear Regression is "Perfect"

Students are compelled to see that the formula *works*, but oftentimes, they are not shown how to go about discovering from where it comes. This is 

Another way to write the equation for the line through $$p_1$$ and $$p_2$$ is:

\\[
f(x) = \dfrac{y_2-y_1}{x_2-x_1}(x-x_1)+y_1,
\\]

which is more obscure pedagogically but nicely illustrates the dependence of the function $$f(x)$$ on the coordinates of $$p_1$$ and $$p_2$$.
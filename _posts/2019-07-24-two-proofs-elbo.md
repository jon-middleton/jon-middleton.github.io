---
layout: post
title:  "Two Derivations of the Evidence Lower Bound"
date:   2019-07-24 14:00:00 +0200
categories: jekyll update
---
The evidence lower bound (abbreviated ELBO) of a probability distribution can be used to turn the approximation of a posterior distribution into an optimization procedure. In this note, we'll explore two methods for deriving the evidence lower bound, first with Jensen's inequality, and then with the Kullback-Leibler divergence. Then we'll show how the evidence lower bound appears as the optimization objective for a variational autoencoder.

## The Setting for ELBO
Let $$X$$ be a measurable space, which we'll call *observable space*. It's on $$X$$ that we observe our data. Let $$Z$$ be another measurable space, called *latent space*. On $$Z$$ we hope to find "useful representations" of data observed from $$X$$. A good example to keep in mind is when $$X$$ and $$Z$$ are finite-dimensional vector spaces, with $$Z$$'s dimension less than $$X$$'s dimension. In that particular setting, the latent space is regarded as a place in which the dimensionality of the data coming from $$X$$ has been reduced in some fashion. In this case we call $$Z$$ a space of codes, and we call the passage from $$X$$ to $$Z$$ *encoding*.

Now suppose that we have a joint probability density $$p(x,z)$$ on the product $$X\times Z$$. This amounts to a probabilistic assertion of the simultaneous occurence of observable data and their corresponding latent data. For an observable $$x$$, we can (in principle) write down its marginal probability density in observable space, which we call the *evidence*:
\\[
p(x) = \int_Z p(x,z)\, dz.
\\]

Due to Bayes' theorem, our joint density gives rise to the conditional density \\[
p(z|x) = \dfrac{p(x,z)}{p(x)} = \dfrac{p(x,z)}{\int_Z p(x,z)\, dz},
\\]
which yields probabilities for the occurence of latent data in the presence of observable data. Note that in order to write down $$p(z|x)$$, we need a complete understanding of the distributions of both the latent data as well as the observable data. Moreover, if we interpret $$Z$$ as a space of codes, then $$p(z|x)$$ represents our understanding of how the observable data are encoded and is thus called a *probabilistic encoder*.

A major impediment to determining this conditional density is the computation of the evidence. For most joint densities, the evidence is an *intractable integral*, i.e. an integral with an unknown [closed form solution][closed_form] or a known closed form solution whose precise computation is impractical. In such a picture, the best we can hope for is an approximation to $$p(x)$$. How this approximation is gotten is what follows.

## Jensen's Inequality
This inequality is nicely illustrated in an elementary case. Let $$s$$ and $$t$$ be real numbers, with $$s < t$$. Then the line segment whose endpoints are $$s$$ and $$t$$ can be parametrized via $$L(\lambda) = (1-\lambda)s+\lambda t$$, where $$\lambda\in [0,1]$$.	

Now, there are an unlimited number of ways to parametrize a line segment. However, there's something interesting about *this* parametrization: each $$L(\lambda)$$ corresponds to the expected value of a probability distribution $$ p_\lambda $$ on the two-element set $$\{s,t\}$$. In fact, if we set $$ p_\lambda(s) = 1-\lambda $$ and $$ p_\lambda(t) = \lambda $$, then $$\mathbb{E}_{p_\lambda}[x] = L(\lambda)$$. This gives a lovely connection between geometry and probability theory.

The expected values, of course, are just for the random variable $$x$$ whose values can be either $$s$$ or $$t$$. What about other random variables? Given $$x$$, we could apply to it some function $$F$$ and wind up with a new random variable $$F(x)$$. What can we say about the expected value of $$F(x)$$? What happens if we make certain assumptions about $$F$$?

The first question is trivial. Since $$F(x)$$ is constructed from $$x$$, and since $$x$$ is distributed according to $$p_\lambda$$, it follows that $$F(x)$$ is distributed according to $$p_\lambda$$. Thus the expected value of $$F(x)$$ is $$(1-\lambda)F(s)+\lambda F(t)$$. 

The second question is more interesting when we impose certain conditions on $$F$$. For instance, let's assume that $$F$$ is *convex*, which means \\[ F((1-\lambda)s+\lambda t ) \leq (1-\lambda)F(s)+\lambda F(t).\\]
Geometrically this says that for inputs on the line segment joining $$s$$ and $$t$$, the graph of $$F$$ is underneath the line segment joining $$(s, F(s))$$ and $$(t, F(t))$$. Probabilistically, this says
\\[
F(\\mathbb{E}\_{p_\\lambda}[x]) \leq \\mathbb{E}\_{p_\\lambda}[F(x)].
\\]
That is, the expected value of $$F(x)$$ can't be less than $$F$$ at the expected value of $$x$$. This means that if we know how $$x$$ is distributed, then can we use a convex function to form a new random variable and have some idea about that new random variable's expected value. Jensen's inequality is a vast generalization of this case:

**Theorem.** Let $$(X, \mathcal{M}, \mu)$$ be a probability space. Let $$(a,b)\subset \mathbb{R}$$ be an open interval, $$g: X\longrightarrow (a,b)$$ a function in $$L^1(\mu)$$, and $$F:(a,b)\longrightarrow \mathbb{R}$$ a convex function. Then
\\[
F\left\(\int_X g(x)\, d\mu(x) \right\) \leq \int_X F(g(x))\, d\mu(x).
\\]

In this general picture, $$g(x)$$ is our random variable, the expectation of which is $$ \mathbb{E}_\mu[g(x)]=\int_X g(x)\, d\mu(x).$$ Thus the inequality reads
\\[
F(\mathbb{E}\_\mu[g(x)])\leq \mathbb{E}\_\mu[F(g(x))].
\\]

The setting for the above example includes $$X = \{s,t\}$$, $$\mathcal{M}$$ the power set of $$X$$, and $$\mu = p_\lambda$$. On a finite measure space, integration against a measure is just summation against a probability mass function, so that 
\\[
\mathbb{E}\_\mu[g(x)]=\sum\_\{X\} g(x) p\_\lambda(x) = g(s) p\_\lambda(s) + g(t) p\_\lambda(t).
\\]
When $$(a,b)$$ contains $$X$$ and $$g$$ is the identity function on $$X$$, we recover the above example.

Useful applications of this result follow quickly. For instance, if $$F(x) = -\log(x)$$, then Jensen's inequality applies, since this function is convex. Multiplying both sides of the ensuing inequality by $$-1$$ gives the following:

**Corollary.** Let $$(X, \mathcal{M}, \mu)$$ be a probability space. Let $$(a,b)\subset \mathbb{R}$$ be an open interval and $$g: X\longrightarrow (a,b)$$ a function in $$L^1(\mu)$$. Then
\\[
\log(\mathbb{E}\_\mu[g(x)])\geq \mathbb{E}\_\mu[\log(g(x))].
\\]

## Derivation of ELBO with Jensen's Inequality
One method of approximating $$p(z|x)$$ is the following: take some computable probability density on latent space, say $$q(z)$$, and use it to construct a computable expression that's reasonably close to our desired conditional density. We note that
\\[
\int_Z p(x,z)\, dz = \int_Z \dfrac{p(x,z)}{q(z)}\, q(z)dz = \mathbb{E}\_\{z\sim q\}\left\[\dfrac{p(x,z)}{q(z)}\right\],
\\]
which casts the evidence as a function that takes observables to expected values of a random variable. Next, we can use Jensen's inequality to relate this to the expectation of another random variable:
\\[
\log\left(\mathbb{E}\_\{z\sim q\}\left\[\dfrac{p(x,z)}{q(z)}\right\]\right)\geq \mathbb{E}\_\{z\sim q\}\left\[\log\left(\dfrac{p(x,z)}{q(z)}\right)\right\].
\\]
The right-hand side is the *evidence lower bound* and is denoted $$\mathcal{L}(p,q;x)$$. It's easy to see that ELBO is a combination of an expectation term and the entropy of $$q(z)$$:
\\[
\mathbb{E}\_\{z\sim q\}\left\[\log\left(\dfrac{p(x,z)}{q(z)}\right)\right\] = \mathbb{E}\_\{z\sim q\}\left\[\log p(x,z)\right\] + H(q).
\\]
Putting together our remarks, we have the following estimate:
\\[
\log p(x) \geq \mathbb{E}\_\{z\sim q\}\left\[\log p(x,z)\right\] + H(q).
\\]
This is a quick way to establish a lower bound for the logarithmic evidence, but the approach gives no indication as to how far away the evidence lower bound is from $$\log p(x)$$. It would be nice to get an explicit decomposition of $$\log p(x)$$ in terms of ELBO *plus* a precise approximation error.

## Kullback-Leibler Divergence

Suppose that we have two probability distributions on a measure space $$X$$, and we want to quantify the extent to which they "diverge" from each other. One way to do it is to use the *Kullback-Leibler divergence*:
\\[
D_{KL}(p_1\,\vert\vert\,p_2) = \mathbb{E}\_\{p_1\}\left\[\log\left(\dfrac{p_1(x)}{p_2(x)}\right)\right\].
\\]
Obviously, if $$p_1$$ and $$p_2$$ are equal, then $$D_{KL}(p_1\,\vert\vert\,p_2)=0$$. For a non-trivial divergence, a good example is when $$X=\mathbb{R}$$ and each $$p_i$$ is a Gaussian:
\\[
p_i(x) = \dfrac{1}{\sqrt{2\pi\sigma_i^2}}\exp\left(\dfrac{|x-\mu_i|^2}{2\sigma_i^2}\right),
\\]
in which case the Kullback-Leibler divergence is an analytic function of the parameters of the $$p_i$$:
\\[
D_{KL}(p_1\,\vert\vert\,p_2) = -\log\left(\dfrac{\sigma_1}{\sigma_2}\right) + \dfrac{(\mu_1-\mu_2)^2 + \sigma_1^2 - \sigma_2^2}{2\sigma_2^2}.
\\]
As a measure of dissimilarity, KL divergence is a natural objective function for machine learning. It's tempting to regard KL divergence as a metric on distributions. However, a quick glance at the defining formula shows that one should not expect KL divergence to be a distance function, because KL divergence is not symmetric in its inputs. The KL divergence for two Gaussians makes this apparent.

## Derivation of ELBO with KL Divergence
Again, the idea is to select some computable density $$q(z)$$ on latent space, and then use it to produce computable integrands in lieu of the joint density. In this case, we'll use the fact that the log marginal probability is independent of the latent space:
\\[
\log p(x) = \int_Z \log p(x)\, q(z)dz.
\\]
Since $$p(x) = p(x,z)/p(z|x)$$, we have
\\[
\int_Z \log p(x)\, q(z)dz = \int_Z \log\left(\dfrac{p(x,z)}{p(z|x)}\right)\, q(z)dz.
\\]
We can relate the right-hand side to the KL divergence with a multiplicative identity:
\\[
\begin{align\*}
\int_Z \log\left(\dfrac{p(x,z)}{p(z|x)}\right)\, q(z)dz &= \int_Z \log\left(\dfrac{p(x,z)}{q(z)}\cdot\dfrac{q(z)}{p(z|x)}\right)\, q(z)dz \\\\ &= \int_Z \log\left(\dfrac{p(x,z)}{q(z)}\right)\, q(z)dz + \int_Z\log\left(\dfrac{q(z)}{p(z|x)}\right)\, q(z)dz
\end{align\*}
\\]
from which it follows that 
\\[
\log p(x) = \mathbb{E}\_\{z\sim q\}\left\[\log\left(\dfrac{p(x,z)}{q(z)}\right)\right\] + D_{KL}(q\, ||\, p(\cdot|x)).
\\]
This last equation shows that the difference of the logarithmic evidence and the KL divergence of $$p(z|x)$$ from $$q(z)$$ *is* the evidence lower bound. It also shows that the maximization of the evidence lower bound is equivalent to the minimization of the KL divergence. This method of deriving ELBO is superior because it gives an explicit decomposition of $$\log p(x)$$ involving an error term to be minimized.

We also note that we need not restrict ourselves to a fixed distribution $$q(z)$$. We can, in fact, parametrize a family of distributions, say $$q(z|x)$$, and then express the logarithmic evidence as
\\[
\log p(x) = \int_Z \log p(x)\, q(z|x)dz.
\\]
Applying the above techniques, we get
\\[
\log p(x) = \mathbb{E}\_\{z\sim q\}\left\[\log\left(\dfrac{p(x,z)}{q(z|x)}\right)\right\] + D_{KL}(q(\cdot | x)\, ||\, p(\cdot|x)).
\\]

## Application: Variational Autoencoders
A variational autoencoder (VAE) is a neural network that learns to produce approximations to its inputs via lower-dimensional feature recognition. It is the composition of two networks: an *encoder network* which maps high-dimensional observable data to low-dimensional latent data, and a *decoder network* which sends low-dimensional latent data to high-dimensional observable data. The idea is to establish a network structure such that an approximation to the identity function is learned, with the expectation that the latent representation of inputs is rich enough to allow generation of similar data, but sufficiently constrained to inhibit replication of inputs.

The parameters $$\hat \phi$$ of the encoder network correspond to the parameters $$\phi$$ of an encoder model $$q_\phi(z\vert x)$$. Similarly, the parameters of $$\hat \theta$$ of the decoder network correspond to the parameters $$\theta$$ of a decoder model $$p_\theta(x\vert z)$$. In this sense, a variational autoencoder learns the probability densities that encode the inputs and decode their corresponding latent representations.

The VAE is trained on data that are assumed to be independent, identically distributed, and generated randomly from data coming from a latent space. This amounts to assuming a *generative model* $$p_\theta(x,z)$$ that factors as the product of a prior $$p_\theta(z)$$ and a decoder $$p_\theta(x\vert z)$$. The posterior $$p_\theta(z\vert x)$$ thus has an interpretation as an encoder. Usually, the encoder, if not the evidence $$p_\theta(x)$$ from which it is constructed, is intractable. One then searches for an approximation $$q_\phi(z\vert x)$$ to the encoder.

To quantify the disparity between the approximate encoder $$q_\phi(z\vert x)$$ and the true encoder $$p_\theta(z\vert x)$$, we apply the evidence lower bound. Let's assume that we have a set $$\{x_j\}_{j=1}^N\subset \mathbb{R}^n$$ of independent, identically distributed observables. Under our assumptions, the logarithmic evidence is
\\[
\log p_\theta(x_1,\ldots, x_N) = \sum_{j=1}^N \log p_\theta(x_j).
\\]
For each data point $$x_j$$, we have an expression of its logarithmic evidence in terms of ELBO and the KL divergence:
\\[
\log p_\theta(x_j) = \mathbb{E}\_\{z\sim q_\phi(\cdot\vert x_j)\}\left\[\log\left(\dfrac{p_\theta(x_j,z)}{q_\phi(z\vert x_j)}\right)\right\] + D_{KL}(q_\phi(\cdot\vert x_j)\,\vert\vert\, p_\theta(\cdot|x_j)).
\\]
A natural optimization objective, then, would seem to be the evidence lower bound consisting of the generative model and the approximate encoder:
\\[
\mathcal{L}(\theta,\phi;x_j) = \mathbb{E}\_\{z\sim q_\phi(\cdot\vert x_j)\}\left\[\log\left(\dfrac{p_\theta(x_j,z)}{q_\phi(z\vert x_j)}\right)\right\].
\\]
We want a good generative model, along with a good approximate encoder for that generative model. Hence we want to look for a maximum by estimating the gradients of this evidence lower bound on the parameters $$\theta$$ and $$\phi$$.

Finally, we note that we can give our optimization objective a little more meaning by writing the generative model as a product of its prior and encoder:
\\[
\begin{align\*}
\mathcal{L}(\theta,\phi;x_j) &= \mathbb{E}\_\{z\sim q_\phi(\cdot\vert x_j)\}\left\[\log\left(\dfrac{p_\theta(z) p_\theta(x_j|z)}{q_\phi(z\vert x_j)}\right)\right\] \\\\ &= \mathbb{E}\_\{z\sim q_\phi(\cdot\vert x_j)\}\left\[\log\left(\dfrac{p_\theta(z)}{q_\phi(z\vert x_j)}\right)\right\] + \mathbb{E}\_\{z\sim q_\phi(\cdot\vert x_j)\}\left\[\log p_\theta(x_j|z)\right\] \\\\ &= - \mathbb{E}\_\{z\sim q_\phi(\cdot\vert x_j)\}\left\[\log\left(\dfrac{q_\phi(z\vert x_j)}{p_\theta(z)}\right)\right\] + \mathbb{E}\_\{z\sim q_\phi(\cdot\vert x_j)\}\left\[\log p_\theta(x_j|z)\right\] \\\\ &= - D_{KL}(q_\phi(\cdot\vert x_j)\,\vert\vert\, p_\theta(z)) + \mathbb{E}\_\{z\sim q_\phi(\cdot\vert x_j)\}\left\[\log p_\theta(x_j|z)\right\]
\end{align\*}
\\]


The first term measures the disparity between the approximate encoder and our generative model's prior belief about the distribution of codes. To minimize this KL divergence is to encourage the approximate encoder and the generative prior to sample latent data as similarly as possible.

The second term is the expected likelihood of the observable $$x_j$$ with respect to the approximate encoding procedure for $$x_j$$ given by $$q_\phi(z\vert x_j)$$. Accordingly, this term is called the *reconstruction loss*. To minimize it is to discourage the generative model from believing that the codes from which observables are reconstructed are close to the codes for $$x_j$$.

[closed_form]: https://en.wikipedia.org/wiki/Closed-form_expression
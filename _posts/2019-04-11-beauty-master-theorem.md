---
layout: post
title:  "The Beauty of the Master Theorem"
date:   2019-04-11 14:00:00 +0200
categories: jekyll update
---
Suppose that we a have an algorithm that recursively performs some operations on (*conquers*) some data and then partitions (*divides*) the data into smaller pieces, each of which is then conquered by the algorithm. Let $$a$$ be the number of pieces, which we'll call *subproblems*. We'll assume that the original data has size $$n$$ and that each partition produces $$a$$ subproblems each of size $$n/b$$, where $$b$$ is some positive integer.

For certain divide-and-conquer algorithms, we have a Master Theorem that explains their time complexity. In the case where $$n$$ is a power of $$b$$, the mathematics behind this theorem is beautiful. On an aesthetic basis alone it is worth a look. Since the statements of the theorem are rather opaque on a first glance, I'll try a presentation that makes these statements arise naturally.

## Some Asymptotic Preliminaries

We're dealing with time complexity, which means we need some comfort with how functions behave for large $$n$$. There are many ways to compare the asymptotic behavior of one function $$f(x)$$ with another function $$g(x)$$.

For simplicity, we'll assume that our functions are non-negative. We say that $$f(x) \in O(g(x))$$ if there exists some $$M > 0$$ and some $$x_0$$ such that for all $$x \geq x_0$$

\\[
f(x) \leq M g(x).
\\]

If $$g(x)$$ is non-zero, then there's a more succinct expression:
\\[
\limsup_{x\rightarrow\infty} \frac{f(x)}{g(x)} < \infty.
\\]

Intuitively, $$f(x) \in O(g(x))$$ means that $$f(x)$$ does not grow too much faster than $$g(x)$$. For example, if $$f(x)$$ and $$g(x)$$ are monic polynomials, then the degree of $$f(x)$$ is at most the degree of $$g(x)$$.

Next, we write $$f(x)\in \Omega(g(x))$$ if there exists some $$M > 0$$ and some $$x_0$$ such that for all $$x \geq x_0$$

\\[
f(x) \geq M g(x).
\\]

If $$g(x)$$ is non-zero, we can write this as

\\[
\liminf_{x\rightarrow\infty} \frac{f(x)}{g(x)} > 0.
\\]

Here, we're saying that the growth of $$g(x)$$ does not dominate the growth of $$f(x)$$. For instance, if $$f(x)$$ and $$g(x)$$ are monic polynomials, then the degree of $$f(x)$$ is at least the degree of $$g(x)$$.

Lastly, we write $$f(x)\in \Theta(g(x))$$ when $$f(x) \in O(g(x))$$ *and* $$f(x) \in \Omega(g(x))$$. This means that if $$x$$ is large enough, then $$f(x)$$ can be sandwiched between scalar multiples of $$g(x)$$. If $$f(x)$$ and $$g(x)$$ are polynomials, this means they have the same degree. In terms of limits, $$f(x)\in \Theta(g(x))$$ means that

\\[
0 < \liminf_{x\rightarrow\infty} \frac{f(x)}{g(x)} \quad \text{and} \quad \limsup_{x\rightarrow\infty} \frac{f(x)}{g(x)} < \infty.
\\]

In particular, an even stronger limit condition implies that $$f(x)\in \Theta(g(x))$$, namely

\\[
\lim_{x\rightarrow\infty} \frac{f(x)}{g(x)} = C,
\\]

where $$C$$ is some constant.


## Recurrences for the Master Theorem

We look at algorithms that enjoy the recurrence relation:

\\[
T(n) = a\, T(n/b) + f(n),
\\]

where $$a \geq 1$$, $$b > 1$$, and $$f(n)$$ is the time taken to aggregate the $$a$$ results gotten from conquering each subproblem of size $$n/b$$. Implicit here is an assumption that $$n$$ is divisible by $$b$$, so that $$n/b$$ is an integer. In what follows, we'll work under the assumption that $$n$$ is a power of $$b$$.

The equation describes what happens when we reduce the size of the problem once. A question then arises . . . what does it look like when we reduce the size of the problem twice? To answer this, we just replace each $$n$$ in the first equation with $$n/b$$ to get:

\\[
T(n/b) = a\, T(n/b^2) + f(n/b).
\\]

Then the first equation reads

\\[
\begin{split}
T(n) &= a\, (a\, T(n/b^2) + f(n/b)) + f(n) \\\\ &= a^2\,T(n/b^2) + a\,f(n/b) + f(n)
\end{split}
\\]

What about reducing the size of the problem three times? Replace $$ T(n/b^2)$$ with $$ a\, T(n/b^3) + f(n/b^2) $$ in the above equation, and we get:

\\[
T(n) = a^3\,T(n/b^3) + a^2\,f(n/b^2) + a\, f(n/b) + f(n).
\\]

For the moment, let's assume that, starting from the original recurrence relation, we can perform $$k-1$$ replacements in this fashion. Then we get a nice formula:

\\[
T(n) = a^k\,T(n/b^k) + a^{k-1}\,f(n/b^{k-1}) + \cdots  + a\, f(n/b) + f(n).
\\]

Now let's adjust the context a bit. If we focus our attention on $$a$$ in this last equation, then it's not much of a leap to think of the right-hand side as a polynomial $$p_k$$ in $$a$$:

\\[
p_k(a) = T(n/b^k)\, a^k + \sum_{i=0}^{k-1} f(n/b^{i})\, a^i.
\\]

$$T(n/b^k)$$ is the time to completion for a subproblem of size $$n/b^k$$, and there are $$a^k$$ such subproblems. Similarly, $$f(n/b^{i})$$ is the cost for a subproblem of size $$n/b^{i}$$, and there are $$a^i$$ of these. With this in mind, we should regard our polynomial as a highly detailed description of the amount of time for the algorithm to finish after it has been applied $$k$$ times.

## The Master Theorem for $$n = b^\ell$$

Now, if $$n$$ were equal to some power of $$b$$, (i.e. $$n = b^\ell$$ for some $$\ell$$), then we can write down the following polynomial:

\\[
p_\ell(a) = T(1)\, a^\ell + \sum_{i=0}^{\ell-1} f(n/b^{i})\, a^i.
\\]

In general, we can't do this. But if we concern ourselves only with those cases in which we *can*, then maybe we'll get some insight. If we can, then $$\ell = \log_b n$$, and we have:

\\[
p_{\log_b n}(a) = T(1)\, a^{\log_b n} + \sum_{i=0}^{(\log_b n)-1} f(n/b^{i})\, a^i.
\\]

Using $$ \log_b n = \ln n / \ln b$$ and $$a^x = e^{x \ln a}$$, we see that $$a^{\log_b n} = n^{\log_b a}$$. Thus we get another expression:

\\[
p_{\log_b n}(a) = T(1)\, n^{\log_b a} + \sum_{i=0}^{(\log_b n)-1} f(n/b^{i})\, a^i.
\\]

*This* suggests a function to use for determining the time complexity: $$g(n) = n^{\log_b a}$$. However, in order to make statements about time complexity for $$T(n)$$, we also need to know the time complexity of $$f(n)$$.

We don't know what $$f(n)$$'s time complexity should be, so we start guessing. We have a candidate in $$g(n)=n^{\log_b a}$$, so why not see what happens if we assume that $$f(n)\in O(n^{\log_b a})$$? If so, then for all $$i$$ *less* than some integer $$i_0$$, we have:

\\[
\begin{split} f(n/b^{i}) &\leq M (n/b^i)^{\log_b a} \\\\ &= M n^{\log_b a}/b^{i(\log_b a)} \\\\ &= M n^{\log_b a}/a^i,\end{split}
\\]

which implies that $$f(n/b^{i})\, a^i \leq M n^{\log_b a}$$ for these $$i$$'s.

Now we have to describe $$i_0$$. We're assuming that for some $$N$$, $$f(n) \leq M n^{\log_b a}$$ for $$n\geq N$$. When our input is $$n/b^i$$ instead of $$n$$, this means that $$f(n/b^i)\leq Mn^{\log_b a}/a^i$$ holds for $$n/b^i\geq N$$, and hence $$i \leq \log_b(n/N)$$. This means that $$i_0 = \lfloor\log_b(n/N)\rfloor$$.

Now we can split the summation in our polynomial:

\\[
p_{\log_b n}(a) = T(1)\, n^{\log_b a} + \sum_{i=0}^{\lfloor\log_b(n/N)\rfloor} f(n/b^{i})\, a^i + \sum_{i=\lfloor\log_b(n/N)\rfloor + 1}^{(\log_b n)-1} f(n/b^{i})\, a^i.
\\]

Next, since $$f(n/b^{i})\, a^i \leq M n^{\log_b a}$$ for all $$i$$'s in the first summation, we have the following:

\\[
\begin{split}
p_{\log_b n}(a) &\leq T(1)\, n^{\log_b a} + \sum_{i=0}^{\lfloor\log_b(n/N)\rfloor} M n^{\log_b a} + \sum_{i=\lfloor\log_b(n/N)\rfloor + 1}^{(\log_b n)-1} f(n/b^{i})\, a^i \\\\ &= T(1)\, n^{\log_b a} + M (\lfloor\log_b(n/N)\rfloor + 1) n^{\log_b a} + \sum_{i=\lfloor\log_b(n/N)\rfloor + 1}^{(\log_b n)-1} f(n/b^{i})\, a^i.
\end{split}
\\]

At this stage, we see three terms. The first term is a constant multiple of $$n^{\log_b a}$$. The third term appears to be a mess of dependencies on $$n$$ and thus merits some attention.

To simplify our analysis, we can assume without loss of generality that $$N = b^m$$, where $$m$$ is a sufficiently large integer. If we do this, then $$\lfloor\log_b(n/N)\rfloor = (\log_b n) - m$$. We can then expand the summation:

\\[
\begin{split}
\sum_{i=(\log_b n) - m + 1}^{(\log_b n)-1} f(n/b^{i})\, a^i &= f(b^{m-1})\,  a^{(\log_b n)-m+1} + f(b^{m-2})\,  a^{(\log_b n)-m+2} + \cdots + f(b)\,  a^{(\log_b n)-1} \\\\ &= (f(b^{m-1})\, a^{-m+1} + f(b^{m-2})\, a^{-m+2} + \cdots + f(b)\, a^{-1})\, a^{\log_b n} \\\\ &=  \left(\sum_{i=1}^{m-1} f(b^i)a^{-i}\right) n^{\log_b a}.
\end{split}
\\]


Thus the third term is *also* a multiple of $$n^{\log_b a}$$. Rewriting our inequality using $$(\log_b n) - m$$:
\\[
p_{\log_b n}(a) \leq T(1)\, n^{\log_b a} + M ((\log_b n) - m + 1) n^{\log_b a} + \left(\sum_{i=1}^{m-1} f(b^i)a^{-i}\right) n^{\log_b a},
\\]
we see that the second term is $$n^{\log_b a}$$ times a function that belongs to $$O(\log_b n)$$. This function sabotages our attempt at a bound.

Nonetheless, if we're content to modify $$g(n)$$ by incorporating this meddlesome function, then perhaps we can make a statement about the asymptotic behavior of $$p_{\log_b n}(a)$$. If instead we set $$g(n) = n^{\log_b a}\log_b n$$, then

\\[
\dfrac {p_{\log_b n}(a)}{n^{\log_b a}\log_b n} \leq \dfrac{T(1)}{\log_b n} + \dfrac{M((\log_b n) - m + 1)}{\log_b n} + \dfrac{1}{\log_b n}\sum_{i=1}^{m-1} f(b^i)a^{-i},
\\]
from which it follows that

\\[
\limsup_{n\rightarrow\infty} \dfrac {p_{\log_b n}(a)}{n^{\log_b a}\log_b n} \leq M.
\\]

Since $$T(n) = p_{\log_b n}(a)$$, we've shown that $$T(n)\in O(n^{\log_b a}\log_b n)$$. Next, we observe that \\[ T(1)\, n^{\log_b a} \leq p_{\log_b n}(a),\\] which implies that

\\[
\dfrac {T(1)}{\log_b n} \leq \dfrac {p_{\log_b n}(a)}{n^{\log_b a}\log_b n}.
\\]

Whenever $$n > b$$, we have
\\[
T(1) \leq \dfrac {p_{\log_b n}(a)}{n^{\log_b a}\log_b n},
\\]

which implies that
\\[
T(1)\leq \liminf_{n\rightarrow\infty} \dfrac {p_{\log_b n}(a)}{n^{\log_b a}\log_b n}.
\\]

Thus $$T(n)\in \Omega(n^{\log_b a}\log_b n)$$ as well. We've proved the following:

**Theorem.** If $$T(n) = a\, T(n/b) + f(n)$$ and $$f(n)\in O(n^{\log_b a})$$, then $$T(n)\in \Theta(n^{\log_b a}\log_b n)$$.

Can we do better? Maybe there's some function $$h(n)$$, not too different from $$g(n)$$, such that $$f\in O(h(n))$$ and will give us tighter asymptotics for $$T(n)$$. If so, it would have to be a function that forces

\\[
\begin{split}
\sum_{i=0}^{(\log_b n) - m} M h(n) = M ((\log_b n) - m + 1) h(n)
\end{split}
\\]

to belong to $$O(n^{\log_b a})$$. It turns out that by altering the exponent of $$n^{\log_b a}$$ ever so slightly, we can produce this behavior.

Let $$\epsilon > 0$$ be given. The context is that $$\epsilon$$ is some tiny real number, something like $$10^{-k}$$ for some large integer $$k$$. Now let $$h(n) = n^{(\log_b a)- \epsilon}$$. Then

\\[
\begin{split}
\sum_{i=0}^{(\log_b n) - m} M h(n) &= M ((\log_b n) - m + 1) n^{(\log_b a)-\epsilon}\\\\ &= M ((\log_b n) - m + 1) n^{\log_b a}n^{- \epsilon}\\\\ &= M n^{- \epsilon} ((\log_b n) - m + 1) n^{\log_b a}
\end{split}
\\]

By L'Hôpital's rule, $$\lim_{n\rightarrow \infty} n^{- \epsilon}((\log_b n) - m + 1) = 0$$. Thus the extra $$n^{- \epsilon}$$ factor controls the blowup of $$(\log_b n) - m + 1$$. Maybe we should assume instead that $$f(n)\in O(n^{(\log_b a)- \epsilon})$$.

Now we return to $$p_{\log_b n}(a)$$. If $$f(n)\in O(n^{(\log_b a)- \epsilon})$$, then for $$i \leq (\log_b n) - m$$

\\[
\begin{split} f(n/b^{i}) &\leq M (n/b^i)^{(\log_b a)-\epsilon} \\\\ &= M\, b^{i\epsilon}\, n^{(\log_b a)-\epsilon}/a^i
\end{split}
\\]

which implies that $$f(n/b^{i})\, a^i \leq M\, b^{i\epsilon}\, n^{(\log_b a)-\epsilon}$$ for these $$i$$'s. Hence:

\\[
\begin{split}
p_{\log_b n}(a) &= T(1)\, n^{\log_b a} + \sum_{i=0}^{(\log_b n) - m} f(n/b^{i})\, a^i + \left(\sum_{i=1}^{m-1} f(b^i)a^{-i}\right) n^{\log_b a} \\\\ &\leq T(1)\, n^{\log_b a} + \sum_{i=0}^{(\log_b n) - m} M\, b^{i\epsilon}\, n^{(\log_b a)-\epsilon} + \left(\sum_{i=1}^{m-1} f(b^i)a^{-i}\right) n^{\log_b a} \\\\ &\leq T(1)\, n^{\log_b a} + M n^{-\epsilon} \left(\sum_{i=0}^{(\log_b n) - m} b^{i \epsilon}\right) n^{\log_b a} + \left(\sum_{i=1}^{m-1} f(b^i)a^{-i}\right) n^{\log_b a} \\\\ &= T(1)\, n^{\log_b a} + M n^{-\epsilon}\, \left(\dfrac{b^{\epsilon((\log_b n) - m + 1)}-1}{b^\epsilon-1}\right)n^{\log_b a} + \left(\sum_{i=1}^{m-1} f(b^i)a^{-i}\right) n^{\log_b a} \\\\ &= T(1)\, n^{\log_b a} + M n^{-\epsilon}\, \left(\dfrac{n^\epsilon b^{-\epsilon(m-1)}-1}{b^\epsilon-1}\right) n^{\log_b a} + \left(\sum_{i=1}^{m-1} f(b^i)a^{-i}\right) n^{\log_b a} \\\\ &= T(1)\, n^{\log_b a} + M  \left(\dfrac{b^{-\epsilon(m-1)}-n^{-\epsilon}}{b^\epsilon-1}\right) n^{\log_b a} + \left(\sum_{i=1}^{m-1} f(b^i)a^{-i}\right) n^{\log_b a}.
\end{split}
\\]

which implies that

\\[
\dfrac {p_{\log_b n}(a)}{n^{\log_b a}} \leq T(1) + M \dfrac{b^{-\epsilon(m-1)}-n^{-\epsilon}}{b^\epsilon-1} + \sum_{i=1}^{m-1} f(b^i)a^{-i},
\\]
which *finally* gives us:
\\[
\limsup_{n\rightarrow \infty} \dfrac {p_{\log_b n}(a)}{n^{\log_b a}} \leq T(1) +  M \dfrac{b^{-\epsilon(m-1)}}{b^\epsilon-1} + \sum_{i=1}^{m-1} f(b^i)a^{-i}.
\\]

We've proved the following:

**Theorem.** If $$T(n) = a\, T(n/b) + f(n)$$ and $$f(n)\in O(n^{(\log_b a)- \epsilon})$$ for some $$\epsilon > 0$$, then $$T(n)\in \Theta(n^{\log_b a})$$.

Our exploration thus far has centered on $$f(n)\in O(n^{(\log_b a) - \epsilon})$$, where $$\epsilon \geq 0$$. What about the opposite situation? What happens when $$f(n)\in \Omega(n^{(\log_b a) + \epsilon})$$ for some $$\epsilon > 0$$? In this case, for $$i \leq (\log_b n) - m$$

\\[
\begin{split} f(n/b^{i}) &\geq M (n/b^i)^{(\log_b a)+\epsilon} \\\\ &= M\, n^{(\log_b a)+\epsilon}/a^i b^{i\epsilon}
\end{split}
\\]

which implies that $$f(n/b^{i})\, a^i \geq M\, n^{(\log_b a)+\epsilon}/b^{i\epsilon}$$ for these $$i$$'s. We get the following estimate:
\\[
\begin{split}
p_{\log_b n}(a) &= T(1)\, n^{\log_b a} + \sum_{i=0}^{(\log_b n) - m} f(n/b^{i})\, a^i + \left(\sum_{i=1}^{m-1} f(b^i)a^{-i}\right) n^{\log_b a} \\\\ &\geq T(1)\, n^{\log_b a} + M \left(\sum_{i=0}^{(\log_b n) - m} b^{-i\epsilon}\right) n^{(\log_b a)+\epsilon} + \left(\sum_{i=1}^{m-1} f(b^i)a^{-i}\right) n^{\log_b a} \\\\ &= T(1)\, n^{\log_b a} + M \left(\dfrac{b^{-\epsilon((\log_b n) - m + 1)}-1}{b^{-\epsilon}-1}\right) n^{(\log_b a)+\epsilon} + \left(\sum_{i=1}^{m-1} f(b^i)a^{-i}\right) n^{\log_b a} \\\\ &= T(1)\, n^{\log_b a} + M \left(\dfrac{n^{-\epsilon} b^{\epsilon(m-1)}-1}{b^{-\epsilon}-1}\right) n^{(\log_b a)+\epsilon} + \left(\sum_{i=1}^{m-1} f(b^i)a^{-i}\right) n^{\log_b a},
\end{split}
\\]
implying that
\\[
\dfrac{p_{\log_b n}(a)}{n^{(\log_b a)+\epsilon}} \geq T(1)\, n^{-\epsilon} + M \left(\dfrac{n^{-\epsilon} b^{\epsilon(m-1)}-1}{b^{-\epsilon}-1}\right) + \left(\sum_{i=1}^{m-1} f(b^i)a^{-i}\right) n^{-\epsilon}.
\\]
Hence,
\\[
\liminf_{n\rightarrow \infty}\dfrac{p_{\log_b n}(a)}{n^{(\log_b a)+\epsilon}} \geq \dfrac{M}{1-b^{-\epsilon}}.
\\]
We've shown that $$T(n)\in\Omega(f(n))$$. Can we show that $$T(n)\in O(f(n))$$? Doing so would require an estimate of
\\[
\dfrac{p_{\log_b n}(a)}{f(n)} = T(1)\, \dfrac{n^{\log_b a}}{f(n)} + \sum_{i=0}^{(\log_b n) - m} \dfrac{f(n/b^{i})}{f(n)}\, a^i + \left(\sum_{i=1}^{m-1} f(b^i)a^{-i}\right) \dfrac{n^{\log_b a}}{f(n)}.
\\]
When $$n$$ is sufficiently large, we have $$f(n)\geq M n^{(\log_b a)+\epsilon}$$, which is equivalent to $$n^{\log_b a}/f(n) \leq 1/(Mn^\epsilon)$$. This gives nice bounds on the first and third terms:
\\[
\dfrac{p_{\log_b n}(a)}{f(n)} \leq \dfrac{T(1)}{Mn^\epsilon} + \sum_{i=0}^{(\log_b n) - m} \dfrac{f(n/b^{i})}{f(n)}\, a^i + \left(\sum_{i=1}^{m-1} f(b^i)a^{-i}\right) \dfrac{1}{Mn^\epsilon}.
\\]
The issue is the second term. There's no reason to believe that it should converge as $$n$$ approaches infinity. This means that we need to *make* it converge. One way to do it would be to assert that the summation is dominated by the partial sum of a geometric series. We can set this up by assuming that there is some constant $$c$$ such that $$0<c<1$$ and
\\[
\dfrac{f(n/b^{i})}{f(n)}\, a^i \leq c^i.
\\]
In particular, when $$i=1$$, the inequality can be written as $$a f(n/b)\leq c f(n)$$, which amounts to an assertion that the cost of aggregating the solutions of all the subproblems gotten from a divide and conquer step is less than the cost of aggregating the original problem. This assumption is sometimes called the *regularity condition*. Under this assumption,
\\[
\limsup_{n\rightarrow \infty}\dfrac{p_{\log_b n}(a)}{f(n)} \leq \dfrac 1 {1-c},
\\]
hence $$T(n)\in O(f(n))$$ as well. We've proved the following:

**Theorem.** If $$T(n) = a\, T(n/b) + f(n)$$, $$f(n)\in \Omega(n^{(\log_b a)+\epsilon})$$, and $$a f(n/b)\leq c f(n)$$ for some constant $$c$$ between zero and one, then $$T(n)\in \Theta(f(n))$$.

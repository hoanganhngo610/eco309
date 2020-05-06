# Primer on optimization

---

## Introduction

---

### Introduction

Optimization is everywhere in economics:

- to model agent's behaviour: what would a rational agent do?
    - consumer maximizes utility from consumption
    - firm maximizes profit
- an economist tries to solve a model:
    - find prices that clear the market

---

### Two basic kinds of optimization problem:

- root finding: find  $x$ in $X$ such that $f(x)=0$

- minimization/maximization $\min_{x\in X} f(x)$

- often a minimization problem can be reformulated as a root-finding problem

    $$x_0 = {argmin}_{x\in X} f(x) \overbrace{\iff}^{??} f^{\prime} (x_0) = 0$$

---

### Plan

- general consideration about optimization problems
- convergence of recursive series
- one-dimensional root-finding
- one-dimensional optimization  (\*)
- local root-finding
- local optimization
- constrained optimization and complementarity conditions
---


## General considerations

---

### Optimization tasks come in many flavours

- continuous versus discrete optimization
- constrained and unconstrained optimization
- global and local
- stochastic and deterministic optimization
- convexity

---
### Continuous versus discrete optimization


Where is the choice picked from $x\in X$? It can be

- continuous: choose amount of debt $b_t \in [0,\overline{b}]$, of capital $k_t \in R^{+}$
- discrete: choose whether to repay or default $\delta\in{0,1}$, how many machines to buy ($\in N$), at which age to retire...
- a combination of both: mixed integer programming

---

### Continuous versus discrete optimization (2)

Discrete optimization requires a lot of combinatorial thinking. We don't cover it.

Sometimes a discrete choice can be approximated by a mixed strategy (i.e. a random strategy).
Instead of $\delta\in{0,1}$ we choose $prob(\delta=1)=\sigma(x)$ where we determine $x$ and $\sigma(x)=\frac{2}{1+\exp(-x)}$

---

### Constrained and Unconstrained optimization

Unconstrained optimization: $x\in R$

Constrained optimization: $x\in X$

- budget set: $p_1 c_1 + p_2 c_2 \leq I$
- positivity of consumption: $c \geq 0$.

In good cases, the optimization set is *convex*...

- pretty much always in this course

---

### Stochastic vs Determinstic

Common case, especially in machine learning

$$f(x) = E_{\epsilon}\left[ \xi(x;\epsilon)\right]$$


One wants to maximize (resp solve) w.r.t. $x$ but it is costly to compute expectation precisely using Monte-Carlo draws (there are other methods).

A *stochastic* optimization method allows to use noisy estimates of the expectation, and will still converge in expectation.
For now we focus on *deterministic* methods. Maybe later...

---

### Local vs global Algorithms

- In principle, there can be many roots (resp maxima) within the optimization set.

- Alorithm that converge to all of them are called "global". For instance:
    - grid search
    - simulated annealing

- We will deal only with local algorithm, and consider local convergence properties.
    - ->then it might work or not
    - to perform global optimization just restart from different points.

---

### Math & theory vs practice

- The full mathematical treatment will typically assume that $f$ is smooth ($\mathcal{C}_1$ or $\mathcal{C}_2$ depending on the algorithm).

- In practice we often don't know about these properties
  - we still try and check we have a local optimal

- So: fingers crossed

---

### Math & theory vs practice

Here is the surface representing the objective that a deep neural network training algorithm tries to minimize.

![non smooth minimization](graphs/nonsmooth.png)

And yet, neural networks do great things!

---

### What do you need to know?

- be able to handcode simple algos (Newton, Gradient Descent)
- understand the general principle of the various algorithms to compare them in terms of
    - robustness
    - efficiency
    - accuracy
- then you can just switch the various options, when you use a library...
    - we'll do that in the tutorial session

---

## Convergence of recursive series

---

### Recursive series

Consider a function $f: R\rightarrow R$ and a recursive series $(x_n)$ defined by $x_0$ and $x_n = f(x_{n-1})$.

We want to compute a fixed point of $f$ and study its properties.

---

### Recursive series

- Wait: does a fixed point exist?
    - we're not very concerned by the existence problem here

- We can assume there is an interval such that $f([a,b])\subset[a,b]$. Then we know there exists $x$ in $[a,b]$ such that $f(x)=x$. But there can be many such points (graph)

---

### Convergence

- How do we characterize behaviour around $x$ such that $f(x)=x$?
    - if $|f^{\prime}(x)|>1$: series is unstable and will not converge to $x$ except by chance
    - if $|f^{\prime}(x)|<1$: $x$ is a stable fixed point
    - if $|f^{\prime}(x)|=1$: ??? (look at higher order terms)

---

### Change the problem

- Sometimes, we are interested by tweaking the convergence speed:

$$x_{n+1} = (1-\lambda) x_n + \lambda f(x_n)$$

- $\lambda$ is the learning rate:
    - $\lambda>1$: acceleration
    - $\lambda<1$: dampening

- We can also replace the function by another one $g$ such that $g(x)=x\iff f(x)=x$, for instance:

$$g(x)=x-\frac{f(x)-x}{f^{\prime}(x)-1}$$

---

### Dynamics around a stable point

- We can write:

$$|x_t - x_{t-1}| =  | f(x_{t-1}) - f(x_{t-2})| $$

$$|x_t - x_{t-1}| \sim |f^{\prime}(x_{t-1})| |x_{t-1} - x_{t-2}| $$

$$\lambda_t =  \frac{ |x_{t} - x_{t-1}| } { |x_{t-1} - x_{t-2}|}$$

---

### Dynamics around a stable point (2)

How do we derive an error bound? Suppose that we have $\overline{\lambda}>|f^{\prime}(x_k)|$ for all $k\geq k_0$:

$$|x_t - x| \leq |x_t - x_{t+1}| + |x_{t+1} - x_{t+2}| + |x_{t+2} - x_{t+3}| + ... $$

$$|x_t - x| \leq |x_t - x_{t+1}| + |f(x_{t}) - f(x_{t+1})| + |f(x_{t+1}) - f(x_{t+2})| + ... $$



$$|x_t - x| \leq |x_t - x_{t+1}| + \overline{\lambda} |x_t - x_{t+1}| + \overline{\lambda}^2 |x_t - x_{t+1}| + ... $$


$$|x_t - x| \leq \frac{1} {1-\overline{\lambda}} | x_t - x_{t+1} |$$

---

###  How do we improve convergence ?

$$\frac{|x_{t-1} - x_{t-2}|} {|x_t - x_{t-1}|} \sim |f^{\prime}(x_{t-1})|  $$

corresponds to the case of __linear__ convergence (kind of slow).

---
### Aitken's extrapolation

note that

$$\frac{ x_{t+1}-x}{x_t-x} \sim \frac{ x_{t}-x}{x_{t-1}-x}$$

Take $x_{t-1}, x_t$ and $x_{t+1}$ as given and solve for $x$:

$$x = \frac{x_{t+1}x_{t-1} - x_{t}^2}{x_{t+1}-2x_{t} + x_{t-1}}$$

---
### Aitken's extrapolation (2)

or after some reordering

$$x = x_{t-1} - \frac{(x_t-x_{t-1})^2}{x_{t+1}-2 x_t + x_{t-1}}$$

---

### Steffensen's Method:

1. start with a guess $x_0$, compute $x_1=f(x_0)$ and $x_2=f(x_1)$
2. use Aitken's guess for $x^{\star}$.
  If required tolerance is met, stop.
3. otherwise, set $x_0 = x^{\star}$ and go back to step 1.

It can be shown that the sequence generated from Steffensen's method converges __quadratically__, that is

$\lim_{t\rightarrow\infty} \frac{x_{t+1}-x_t}{(x_t-x_{t-1})^2} \leq M$

---

### Convergence speed

Rate of convergence of series $x_t$ towards $x^{\star}$ is:

- linear:

$${\lim}_{t\rightarrow\infty} \frac{|x_{t+1}-x^{\star}|}{|x_{t}-x^{\star}|} = \mu \in R^+$$

- superlinear:

$${\lim}_{t\rightarrow\infty} \frac{|x_{t+1}-x^{\star}|}{|x_{t}-x^{\star}|} = 0$$

- quadratic:

$${\lim}_{t\rightarrow\infty} \frac{|x_{t+1}-x^{\star}|}{|x_{t}-x^{\star}|^2} = \mu \in R^+$$

----

### Convergence speed

Remark: in the case of linear convergence:

$${\lim}_{t\rightarrow\infty} \frac{|x_{t+1}-x_t|}{|x_{t}-x_{t-1}|} = \mu \in R^+ \iff {\lim}_{t\rightarrow\infty} \frac{|x_{t+1}-x^{\star}|}{|x_{t}-x^{\star}|}$$


---

## One-dimensional root-finding

---

### Bisection

- Find $x \in [a,b]$ such that $f(x) = 0$. Assume $f(a)f(b) <0$.
- Algorithm
    1. Start with $a_n, b_n$. Set $c_n=(a_n+b_n)/2$
    2. Compute $f(c_n)$
        - if $f(c_n)f(a_n)>0$ then set $(a_{n+1},b_{n+1})=(a_n,c_n)$
        - else set $(a_{n+1},b_{n+1})=(c_n,b_n)$
    3. If $f(c_n)<\epsilon$ and/or $\frac{b-a}/2^n<\delta$ stop. Otherwise go back to 1.

----

### Bisection (2)

- No need for initial guess: *globally convergent algorithm*
    - not a *global* algorithm
- $\delta$ is a guaranteed accuracy on $x$
- $\epsilon$ is a measure of how good the solution is
- think about your tradeoff: ($\delta$ or $\epsilon$ ?)

---

### Newton algorithm

Find $x$ such that $f(x) = 0$. Use $x_0$ as initial guess.

- $f$ must be $\mathcal{C_1}$ and we assume we can compute its derivative $f^{\prime}$

- General idea: observe that the zero $x^{\star}$ must satisfy $f(x^{\star})=0=f(x_0)+f^{\prime}(x_0)(x^{\star}-x_0) + o(x-x_0)$. Hence a good approximation should be $x^{\star}\approx x_0- f(x_0)/f^{\prime}(x_0)$

----

### Newton algorithm (2)

- Algorithm:
    - start with $x_n$
    - compute $x_{n+1} = x_n- f(x_n)/f^{\prime}(x_n)=f^{\text{newton}}(x_n)$
    - stop if $|x_{n+1}-x_n|<\eta$ or $|f(x_n)| < \epsilon$

- Convergence: __quadratic__

----

### Newton algorithm (3)

Let's prove that Newton convergence is quadratic.
The main ingredient is a Taylor expansion of $f$ at the second order. The trick is to formulate it around $x_n$ instead of the fixed point. Since the $o()$ would depend on the rank $n$, we use the Taylor-Lagrange version to control the remainder:

$$f(x^{\star}) = f(x_n) + f^{\prime}(x_n) (x^{\star} - x_n) +  f^{\prime\prime}(\xi_n) \frac{(x_n - x^{\star})^2}{2}$$

where $\xi_n \in [x^{\star}, x_n]$. Note that the left hand side is zero by definition of the root.

Now we divide by $f^{\prime}(x_n)$

$$0 = \frac{f(x_n)}{f^{\prime}(x_n)} + (x^{\star} - x_n) +  \frac{f^{\prime\prime}(\xi_n)}{{f^{\prime}(x_n)}} \frac{(x^{\star} - x_n)^2}{2}$$

we recognize the term $\frac{f(x_n)}{f^{\prime}(x_n)} - x_n$: it is equal to $-x_{n+1}$. Hence we have:

$$x_{n+1}- x^{\star} =  \frac{f^{\prime\prime}(\xi_n)}{{f^{\prime}(x_n)}} \frac{(x^{\star} - x_n)^2}{2}$$

In the limit, since $\xi_n\rightarrow x^{\star}$, it yields:

$$x_{n+1} - x^{\star} \sim \frac{ f^{\prime\prime} (x^{\star}) }{2 f^{\prime}(x^{\star})  } (x_n - x^{\star})^2$$

This corresponds to quadratic convergence.




---

### Quasi-Newton

- What if we can't compute $f^{\prime}$ or it is expensive to do so?
    - Idea: try to approximate $f^{\prime}(x_n)$ from the last iterates

- secant method: $f^{\prime}(x_n)\approx \frac{f(x_n)-f(x_{n-1})}{x_n-x_{n-1}}$
$x_{n+1} = x_n- f(x_n)\frac{x_n-x_{n-1}}{f(x_n)-f(x_{n-1})}$
    - requires two initial guesses: $x_1$ and $x_0$
    - superlinear convergence: $\lim \frac{x_t-x^{\star}}{x_{t-1}-x^{\star}}\rightarrow 0$

---

### Limits of Newton's method

- How could Newton method fail?
    - bad guess
        - -> start with a better guess
    - overshoot
        - -> dampen the update (problem: much slower)
        - -> backtrack
    - stationary point  
        - -> if root of multiplicity $m$ try  $x_{n+1} = x_n- m f(x_n)/f^{\prime}(x_n)$

----

(TODO: some graphs of failed Newton convergence)

---

### Backtracking

- Simple idea:
    - at stage $n$ given $f(x_n)$ compute Newton step $\Delta_n=-f(x_n)/f^{\prime}(x_n)$
    - find the smallest $k$ such that $|f(x_n-\Delta/2^k)|<|f(x_n)|$
    - set $x_{n+1}=x_n-\Delta/2^k$

---

## One dimensional minimization
---

### Golden section search

- Minimize $f(x)$ for  $x \in [a,b]$
- Choose $\Phi \in [0,0.5]$

- Algorithm:
    - start with $a_n < b_n$ (initially equal to $a$ and $b$
    - define $c_n = a_n+\Phi(b_n-a_n)$ and $d_n = a_n+(1-\Phi)(b_n-a_n)$
        - if $c_n<d_n$ set $a_{n+1},b_{n+1}=a_n, d_n$
        - else set $a_{n+1}, b_{n+1}= c_n, b_n$

----

### Golden section search (2)

- This is guaranteed to converge to a local minimum
- In each step, the size of the interval is reduced by a factor $\Phi$
- By choosing $\Phi=\frac{\sqrt{5}-1}{2}$ one can save one evaluation by iteration.

- Remark that bisection is not enough

---
### Gradient Descent

- Minimize $f(x)$ for  $x \in R$ given initial guess $x_0$
- Algorithm:
    - start with $x_n$
    - compute $x_{n+1} = x_n (1-\lambda)- \lambda f^{\prime}(x_n)$
    - stop if $|x_{n+1}-x_n|<\eta$ or $|f^{\prime}(x_n)| < \epsilon$

----

### Gradient Descent (2)

- Uses local information
    - one needs to compute the gradient
    - note that gradient at $x_n$ does not provide a better guess for the minimum than $x_n$ itself
    - learning speed is crucial
- Convergence speed: __linear__
    - rate depend on the learning speed
    - optimal learning speed? the fastest for which there is convergence

---

### Newton-Raphson method

- Minimize $f(x)$ for  $x \in R$ given initial guess $x_0$

- Build a local model of $f$ around $x_0$

$$f(x) = f(x_0) + f^{\prime}(x_0)(x-x_0) + f^{\prime\prime}(x_0)\frac{(x-x_0)^2}{2} + o(x-x_0)^2$$

- According to this model,

$$ f(x{\star}) = min_x f(x)\iff \frac{d}{d x} \left[ f(x_0) + f^{\prime}(x_0)(x-x_0) + f^{\prime\prime}(x_0)\frac{(x-x_0)^2}{2} \right] = 0$$

which yields: $x^{\star} = x_0 - \frac{f^{\prime}(x_0)}{f^{\prime\prime}(x_0)}$

----


### Newton-Raphson Algorithm (2)

- Algorithm:
    - start with $x_n$

    - compute $x_{n+1} = x_n-\frac{f^{\prime}(x_0)}{f^{\prime\prime}(x_0)}$
    - stop if $|x_{n+1}-x_n|<\eta$ or $|f^{\prime}(x_n)| < \epsilon$

- Convergence: *quadratic*

----



## Unconstrained Multidimensional Optimization

---

### Unconstrained problems


- Minimize $f(x)$ for  $x \in R^n$ given initial guess $x_0 \in R^n$

- Many intuitions from the 1d case, still apply
    - replace derivative by gradient, jacobian and hessian
    - recall that matrix multiplication is not commutative
- Some specific problems:
    - update speed can be specific to each dimension
    - saddle-point issues (for minimization)

----

### Quick terminology

Function $f: R^p \rightarrow R^q$

- *Jacobian*: $J(x)$ or $f^{\prime}\_x(x)$, $p\times q$ matrix such that:

$$J(x)_ {ij} = \frac{\partial f(x)_ i} {\partial x_j}$$

- *Gradient*: $\nabla J(x)$, gradient when $q=1$
- *Hessian*: denoted by $H(x)$ or $f^{\prime\prime}\_{xx}(x)$ when $q=1$:

$$H(x)_ {jk} = \frac{\partial f(x)}{\partial x_ j\partial x_k}$$

- In the following explanations, $|x|$ denotes the supremum norm, but most of the
following explanations also work with other norms.
---

## Unconstrained Multidimensional Root-Finding

---

### Multidimensional Newton-Raphson

- Algorithm:
    - start with $x_n$
    - compute $x_{n+1} = x_n- J(x_{n})^{-1}f(x_n)=f^{\text{newton}}(x_n)$
    - stop if $|x_{n+1}-x_n|<\eta$ or $|f(x_n)| < \epsilon$

- Convergence: __quadratic__

----

### Multidimensional Newton Minimization (2)

- what matters is the computation of the step $\Delta_n = {\color{\red}{J(x_{n})^{-1}}} f(x_n)$
- don't compute $J(x_n)^{-1}$
    - it takes less operations to compute $X$ in $AX=Y$ than $A^{-1}$ then $A^{-1}Y$
- strategies to improve convergence:
    - *dampening*: $x_n = (1-\lambda)x^{n-1} - \lambda \Delta_n$
    - *backtracking*: choose $k$ such that $|f(x_n-2^{-k}\Delta_n)|$<$|f(x_{n-1})|$
    - *linesearch*: choose $\lambda\in[0,1]$ so that $|f(x_n-\lambda\Delta_n)|$ is minimal

----



## Unconstrained Multidimensional Minimization

---

### Multidimensional Gradient Descent

- Minimize $f(x) \in R$ for  $x \in R^n$ given  $x_0 \in R^n$
- Algorithm
    - start with $x_n$
    - $$x_{n+1} = (1-\lambda) x_n - \lambda \nabla f(x_n)$$
    - stop if $|x_{n+1}-x_n|<\eta$ or $|f(x_n)| < \epsilon$
- Comments:
    - lots of variants
    - automatic differentiation software makes gradient easy to compute
    - convergence is typically __linear__
----

![Contours](contours_evaluation_optimizers.gif)

---

### Multidimensional Newton Minimization (1)


- Algorithm:
    - start with $x_n$
    - compute $x_{n+1} = x_n-{\color{\red}{H(x_{n})^{-1}}}\color{\green}{ J(x_n)'}$
    - stop if $|x_{n+1}-x_n|<\eta$ or $|f(x_n)| < \epsilon$
- Convergence: __quadratic__
- Problem:
    - $H(x_{n})$ hard to compute efficiently
    - rather unstable

----

### Multidimensional Newton Minimization (2)

Intuition for the size of the Newton step : ${\color{\red}{H(x_{n})^{-1}}}\color{\green}{ J(x_n)'}$
Suppose $x_n$ is known and that we have derivative information $J(x_n) \in R$ and $H(x_n) \in R^p$ where p (such that $x_n \in R^p$). We can write a second order taylor expansion of $f$ around $x_n$ as follows:

$$f(x^{\star}) = f(x_n) + \underbrace{f^{\prime}(x_n)}_{\nabla_n = J_n \in R^p} . (x^{\star} - x_n) + \frac{1}{2}(x^{\star} - x_n)' \underbrace{f^{\prime\prime}(x_n)}_{H_n \in R^p \times R^p} (x^{\star} - x_n) + o(x^{\star} - x_n)$$

The expression on the right is a quadratic form (the hessian $H_n$ is symmetric). If we consider it is a good approximation of $f$ we can minimize it w.r.t. $x^{\star}$ to find a suitable guess.

The derivative operator of the right hand side (without the $o()$) is the linear form:

$$D_n = J_n + H_n (x^{\star} - x_n)$$

??? note
    The Frechet Derivative of a function $f: R^p\rightarrow R^q$ at $x$ is a linear operator $L$ such that $\lim_{|x|\rightarrow 0} \frac{|f(x+dx) - f(x) - L.x| } {|dx|} = 0$.

    Since $J_n (x+dx) = J_n x + J_n dx$ then the differential of $x \rightarrow J_n x$ is trivially $J_n$.

    For the hessian we have $q(x+dx) = (x+dx-x_n)'H_n(x+dx-x_n) = (x-x_n)' H_n (x-x_n) + (x-x_n)' H_n dx + dx' H_n (x-x_n)' + dx' H_n dx$.
    Since $|dx'H_n dx|\leq M ||H_n|| | dx| ^2$ for some constant $M$ depending on the exact norms we choose, we have thus proven that the derivative of $q$ is the linear operator $L: dx \rightarrow  (x-x_n)' H_n dx + dx' H_n (x-x_n)'$ (it is trivial to check it is linear). By chance since $dx' H_n (x-x_n)'$ belongs to $R$ it is equal to its transpose which can be rewritten as  $dx' (x-x_n)$. Hence we can say that the differenatial operator is $L.dx = 2 dx' H_n (x-x_n)$ which is a simple left matrix multiplication.


When $f$ is minimized any deviation $dx \in R^p$ must satisfy $D_n.dx=0$. This is true if $D_n=0$ that is:

$$x^{\star} = x_n - {H_n}^{-1} J_n$$

----

### Quasi-Newton method for multidimensional minimization

- Recall the secant method: $f(x_{n-1})$ and $f(x_{n-2})$ are used to approximate $f^{\prime}(x_{n-2})$.
    - Intuitively, $n$ iterates would be needed to approximate a hessian of size $n$....
- Broyden method: takes $2 n$ steps to solve a linear problem of size $n$
  - uses past information incrementally

----

### Quasi-Newton method for multidimensional minimization

Consider the approximation:

$$f(x_n)-f(x_{n-1}) \approx J(x_n) (x_n - x_{n-1})$$

- $J(x_n)$ is unknown and cannot be determined directly as in the secant method.
- idea: $J(x_n)$ as close as possible to $J(x_{n-1})$ while solving the secant equation
- formula:

$$J_n = J_{n-1} + \frac{(f(x_n)-f(x_{n-1})) - J_{n-1}(x_n-x_{n-1})}{||x_n-x_{n-1}||^2}(x_n-x_{n-1})^{\prime}$$


----


### Gauss-Newton Minimization

- Restrict to least-square minimization: $min_x \sum_i f(x)\_i^2 \in R $

- Then up to first order, $H(x_n)\approx J(x_n)^{\prime}J(x_n)$
- Use the step: ${J(x_n)^{\prime}J(x_n)}^{-1}\color{\green}{ J(x_n)}$

- Convergence:
    - can be __quadratic__ at best
    - linear in general

----

### Levenberg-Marquardt

- Least-square minimization: $min_x \sum_i f(x)\_i^2 \in R $

- replace ${J(x_n)^{\prime}J(x_n)}^{-1}$ by ${J(x_n)^{\prime}J(x_n)}^{-1} + μ I$
    - adjust $\lambda$ depending on progress
- uses only gradient information like Gauss-Newton
- equivalent to Gauss-Newton close to the solution
- equivalent to Gradient far from solution


---


## Constrained optimization and complementarity conditions

---

### Consumption optimization

Consider the optimization problem:

$$\max U(x_1, x_2)$$

under the constraint $p_1 x_1 + p_2 x_2 \leq B$

where $U(.)$, $p_1$, $p_2$ and $B$ are given.

How do you find a solution by hand?

----

### Consumption optimization (1)

- Compute by hand
- Easy: since the budget constraint must be binding, get rid of it by stating $x_2 = B - p_1 x_1$. Then maximize in $x_1$, $U(x_1, B - p_1 x_1)$ using the first order conditions.

- It works but:
    - breaks symmetry between the two goods
    - what if there are other constraints: $x_1\geq \underline{x}$?
    - what if constraints are not binding?
    - is there a better way to solve this problem?

----

### Consumption optimization (2)

- Another method, which keeps the symmetry. Constraint is binding, trying to minimize along the budget line yields an implicit relation between $d x_1$ and $d x_2$

$$p_1 d {x_1} + p_2 d {x_2} = 0$$

- At the optimal:
$U^{\prime}\_{x_1}(x_1, x_2)d {x_1} + U^{\prime}\_{x_2}(x_1, x_2)d {x_2} = 0$
- Eliminate $d {x_1}$ and $d {x_2}$ to get *one* condition which characterizes optimal choices for all possible budgets.
Combine with the budget constraint to get a *second* condition.

----

### Penalty function

- Take a penalty function $p(x)$ such that $p(x)=K>0$ if $x>0$ and $p(x)=0$ if $x \leq 0$. Maximize: $V(x_1, x_2) = U(x_1, x_2) - p( p_1 x_1 + p_2 x_2 - B)$
- Clearly, $\min U \iff \min V$
- Problem: $\nabla V$ is always equal to $\nabla U$.
    - Solution: use a smooth solution function like $p(x) = x^2$
- Problem: distorts optimizationt
    - Solution: adjust weight of barrier and minimize $U(x_1, x_2) - \kappa p(x)$
- Possible but hard to choose the weights/constraints.

---

### Penalty function

Another idea: is there a canonical way to choose $\lambda$ such that at the minimum it is equivalent to minimize the original problem under constraint or to minimize

$$V(x_1, x_2) = U(x_1, x_2) - \lambda (p_1 x_1 + p_2 x_2 - B)$$

Clearly, when the constraint is not binding we must have $\lambda=0$. What should be the value of $\lambda$ when the constraint is binding ?

---

### Karush-Kuhn-Tucker conditions

- If $(x^{\star},y^{\star})$ is optimal there exists $\lambda$ such that:
    - $(x^{\star},y^{\star})$ maximizes $U(x_1, x_2) - \lambda (p_1 x_1 + p_2 x_2 - B)$
    - $\lambda \geq 0$
    - $\lambda  (p_1 x_1 + p_2 x_2 - B) = 0$
- The two latest conditions are called "complementarity" or "slackness" conditions
    - they are equivalent to $\min(\lambda, p_1 x_1 + p_2 x_2 - B)=0$
    - we denote $\lambda \geq 0 \perp p_1 x_1 + p_2 x_2 - B \geq 0$
- $\lambda$ can be interpreted as the welfare gain of relaxing the constraint.

----

### Karush-Kuhn-Tucker conditions

- We can get first order conditions that factor in the constraints:
    - $U^{\prime}_x - \lambda p_1 = 0$
    - $U^{\prime}_y - \lambda p_2 = 0$
    - $\lambda \perp p_1 x_1 + p_2 x_2 - B$
- It is now a nonlinear system of equations with complementarities (NCP)
      - there are specific solution methods to deal with it

---

### Solution strategies for NCP problems

- General formulation for vector-valued functions $f(x)\geq 0 \perp g(x)\geq 0$ means $\forall i, f_i(x)\geq 0 \perp g_i(x)\geq 0$  
  - NCP do not necessarily arise from a single optimization problem

- There are robust (commercial) solvers for NCP problems (PATH, Knitro) fo that

- How do we solve it numerically?
    - assume constraint is binding then non-binding then check which one is good
        - OK if not too many constraints
    - reformulate it as a smooth problem
    - approximate the system by a series of linear complementarities problems (LCP)

----

### Smooth method

- Consider the *Fisher-Burmeister* function $\phi(a,b) = a+b-\sqrt(a^2+b^2)$. It is infinitely differentiable, except at $(0,0)$
- Show that $\phi(a,b) = 0 \iff \min(a,b)=0$
- After substitution in the original system one can a regular non-linear solver
  - fun fact: the formulation with a $\min$ is nonsmooth but also works quite often

----

### Pervasiveness of complementarity problems

- We've seen NCP problems arise naturally from constrained optimization
- Some NCP problems don't come from a single optimization

---

### Trade in an endowment economy

- Agent 1 (resp 2) is endowed with quantity $y_A$ of good $A$ and quantity $y_B$ of good $B$
- both agents have the same preferences $U(x_B, y_B)$ over the two goods
- they can trade good $A$ at price 1 and $B$ at price $p$
- write the equations characterizing the equilibrium prices and quantities such that the market clear and budget constraints are satisfied

---

## Practicalities

---

### Optimisation libraries

- Robust optimization code is contained in the following libraries:
    - Roots.jl: one-dimensional root finding
    - NLSolve.jl: multidimensional root finding (+complementarities)
    - Optim.jl: minimization
- The two latter libraries have a somewhat peculiar API, but it's worth absorbing it.

----

### How do you compute derivatives?

Several methods to to compute derivatives:

- by hand
    - ++ full control
    - -- error prone
- finite differences (`FiniteDifferences.jl`, `FiniteDiff.jl`)
    - ++ works for any function
    - -- possibly inacurate
- symbolic differentiation (`Calculus.jl`,  `SymEngine.jl`)
    - ++ only for mathematical expression
    - -- can produce instable formulas
- automatic differentiation (`JuliaDiff.jl`, `Zygote.jl`)
    - ++ differentiate any code (even loops or conditional)
    - -- can generate inefficient code

---

### Finite differences

- Basic: take $\epsilon>0$ compute $\frac{f(x+\epsilon)-f(x)}{\epsilon}$

- For $\epsilon$ small enough it produces an approximation of $f^{\prime}$
- Typically choose $\epsilon=sqrt(eps)$ where epsilon is machine precision
    - in Julia: `sqrt(eps()) -> 1.4901161193847656e-8`

- Precision: second order accuracy in $\epsilon$

- There are much better methods. (stay tuned)

---
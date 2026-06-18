---
title: "Humble Primer on Approximation Algorithms"
summary: "Some problems are so hard that finding the perfect answer would take forever. Approximation algorithms ask a humbler question: how close can we get, quickly, and how do we know?"
math: true
---

Even hearing "approximation algorithms" has frightened me for a while, especially because I sat through many talks in this area where instead of already knowing 3-4 methods to solve max cut and min vertex cover, and seeing how the talk builds on them, I was recalling the problem formulation of max cut and min vertex cover at snail's pace like "oh cut? Oh cut's when the edge sort of joins 2 clusters of vertices? Oh?" Yeah I know, I know. In this post, we're going to decode portions of Chapter 1 and Chapter 6 of "The Design of Approximation Algorithms" by Williamson and Shmoys.

Suppose you're carrying a bag, and you want to collect valuable precious stones from the river bank. Ideally, you want to pack all the valuable stones into your bag, but the weight that your bag can carry is limited. All you can do is pack the *most valuable* stones in your bag.

Suppose you're an Uber driver, and you have a series of rides lined up at various locations. You can't take all of them, because your fuel is limited. So you want to choose which rides to take, and in what order to drive them, so that you earn as much as possible without running out of fuel.

All of these are discrete optimization problems. Discrete optimization is the process of finding the best possible solution from a set of distinct options. Note the word *distinct*. Either you choose the option or you don't. No half an option, quarter an option and so on.

Most discrete optimization problems are "NP-hard", meaning that there are no efficient algorithms to find optimal aka best solutions to such problems. This means that even if I wait for my entire lifetime, I may not be able to find the set of most valuable stones I can pack into my bag of limited weight. Or find the set of rides that corresponds to my maximum earnings.

Fine, instead of finding the set of *most* valuable stones, can I find the set of *reasonably* valuable stones? Can I find the set of rides that corresponds to *reasonably high* earnings? In other words, is there an efficient (aka polynomial time) algorithm to find an approximation to the optimal solution instead? And can I quantify this approximation with a quantity called $$\alpha$$? If I'm trying to maximize something, the algorithm would find a solution that's *at least* $$\alpha$$ times the optimal solution. If I'm trying to minimize something, the algorithm would find a solution that's *at most* $$\alpha$$ times the optimal solution.

With this intuition, let's define an approximation algorithm.

> **Definition 1.1:** *An $$\alpha$$-approximation algorithm for an optimization problem is a polynomial-time algorithm that for all instances of the problem produces a solution whose value is within a factor of $$\alpha$$ of the value of an optimal solution.*

We need examples. Let's examine a famous example, the Minimum Vertex Cover problem aka one of the problems that made me zone out of TCS lectures. The formulation of the Minimum Vertex Cover problem is as follows:

> Given an undirected graph $$G = (V, E)$$, a **vertex cover** is a set $$C \subseteq V$$ such that every edge has at least one endpoint in $$C$$ i.e. $$\forall (i, j) \in E, \quad i \in C \text{ or } j \in C.$$
> Output the vertex cover of smallest size.

We are now going for a different formulation of the same problem, what we call the integer programming formulation.

For every graph vertex $$i \in V$$, define

$$
x_i =
\begin{cases}
1, & \text{if } i \in C, \text{ i.e. i is in the cover} \\
0, & \text{if } i \notin C, \text{ i.e. i is not in the cover}.
\end{cases}
$$

Then finding the vertex cover of smallest size is equivalent to minimizing $$\sum_{i \in V} x_i$$. Take a moment to see that.

We could just set $$x_i$$ for all vertices $$i$$ to be 0 then to minimize $$\sum_{i \in V} x_i$$. But no, we can't do that. We have a constraint: at least one endpoint of each edge is chosen. Mathematically, this translates to $$x_i + x_j \geq 1, \forall (i, j) \in E$$.

We have all the ingredients for our integer programming formulation now which is as follows. Remember we are solving for $$x = (x_1, x_2, \dots, x_{\lvert V\rvert})$$, and the optimal solution is $$(x_1, x_2, \dots, x_{\lvert V\rvert}) = (x_1^\star, x_2^\star, \dots, x_{\lvert V\rvert}^\star)$$

$$
\begin{aligned}
\text{minimize} \quad & \sum_{i \in V} x_i \\
\text{subject to} \quad & x_i + x_j \geq 1, & \forall (i, j) \in E, \\
& x_i \in \{0, 1\}, & \forall i \in V.
\end{aligned}
$$

Here's the catch: that last constraint, $$x_i \in \{0, 1\}$$, is exactly what makes this hard. Forcing each variable to be a clean "in or out" decision is what turns this into an *integer* program, and integer programs are NP-hard in general. So we do something that feels almost like cheating: we *relax* it. Instead of demanding $$x_i \in \{0, 1\}$$, we allow $$x_i$$ to be any real number in the interval $$[0, 1]$$:

$$
\begin{aligned}
\text{minimize} \quad & \sum_{i \in V} x_i \\
\text{subject to} \quad & x_i + x_j \geq 1, & \forall (i, j) \in E, \\
& 0 \leq x_i \leq 1, & \forall i \in V.
\end{aligned}
$$

This is the **linear programming (LP) relaxation**, and unlike the integer version, it *can* be solved efficiently. Of course, this linear programming relaxation might hand us fractional answers like $$x_i = \tfrac{1}{2}$$, which makes no sense for "is vertex $$i$$ in the cover or not?" But notice that every valid integer solution is also a valid solution to the relaxed problem, so the LP's optimal solution can only be *smaller* than (or equal to) the true integer optimal solution.

Circling back to what do we do with a fractional answer? Well, we can round them to an integer answer! So each component of the optimal solution $$x_i^\star$$ to the LP relaxation is rounded to 1 if $$x_i^\star \geq 1/2$$, 0 otherwise. That means the vertex cover $$C = \{i \in V: x_i^\star \geq 1/2 \}$$.

Is $$C = \{i \in V: x_i^\star \geq 1/2 \}$$ actually a vertex cover? How can we prove this? Remember for $$C$$ to be a vertex cover, an endpoint of every edge of the graph G should be covered. Consider an edge $$(i, j) \in E$$. A constraint of the LP relaxation problem that we were solving was $$x_i^\star + x_j^\star \geq 1$$. Therefore, at least one of $$x_i^\star$$ or $$x_j^\star$$ is at least 1/2, and that endpoint is therefore included in C. So C is a valid vertex cover.

It turns out that this rounding algorithm that we just saw is a 2-approximation algorithm. What this means is that if the minimum vertex cover is of size $$s$$, the solution that we get is of size at most $$2s$$. How is this the case? Let's prove this.

For every selected vertex $$i \in C$$, we have $$x_i^\star \geq 1/2$$ (because that's when it gets rounded up to 1, and 1 means vertex $$i$$ is included in vertex cover), so

$$
1 \leq 2x_i^\star.
$$

Therefore

$$
|C| = \sum_{i \in C} 1 \leq \sum_{i \in C} 2x_i^\star \leq 2 \sum_{i \in V} x_i^\star = 2\text{times the solution to the LP relaxation}
$$

Since the solution to the LP relaxation is smaller than or equal to the solution to the integer programming formulation (which is the optimal solution), we have that the minimum size of the vertex cover is smaller than or equal to twice the optimal solution.

Let's give this some notation, so that it's easier to understand the textbook language if you were to refer a textbook after reading this post.

We refer to solution to the LP relaxation as $$\text{LP}^\star$$.  The optimal solution, in other words, the solution to the integer programming solution is $$OPT$$. And the size of the minimum vertex cover is $$\lvert C\rvert$$. So

$$
|C| \leq 2\,\text{LP}^\star \leq 2\,\text{OPT}.
$$

Thus the LP-threshold algorithm is a 2-approximation. Can we do better than 2?

The factor of 2 came from rounding fractional values up, and our whole bound leaned on $$\text{LP}^\star \leq \text{OPT}$$. Could a cleverer argument squeeze out a factor of, say, 1.5 as 
$$
|C| \leq 1.5\,\text{LP}^\star \leq 1.5\,\text{OPT}.
$$

It turns out the answer is *no, not with this LP*, the $$\text{LP}^\star$$ of this relaxed LP can actually be $$\tfrac{1}{2}\,\text{OPT}$$.

Consider the **complete graph** $$K_n$$, where every pair of vertices is joined by an edge. We compute two things.

First, the LP value. Set $$x_i = 1/2$$ for every vertex. Every edge constraint becomes $$x_i + x_j = \tfrac12 + \tfrac12 = 1 \geq 1$$, so this fractional solution is feasible, and

$$
\text{LP}^\star \leq \sum_{i \in V} x_i = \frac{n}{2}.
$$

Second, the true (integer) optimum. In $$K_n$$, a vertex cover must contain at least $$n - 1$$ vertices (else one edge would always be missed out). Thus

$$
\text{OPT} = n - 1.
$$

Putting these together,

$$
\frac{\text{OPT}}{\text{LP}^\star} \geq \frac{n - 1}{n/2} = 2 - \frac{2}{n},
$$

which approaches 2 as $$n \to \infty$$. This worst-case ratio $$\text{OPT}/\text{LP}^\star$$ is called the **integrality gap** of the relaxation, and for vertex cover it equals 2. TLDR: We can't do better than 2 yet.
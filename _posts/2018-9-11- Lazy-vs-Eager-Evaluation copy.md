---
layout: post
title:  "Lazy vs Eager Evalution"
date:   2018-09-11 1:00:05 +0000
image: /assets/images/post19.png
---

***Evaluation Strategies*** are used by programming languages to determine when to evaluate the arguement(s) of a function call and what kind of value to pass to the function.

For example, ***Call By Value / Call By Reference*** specifies that a function application evaluates the argument and to modify it through an assignment statement.


![dot](/assets/images/PostImages/Evaluations.png){:height="50%" width="50%"}

---
#### 1. Lazy Evaluation

In programming language theory, lazy evaluation, or ***Call By Need*** is an evaluation strategy which delays the evaluation of an expression ***until its value is needed***(non-strict evaluation) and which also avoids repeated evaluations(sharing);

The sharing can reduce the running time of certain functions by an exponential factor over other non-strict evaluation strategies, such as ***Call By Name***.

>Benefits:
1. The ability to define control flow(structures) as abstractions instead of primitives.
2. The ability to define potentially infinite data structures. This allows for more sraightforward implementation of some algorithms.
3. Performance increases by avoiding needless alculations and error conditions in evaluating compound expressions.

***Lazy evaluation*** is often combined with memoization, as described in Jon Bentley's Writing Efficient Programs. After a function's value is computed for that parameter or set of parameters, the result is stored in a lookup table that is indexed by the values of those parameters; the next time the function is called, the table is consulted to determine whether the result for that combination of parameter values is already available. If so, the stored result is simply returned. If not, the function is evaluated and another entry is added to the lookup table for reuse.

***Lazy Evaluation*** can lead to reduction in memory footprint, since values are created when needed. However, lazy evaluation is difficult to combine with imperative features such as exception handling and input/output, because the order of operations becomes indeterminated. Lazy evaluation can introduce space leaks.


---
#### 2. Eager Evaluation

In computer programming, eager evaluation, also known as strict evaluation or greedy evaluation, is the evaluation strategy used by most traditional programming languages. In eager evaluation, an expression is evaluated as soon as it is bound to a variable. An opposite alternative to eager evaluation is lazy evaluation, where expressions are evaluated only when a dependent expression is evaluated depending upon a defined evaluation strategy.

Imperative programming languages, where the order of execution is implicitly defined by the structure of the source code, almost always use eager evaluation.

>The effects of eager evaluation include:

Code that is easily understandable in terms of execution order that does not potentially change its behaviour based on a change of execution context.
An easier debug process compared to other evaluation strategies due to the above.
Responsibility for code performance is however shifted towards the programmer, thus requiring a careful code optimisation process.


---

>**End --Cheng Gu**


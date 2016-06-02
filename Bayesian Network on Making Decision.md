title: Bayesian Network on Making Decision
categories:
  - Project
  - Artificial Intelligence
tags: [Bayesian Network, Utility, Enumeration]
date: 2016-05-31
---

### Introduction
Artificial intelligence makes great steps in recent years. We know that if all the events could be seen as true or false, we can get results of queries using first order algorithm based on existing knowledge base. However, in the real worlds, events, such as whether and accidents, always can’t be represent as true or false. For this case, we can see the happening of the events as probability. With the help of [Bayes’ rule](https://en.wikipedia.org/wiki/Bayes%27_rule) we can calculate the event’s probability based on what we have known. Luckily, the characteristics of Bayes’ rule is suitable to make the knowledge base shown as a graph. The graph descried the collection of events and the known event’s probability. The structure helps us calculate the probability in reasonable order. This article’s target is showing you how to calculate event’s probability based on enumeration and then making decision based on maximum utility policy. In order to explain clearly, we discuss this topics basing on the following Bayesian Network example.

<!-- more -->
### Bayesian Network
Based on Bayes’ rule we can make use of graphic representation of a probability distribution. In this case, the graph contains the connection between variables in graph. The graph representation helps us enumerate variables in reasonable order. Besides, all graphic properties and theories can be used to calculate the event’s probability.

### Calculate Probability by Enumeration

Based on the structure of Bayesian Network, we can enumerate variables in order. Based on the graphic [DAG]( https://en.wikipedia.org/wiki/Directed_acyclic_graph) properties, we can enumerate the variables in the DAG order. The enumeration formation is:

![](https://c3.staticflickr.com/8/7191/27129768010_a63c06da9e.jpg)

Based on the DAG order, in our example, we can calculate p (b | j, m) using following formulation:

![](https://c8.staticflickr.com/8/7595/27334320831_8b50c8c344.jpg)

The calculation process is:
$$
P(B | J, M) \\
= P(B, J, M) / … \\
=\sum_A \sum_E P(B, J, M, E, A) / ... \\
= \sum_A \sum_E P(B) P(E) P(A | B, E) P(J | A) P(M | A) / … \\
= P(B) \sum_E P(E) \sum_A P(A | B, E) P(J | A) P(M | A) / … \\
= 0.000592 / … \\
 P(¬B | J, M) \\
= P(¬B) \sum_E P(E) \sum_A P(A | ¬B, E) P(J | A) P(M | A) / … \\
= 0.00149 / … \\
P(B | J, M) = 0.284
$$
The AIMA gives us a pseudo code to calculate the above formulation.

![](https://c5.staticflickr.com/8/7513/27307341452_ebf90047de.jpg)

Based on the above pseudo code, we realize here in Python. The normalize() function is to normalize the given probability.

``` python
def enumeration_ask(X, e, bn, vars):
  QX = {}
  for xi in [False, True]:
      e[X] = xi
      QX[xi] = enumeration_all(vars, e, bn)
      del e[X]
  return normalize(QX)

def enumeration_all(vars, e, bn):
  if len(vars) == 0:
      return 1.0
  Y = vars.pop()
  if Y in e:
      val = Pr(Y, e[Y], e, bn) * enumeration_all(vars, e, bn)
      vars.append(Y)
      return val
  else:
      total = 0
      e[Y] = True
      total += Pr(Y, True, e, bn) * enumeration_all(vars, e, bn)
      e[Y] = False
      total += Pr(Y, False, e, bn) * enumeration_all(vars, e, bn)
      del e[Y]
      vars.append(Y)
      return total
```

### Making decision
In this example we use the maximum utility to make decision. Actions are selected by evaluating the decision network for each possible setting of the decision
node. Once the decision node is set, it behaves exactly like a chance node that has been
set as an evidence variable. The algorithm for evaluating decision networks is the following:
* Set the evidence variables for the current state.
* For each possible value of the decision node:
  -	Set the decision node to that value.
  -	Calculate the posterior probabilities for the parent nodes of the utility node, using a standard probabilistic inference algorithm.
  -	Calculate the resulting utility for the action.
* Return the action with the highest utility.

![](https://c1.staticflickr.com/8/7126/27129767960_c832879682.jpg)

In this example, we make decision whether should we call the police when J calls and M does not call. Base on previous calculation of probability, we assume that $P(B | J, ¬M) = 0.005$ and $P(¬B | J, ¬M) = 0.995$. Then can get the utility of calling and not calling:

$$
  EU(CP|J,¬M)
= P(B|J,¬M)U(B,CP)+P(¬B|J,¬M)U(¬B,CP)
= 0.005*0 + 0.995*(-5) = -4.975
$$
$$
EU(¬CP|J,¬M)
= P(B|J,¬M)U(B,¬CP)+P(¬B|J,¬M)U(¬B,¬CP)
= 0.005*(-100) + 0.995*0 = -0.500
$$

Based on the maximum utility policy, our decision is not calling the police.

### Usage
This script deals with the input file. The input file includes the queries we need to calculate and the knowledge which we need to build as a Bayesian Network. They are divided by line with ‘*’. In the query part, P means probability, EU means expected utility and MEU means the maximum expected Utility. In the knowledge base, ‘+’ means true and ‘-’ means false. Have a try of the script will make you get more comprehension on the usage of script. The code link is in the end of article.

### Conclusion
With the help of probability representation, we can describe more complex real world. And making use of Bayesian Network helps us calculate the wanted probabilities effectively. All graph theory can help us during the calculation.

-[github code](https://github.com/spacime/Projects/tree/master/Artificial%20Intelligence/Bayesian%20Network)

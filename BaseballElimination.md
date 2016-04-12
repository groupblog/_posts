title: Baseball Elimination
categories: 
    - Project
    - Princeton Algorithms
tags: [Graph, Maxflow, Ford-Fulkerson]
date: 2016-04-11
---
<img src="https://farm2.staticflickr.com/1586/26379159155_9fbb11ef23_z.jpg" width="589" height="301">
### Introduction
> <font size=2>In optimization theory, maximum flow problems involve finding a feasible flow through a single-source, single-sink flow network that is maximum. The maximum flow problem can be seen as a special case of more complex network flow problems, such as the circulation problem. The maximum value of an s-t flow is equal to the minimum capacity of an s-t cut in the network, as stated in the max-flow min-cut theorem. - [digital image processing](https://en.wikipedia.org/wiki/Maximum_flow_problem)</font>

maximum flow problems have many practical applications, baseball elimination is one of them. There is a division consisting of N teams. At some point during the season, team i has w[i] wins, l[i] losses, r[i] remaining games, and g[i][j] games left to play against team j. A team is mathematically eliminated if it cannot possibly finish the season in (or tied for) first place. The goal is to determine exactly which teams are mathematically eliminated.
<!--more-->
----       


### Max-flow Min-cur theorem
> <font size=2>In optimization theory, the max-flow min-cut theorem states that in a flow network, the maximum amount of flow passing from the source to the sink is equal to the minimum capacity that, when removed in a specific way from the network, causes the situation that no flow can pass from the source to the sink. The max-flow min-cut theorem is a special case of the duality theorem for linear programs and can be used to derive Menger's theorem and the König–Egerváry theorem. - [digital image processing](https://en.wikipedia.org/wiki/Max-flow_min-cut_theorem)</font>


### Ford-Fulkerson algorithm
> <font size=2>The Ford–Fulkerson method or Ford–Fulkerson algorithm (FFA) is an algorithm that computes the maximum flow in a flow network. It is called a "method" instead of an "algorithm" as the approach to finding augmenting paths in a residual graph is not fully specified or it is specified in several implementations with different running times. The idea behind the algorithm is as follows: as long as there is a path from the source (start node) to the sink (end node), with available capacity on all edges in the path, we send flow along one of the paths. Then we find another path, and so on. A path with available capacity is called an augmenting path. - [digital image processing](https://en.wikipedia.org/wiki/Ford–Fulkerson_algorithm)</font>


|<font size=1>  augmenting </font> |<font size=1>  number of paths  </font>|<font size=1>  implementation  </font>|
| :| |
|<font size=1>  shortest path </font> |<font size=1>  1/2EV  </font>|<font size=1>  queue(BFS)  </font>|
|<font size=1>  fattest path </font> |<font size=1>  Eln(EU)  </font>|<font size=1>  priority queue  </font>|
|<font size=1>  random path </font> |<font size=1>  EU  </font>|<font size=1>  randomized queue  </font>|
|<font size=1>  DFS path </font> |<font size=1>  EU  </font>|<font size=1>  stack(DFS)  </font>|


<font size=1>**different implementation about augmentation path**</font>


---
### Project
For this project, we use several integer arrays to store teams and each team's winning, losing and remaining games. Then we use FlowNetwork class to create a flow network based on teams and winning, remaining games, and use FordFulkerson class to calculate max flow and min cut. Finally based on the status of flow network, we can make a team elimination.

The goal of this project is to implement 1 class:
```java
public class BaseballElimination{
	public BaseballElimination(String filename)                    // create a baseball division from given filename in format specified below
	public              int numberOfTeams()                        // number of teams
	public Iterable<String> teams()                                // all teams
	public              int wins(String team)                      // number of wins for given team
	public              int losses(String team)                    // number of losses for given team
	public              int remaining(String team)                 // number of remaining games for given team
	public              int against(String team1, String team2)    // number of remaining games between team1 and team2
	public          boolean isEliminated(String team)              // is given team eliminated?
	public Iterable<String> certificateOfElimination(String team)  // subset R of teams that eliminates given team; null if not eliminated
}
```


> **Note:**
>  1. In order to match a index in winning/remainging array with a specific team, we use a hashmap to store team/id pairs. So that when giving a string of a team, we could quickly find the corresponding status.
>  2. Every time operating isEliminated or certificateOfElimination function, we at first check if the given team could be trivially eliminated. if not, creating a related flow network and use Ford-Fulkerson class to check if the given team could be eliminated.
>  3. We could create a Graph node to store FlowNetwork and FordFulkerson together. And when initializing flow network, we use nested for loop to try every valid matched teams and add three corresponding edges and update current index(number of nodes in network).

- [project detail discription](http://coursera.cs.princeton.edu/algs4/assignments/baseball.html)

- [github code link](https://github.com/xiaofeixiawang/algorithms/tree/master/princeton_hw8/src)

---

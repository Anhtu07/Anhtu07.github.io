---
layout: post
title:  "Polynomial Algorithm for Minimum Dominating Set in Some Family of Graphs"
date:   2020-01-09
excerpt: "This post present proof and techniques for finding minimum dominating set in some families of graphs"
image: "/images/prob_model.png"
---

## The Problem
A dominating set of a graph $G = (V(G),E(G))$ is a set $D \subset V$ of vertices such that every other vertices not in $D$ has at least a neighbor in it. Concretely, the dominating set $D$ of graph $G$ is defined as $D := \{v \in V(G) \ | \ \forall u \in V(G) \setminus D, \ \exists v \in D \ \text{such that} \ v \in N(u) \}$, where $N(u) := \{v \in V(G) \text{ such that } (u,v) \in E(G)\}$. The minimum dominating set problem asks for such a set with minimum cardinality. This problem is first stated by C.F. De Jaenisch in 1862 when he tried to find the minimum number of queens to dominate a 8x8 chessboard. Modeling the queens-problem into graph is fairly simple: each square represents a node, and two nodes have a link if a queen can travel between them in a move. From this, it is straightforward to see that minimum number of queens to cover all the chessboard will be the smallest number of vertices to dominate the entire graph. 
However, dominating set appears in many applications than just an interesting puzzle, such as online social networking or communication network. Each application requires a variation of dominating set. For example, Positive Influences Dominating Set (PIDS) is proposed for problem in social theory, while Connected Dominating Set (CDS) can serve as backbone for many communication networks. 

Most algorithm used in applications can only return a good approximation of the minimum dominating set (MDS). At the moment, we cannot hope for an efficient algorithm since MDS problem is NP-hard. In fact, the best known algorithm has exponential time complexity $O(1.4969^n)$, achieved by using a measure and conquer approach. 

Studying how to arrive at a good approximation and where the number $1.4969$ comes from are interesting. I hope to cover these topics in the future. But in this post, I want to tackle a different question related to MDS problem: On what case the MDS problem can be solved in polynomial time? This question, is, of course, not without basis. It based on a huge amount of work on the independent set. In literature, finding maximum independent set (MIS) problem is proved to have efficient algorithm (polynomial time) in many families of graph using a technique named augmenting set. Since dominating and independent set are closely related, a simple idea is to inherit the augmenting method for MDS problem. Because the general idea is we want to find a subgraph that can reduce the number of vertices in the dominating set after each iterative step, I named it reducing set. 

This newly developed technique can be applied to solve two open problems: MDS in $(fork, \overline{P_5})$-free and $(claw, P_5)$-free graph. For references, visit [here](http://www.graphclasses.org/classes/problem_Domination.html). Let $F$ be a set of graph, a graph $G$ is called $F$-free if $G$ do not have any induced subgraph contained in $F$. The rest of this blog, I will try to describe the general idea of how to solve these two problem and avoid going through too much proof. Extensive readers can access to my [paper](/docs/Polynomial_Algorithm_for_Minimum_Dominating_Set.pdf) for more details.



### $(fork, \overline{P_5})$-free Graphs
In this section, for convinient, every graphs are $(fork, \overline{P_5})$-free. My first idea for this problem is we first start with an minimal dominating set. This set will contains many maximal connected components. If this set is not minimum, then of course, exists other sets with smaller cardinality. My intuition is we can replace components of the current DS by other components to obtain smaller one. Thinking in this direction, I want to know the characteristic of a components in a minimal dominating set.

The following lemma is stated on connected dominating set. Since after a long process of refining my proof, I realize I am longer need the reducing set concept for this class of graphs, but rather a decomposition scheme for this problem. In fact, I had a proof using  reducing set, but when I found a shorter proof, it seemed redundant so I obmited it. However, the idea for reducing set is much clearer in the next section.

**Lemma 1.** The minimal connected dominating set of a $(fork, \overline{P_5})$ - free graph can only be path, cycle or clique.

The main result is to prove the minimum CDS only have a limited vertices besides some special cases. As I mentioned, how I derived from reducing set idea to this idea is a long process. But mainly I popped up with idea since I found it very hard to construct a graph that has minimum dominating set with more than 8 vertices.

After we come up with that lemma, the rest of the work only contains of ''rigorous proof". We have two cases to deal with: the first one is dominating cliquqe, and the second one is dominating path or cycle.

To find minimum dominating clique, we consider each vertex and search for the minimum dominating clique contains that vertex. Let $v \in V(G)$, if $C = v \cup N(v)$ is not a dominating set then we can conclude that $v$ does not belong to any dominating clique and continue our search to another vertex. If $C$ is dominating set of $G$, then we sequentially remove vertex of $C$ until we obtain a minimal dominating containing $v$ in $C$. The following lemma show that we can find the minimum dominating set containing $v$ in polynomial time.

**Lemma 2.** IF $C = v\cup N(v)$ is a dominating set, $M \subset  C$ such that $M$ is minimal dominating set containing $v$, then $M$ is minimum dominating set contain $v$

To deal with the second case, we try to decompose the graph into smaller ones, until it becomes $P_8$-free. Concretely, I utilize the following theorem on graph without fork proved by Alekseev

**Theorem 1.** If $G$ is $fork$-free, contains both $claw$ and $P_6$ as induced subgraph, then there exists a polynomial algorithm that partition $V(G)$ into 3 subsets $A, B$ and $C$ which satisfies all of the following:
* $G(B)$ contains $P_6$ as induced subgraph
* Any vertices in A is connected to every vertices in B
* There is no edge which has endpoints in $B$ and $C$


And one final important lemma to complete this section

**Lemma 3.** Minimum connected dominating set of $G[A \cup C]$ is also minimum connected dominating set of $G$




### $(claw, P_5)$-free Graph

















                                                                                           

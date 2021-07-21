---
layout: distill
title:  Polynomial Algorithm for Minimum Dominating Set in Some Family of Graphs
date:   2019-05-31
description: This post present proof and techniques for finding minimum dominating set in some families of graphs.

authors:
  - name: Tu Anh-Nguyen
    url: "/"
    affiliations:
      name: HUST
---

## The Problem
A dominating set of a graph $G = (V(G),E(G))$ is a set $D \subset V$ of vertices such that every other vertices not in $D$ has at least a neighbor in it. Concretely, the dominating set $D$ of graph $G$ is defined as $D := \{v \in V(G) \ | \ \forall u \in V(G) \setminus D, \ \exists v \in D \ \text{such that} \ v \in N(u) \}$, where $N(u) := \{v \in V(G) \text{ such that } (u,v) \in E(G)\}$. The minimum dominating set problem asks for such a set with minimum cardinality. This problem is first stated by C.F. De Jaenisch in 1862 when he tried to find the minimum number of queens to dominate a 8x8 chessboard. Modeling the queens-problem into graph is fairly simple: each square represents a node, and two nodes have a link if a queen can travel between them in a move. From this, it is straightforward to see that minimum number of queens to cover all the chessboard will be the smallest number of vertices to dominate the entire graph. 
However, dominating set appears in many applications than just an interesting puzzle, such as online social networking or communication network. Each application requires a variation of dominating set. For example, Positive Influences Dominating Set (PIDS) is proposed for problem in social theory, while Connected Dominating Set (CDS) can serve as backbone for many communication networks. 

Most algorithm used in applications can only return a good approximation of the minimum dominating set (MDS). At the moment, we cannot hope for an efficient algorithm since MDS problem is NP-hard. In fact, the best known algorithm has exponential time complexity $O(1.4969^n)$, achieved by using a measure and conquer approach. 

Studying how to arrive at a good approximation and where the number $1.4969$ comes from are interesting. I hope to cover these topics in the future. But in this post, I want to tackle a different question related to MDS problem: On what case the MDS problem can be solved in polynomial time? This question, is, of course, not without basis. It based on a huge amount of work on the independent set. In literature, finding maximum independent set (MIS) problem is proved to have efficient algorithm (polynomial time) in many families of graph using a technique named augmenting set. Since dominating and independent set are closely related, a simple idea is to inherit the augmenting method for MDS problem. Because the general idea is we want to find a subgraph that can reduce the number of vertices in the dominating set after each iterative step, I named it reducing set. 

This newly developed technique can be applied to solve two open problems: MDS in $(fork, \overline{P_5})$-free and $(claw, P_5)$-free graph. For references, visit [here](http://www.graphclasses.org/classes/problem_Domination.html). Let $F$ be a set of graph, a graph $G$ is called $F$-free if $G$ do not have any induced subgraph contained in $F$. The rest of this blog, I will try to describe the general idea of how to solve these two problem and avoid going through too much proof. Extensive readers can access to my [paper](/assets/pdf/Polynomial_Algorithm_for_Minimum_Dominating_Set.pdf) for more details.

<div class="row mt-3">                                                           
    <div class="col-sm mt-3 mt-md-0">                                            
        <img class="img-fluid rounded z-depth-0" src="{{ site.baseurl }}/assets/img/graphs.png">
    </div>
</div>
<div class="caption">
</div>

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

In this section, we will introduce the concept of reducing set to point out that minimum dominating set can be found in polynomial time in $(claw, P_5)$-free class. For brevity, every graph mentioned in this section is $(claw, P_5)$-free.
We can easily prove the following lemma by using the same idea from Lemma 1.

**Lemma 3.**
Every connected component of minimal dominating set is clique


Let $D_1$ be the minimal dominating set of $G$. Denote $C^1_1$ , $C^1_2$ , ... , $C^1\_{d\_1}$ be connected components of $D_1$ (here, we suppose that $D_1$ has more than 1 components i.e $d_1 > 1$). If $D_1$ is not minimum, there must exist another dominating set $D_2$ has smaller cardinality $|D_2|<|D_1|$. We also denote $C^1_1$ , $C^1_2$ , ... , $C^1\_{d\_2}$ be connected components of $D_2$.
We will say two components $C^1_i$ and $C^2_j$ adjacent if $\exists \ v_1 \in C^1_i$ and $C^2_j$ such that $v_1$ coincides $v_2$ or $v_1$ is adjacent to $v_2$. By definition of connected components, it is easily seen that two different components of the same dominating set are not adjacent. Therefore, we only say adjacent components when one component belongs to a dominating set, and the other one belongs to another dominating set.
Let $R = G[D_1 \cup D_2]$, and denote $R_1, R_2, ..., R_k$ be connected components fo $G$. Since $|D_2| < |D_1|$, there must exists $R_i$ where the number of vertices belonging to $D_1$ less that that of which in $D_1$. We call such components is reducing set, and show that if from $D_1$ we replace $D_1 \cap R_i$ by $D_2 \cap R_i$, we will obtain smaller dominating set.

**Lemma 4.**
Let $D$ be a minimal dominating set of $G$, $a, b \in D$ be two adjacent vertices. Replacing $\{u, v\}$ by $\{u, b\}$ or ($\{v, a\}$) obtains another dominating set $D'$ with the same cardinality as $D$

*Proof*. We only need to prove $D \cup u \setminus a$ a dominating set. 
Let $x \in N(u)$ and $x \notin \{a, v\}$. There must be a vertex $x$ in neighbor of $u$ and different form $a$ and $v$ because if $N(u) = \{a, v\}$ then replacing $\{u, v\}$ by $\{v, a\}$, we obtain $D'$ with satified properties. 
If $\forall x \in N(u)$, $x \notin \{a, v\}$, $x$ is adjacent to $v$, then replacing $\{u, v\}$ by $\{v, a\}$ also creates dominating set $D'$ such that $|D'| =|D|$\\
If $\exists x \in N(u)$, $x \notin \{a, v\}$, $x$ is not adjacent to $v$, then $x$ must be adjacent to $a$ otherwise $\{u, a, x, v\}$ induces a claw. In this case replacing $\{u, v\}$ by $\{v, a\}$ also creates dominating set $D'$ 
Similar argument can be made for $\{u, b\}$ 
We will point out that, every minimal dominating set in this class has a independent dominating set with less or equal cardinality. Inspired by augmenting technique, to deal with finding minimum dominating set, we will start with a minimal dominating set $D_1$, and keep reducing the number of vertices in $D_1$ until we are longer able to. Suppose $D_1$ has a component with more than 1 node, denote $u, v \in D_1$ then by lemma 6, we can replace $u, v$ by $u, b$ or $v, a$ without increasing number of node and keep the dominating properties. This replacement step always creates a connected component with single vertex, since $a \in N^r_D(u)$ (or $b \in N^r_D(v)$). However, above argument can only be true if there is no components in $D_1$ which contains only a single vertex $v$ and all of its neighbor is adjacent to other vertex in $D_1$. In this case, we can replace $v$ by one of its neighbor. If the dominating obtained after the replacement step has a non-clique connected component then it is not minimal, we can continue to remove vertices in $D_1$ to obtain smaller one. We now assume that every connected components of $D_1$ has more than 2 vertices. Let $C^1_i$ is a components of $D_1$ and $C^1_i$ = $x^i\_1$ , $x^i\_2$ , ... , $x^i\_{k\_i}$ ($k_i$ < 2). In the replacement step, we replace $C^1_i$ by $\{x^i_1, y^i_2, ..., y^i_k \}$ where $y^i_j \in N^r_D(x_j)$ $\forall \ j \in \{2, 3, ..., k_i \}$


**Lemma 5.**
After perform series of replacement steps, we obtain a dominating independent set

This lemma tells us that, for every minimal dominating set in $G$ there exist an independent set with equal or smaller size. Hence, to find minimum dominating set, we focus on finding minimum independent dominating set. Let $D\_1$ = $x^1\_1$ , $x^1\_2$ , ... , $x^1\_{k\_1}$ and $D\_2$ = $x^2\_1$ , $x^2_2$ , ..., $x^2\_{k\_2}$ be two minimal independent dominating set and $R = G[D_1 \cup D_2]$. Since $D_1$ and $D_2$ are independent, no vertices within the two set is adjacent to each other. Furthermore $\Delta(R) \leq 2$, since if $v \in R$ and $d(v) \leq 3$, suppose $v \in D_1$, then $N_R(v) \subset D_2$, therefore $v$ combined with its neighbors in $R$ create a claw. This means that every connected component of $R$ can only be path or cycle. Moreover, $G$ is $P_5$-free, every components of $R$ cannot have more than 5 vertices.


**Lemma 6.**
If $C$ is a connected component of $R$, denote $C = C_1 \cup C_2$ where $C_1$ and $C_2$ are respectively connected components of $D_1$ and $D_2$, then $D_1 \setminus C_1 \cup C_2$ is a dominating set.
*Proof.* Since $C$ is a connected component with more than 2 vertices, suppose $x_1$ and $x_2$ belongs to $C$ and adjacent to each other, where $x_1 \in C_1$ and $x_2 \in C_2$. Without loss of generality, assume contradictory that $\exists x \in G \setminus N[D_1 \setminus C]$ such that $x$ is adjacent to $x_1$ but not $x_2$. However, since $D_2$ is also dominating set, there must exist $y_2$ does not belong to $C$ and has $x$ as its neighbor. Since $D_1$ is also a dominating set, and by maximality of $C$, there must be a vertex $y_1 \in D_1$ but not in $C$ such that $y_1$ is adjacent to $y_2$ but not $x$. In this case $x_2, x_1, x, y_2, y_1$ induces $P_5$.

We are now able to state the main theorem in this section.

**Theorem 2.** Minimum doninating set in $(claw, P_5)$-free graph can be found in polynomial time.

*Proof.* From Lemma 9, we know that, in  $(claw, P_5)$-free graph, there exists an independent dominating has the minimum cardinality. We begin our algorithm by finding a minimal independent dominating set. By lemma 10, we have that a connected component of the union of two minimal dominating set can serve as reducing set. Moreover, sine $G$ is $claw$-free, the bipartite graph can only be path or cycle. $P_5$-free property make the connected components cannot have more than 5 vertices. Therefore, we can enumerate all path with length three and all cycles with length five. If no path or cycles founded can reduce the number of the current dominating set, we conclude that the minimum dominating set is found. Since after each step, the number of vertices decrease at least one, so after at most $n$ step, an minimum dominating set is found.







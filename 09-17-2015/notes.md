# September 17, 2015

## Graphs

### Definition 1

A directed graph consists of a finite set of vertices *V* and a set of directed edges *E*. A directed edge is an ordered pair of vertices *(u, v)*.

  - In mathematical terms, a directed graph *G = (V, E)* is just a binary reation *E is a subset of V x V* on a finite set *V*.

  - An undirected graph is the special case of a directed graph where *(u, v) in E* if and only if *(v, u) in E*. In this case, an edge may be indicated as an unordered pair *{u, v}*.

  - Notational convention: *|V| = n*, *|E| = m*

#### Node degree

  - **Degree** of a vertex *v* in an undirected graph: the number of edges incident to *v*.

  - **In-degree** of a vertex *v* in a directed graph: the number of edges entering *v*.

  - **Out-degree** of a vertex *v* in a directed graph: the number of edges leaving *v*.

#### Examples of graphs

  - **Transportation** networks: e.g., nodes are cities, edges (potentially weighted) are highways connecting the cities

    - Can we reach a city *j* from a city *i*?

    - If yes, what is the shortest (or cheapest) path?


  - **Information** networks: e.g., we can model the World Wide Web as a directed graph

  - **Wireless** networks: nodes are devices sitting at locations in physical space and there is an edge from u to v if v is close enough to u to hear from it.

  - **Social** networks: nodes are people, edges represent friendship

  - **Dependency networks**: e.g., given a list of functions in a large program, find an order to test the functions.

#### Useful definitions

  - A **path** is a sequence of vertices *(x1, x2, ..., xn)* such thta consecutive vertices are adjacent (edge *(xi, xi+1) in E*) for all *1 lte i lte n - 1*

  - A path is **simple** when all vertices are distinct

  - A **cycle** is a simple path that ends where it starts, that is, *xn = x1*

  - The **distance** from *u* to *v* is the length of the *shortest* path from *u* to *v*

  - An undirected graph is **connected** when there is a path between every pair of vertices.

  - The **connected component** of a node *u* is the set of all nodes in the graph reachable by a path from *u*.

  - A directed graph is **strongly connected** if for every pair of vertices *u*, *v*, there is a a path from *u* to *v* and from *v* to *u*.

  - The **strongly connected component** of a node *u* in a directed graph is the set of nodes *v* in the graph such that there is a path from *u* to *v* and from *v* to *u*.

### Trees and tree properties

#### Definition

A tree is a connected acyclic graph (undirected graph). Or,
A rooted graph such that there is a unique path from the root to any other vertex (all graphs).

It is the most widely used special type of graph: it is the minimal connected graph.

#### Lemma

*Let G be an undirected graph. Any two of the following properties imply the third property, and that G is a tree*

  1. *G is connected*

  2. *G is acyclic*

  3. *|E| = |V| - 1*

### Matchings and bipartite graphs

**Bipartite graphs**: vertices can be split into two subsets such taht there are no edges between vertices in the same subset.

  - Applications: social networks, coding theory

  - **Notation**: *G = (X union Y, E)*, where *X union Y* is the set of vertices in *G* and every edge in *E* has one endpoint in *X* and one endpoint in *Y*.

#### Example

**Goal**: find a one-to-one *matching* (also called a *perfect matching*) of people to jobs, if one exists.

### Degree theorem

*In any graph, the sum of the degrees of all vertices is equal to twice the number of the edges.*

**Proof**: Every edge is incident to two vertices, thus contributes twice to the total sum of the degrees. (Summing the degrees of all vertices simply counts all instances of some edge being incident to some vertex). 

### Running time of graph algorithms

**Input**: graph *G = (V, E), |V| = n, |E| = m*

  - **Linear** graph algorithms run in *O(n + m)* time

    - *Lower bound on m (assume **connected** graphs)?* **n - 1**

    - *Upper bound on m (assume **simple** graphs)? **n choose 2**

    - => for connected simple graphs, *O(n + m) = O(m)*

  - More general running times: the best performance is determined by the relationship between *n* and *m*

    - For example *O(n^3)* is better than *O(m^2)* if the graph is **dense** (that is, *m = Omega(n^2)* edges)

### Representing Graphs

#### Adjacency Matrix

We want to represent a graph *G = (V, E), |V| = n, |E| = m*.

**Adjacency matrix for G**: an *n x n* matrix *A* such that

*A[i, j]
  = 1, if edge *(i, j) in E
  = 0, otherwise*

**Space** required for adjacency matrix *A*: *Theta(n^2)*.

##### Advantages

  1. check whether edge *e in E* in constant time

  2. easy to adapt if the graph is weighted

  3. suitable for dense graphs where *m = Theta(n^2)*

##### Drawbacks

  1. requires *Omega(n^2)* space even if *G* is **sparse** (*m = o(n^2)*)

  2. does not allow for linear time algorithms for sparse graphs (at least when all matrix entries must be examined)

#### Adjacency List

Recall that vertex *j* is **adjacent** to vertex *i* if *(i,j) in E*; then the adjacency list for vertex *i* is simply the list of vertices adjacent to vertex *i*.

The adjacency list representation of a graph consists of an array *A* with *n* entries such that *A[i]* points to the adjacency list of vertex *i*.

(An array of linked lists)

##### Remark

**Dense** graph: *Theta(n^2)* edges

**Sparse** graph: *o(n^2)* edges (think of it as just *O(n)* edges)

Need
􏰀 an array of n pointers: O(n) space; plus
􏰀 the sum of the lengths of all adjacency lists:
􏰀 directed G: maintain the list of vertices with incoming edges from v and the list of vertices with outgoing edges to v.
􏰀 length of adjacency lists of v = outdeg(v) + indeg(v)
􏰀 length of all adjacency lists = 􏰃 outdeg(v) + indeg(v) = 2m
v
􏰀 undirected G: maintain the list of vertices adjacent to v
􏰀 length of adjacency list of v = deg(v)
􏰀 length of all adjacency lists = 􏰃 deg(v) = 2m. v
⇒ Total space: O(n + m)
Representing G = (V, E), |V | = n, |E| = m using adjacency lists has the following pros/cons.
Advantages:
􏰀 allocates no unnecessary space: O(n + m) space to represent a graph on n vertices and m edges
􏰀 suitable for linear or near-linear time algorithms
Drawbacks:
􏰀 searching for an edge can take O(n) time
We prefer adjacency matrix when
􏰀 we need determine quickly whether an edge is in the graph 􏰀 the graph is dense
􏰀 the graph is small (it is a simpler representation).
We use an adjacency list otherwise.

### Searching a graph

Given a transportation network and a city *s*, we want to find all cities reachable from *s*.

This problem is known as *s-t connectivity*.

**Input**: a graph *G = (V, E)*, a vertex *s in V*

**Output**: all vertices *t in V* such that there is a path from *s* to *t*

#### An algorithm for *s-t* connectivity: breadth-first search

**Breadth-first search (BFS)**: explore *G* starting from *s* **outward in all possible directions**, adding reachable nodes one **layer** at a time.

  - First add all nodes that are joined by an edge to *s*: these nodes form the first layer.

    - *If G is unweighted, these are the nodes at distance 1 from s*

  - Then add all nodes that are joined by an edge to a nodei n the first layer: these nodes form the second layer.

    - *If G is unweighted, these are the nodes at distance 2 from s*.

  - And so on and so forth.

##### Properties of the layers in BFS

Formally,

  - **Layer** *L0* constains *s*

  - **Layer** *L1* contains all nodes *v* such that *(s, v) in E*.

  - For *i gte 1*, **layer** *Li* contains all nodes that

    1. have an edge from a node in layer *L_i-1*; and

    2. do **not** belong to a previous layer

**Fact**:

*Li is the set of nodes that are at **distance** i from s.*

*Equivalently, the length of the shortest s-v path for all v in Li equals i*

**Proof**:

By induction:

  - **Base**: true for layer *L0*

  - **Hypothesis**: suppose *Li* is the set of nodes at distance *i* from *s*, for some *i gte 0*.

  - **Step**: The only vertices added to *Li+1* are those that

    1.. have an edge form a node in *Li*; and

    2. do **not** have an edge from any node in any previous layer *k*, for *k lt i*

Then *Li+1* contains the nodes that are at distance *1 + i* from *s*.

##### Output of BFS

  - When is a node v reachable from s added to the graph produced by BFS?

  - Why would a node fail to appear in the BFS graph?

  - Which problems are answered by BFS?

  - Why is the graph produced by BFS a tree?

  - Consider an edge *(u,v) ∈ E* that is not a tree edge; *u,v* must appear at some 
layers of the BFS tree, say *Li, Lj* respectively. How far apart can these layers be?


##### Implementing BFS

We need to store the nodes *discovered* at layer *Li* in order to *explore* them later (once we have finished exploring layer *Li*).

To this end we use a **queue**.

  - **FIFO** data structure: add to the end of the queue, extract from the head of the queue.

  - Implemented as a **double-linked list**: maintain explicit pointers to the head and tail elements. Then **enqueue** and **dequeue** operations take constant time.

###### Pseudocode

```
BFS(G = (V, E), s ∈ V )
  array dist[V] initialized to ∞        # O(n)
  array discovered[V] initialized to 0  # O(n)
  queue q
  discovered(s) = 1
  dist(s) = 0
  parent(s) = NIL
  enqueue(q, s)        
  while size(q)>0 do
    u = dequeue(q)
    for (u, v) ∈ E do
      if discovered(v) == 0 then        # O(deg(u))
        discovered(v) = 1               # O(deg(u))
        dist(v) = dist(u) + 1           # O(deg(u))
        parent(v) = u                   # O(deg(u))
        enqueue(q, v)                   # O(deg(u))
      end if
    end for
end while
```

## Review

  - Graphs (directed, undirected, weighted, unweighted)

    - Notation: *G = (V,E)*, *|V| = n*, *|E| = m*

  - Representing graphs

    1. Adjacency matrix

    2. Adjacency list

  - Trees, bipartite graphs, the degree theorem

  - Linear graph algorithms: running time *O(n + m)*

  - Breadth-first search (BFS)

**Claim 1**: Let T be a BFS tree,let x and y be nodes in T belonging to layers Li and Lj respectively, and let (x,y) be an edge in G. Then i and j differ by at most 1.
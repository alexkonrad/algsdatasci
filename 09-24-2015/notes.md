# September 24, 2015

## DFS: starting from a vertex *s*, explore the graph as deeply as possible, then **backtrack**

  1. Try the first edge out of *s*, towards some node *v*.

  2. Continue from *v* until you reach a **dead end**, that is a node whose neighbors have all been explore.

  3. **Backtrack** to the first node with an unexplored neighbor and repeat 2.

### Remark: DFS answers *s-t* connectivity

Graph edges that do not belong to the DFS tree(s) may be

  1. forward: from a vertex to a descendant (other than a child)

  2. back: from a vertex to an ancestor

  3. cross: from right to left (no ancestral relation), that is
  􏰀 from tree to tree between nodes in the same tree but on different branches

If we use an explicit stack, then

  - *start(u)* is the time when u is pushed in the stack
􏰀
  - *finish(u)* is the time when u is popped from the stack (that is, all of its neighbors have been explored).

Intervals *[start(u),finish(u)]* and *[start(v),finish(v)]* either 􏰀contain each other (u is an ancestor of v or vice versa); or 􏰀they are disjoint.

###  Classifying edges using time

  1. Edge *(u,v) ∈ E* is a back edge in a DFS tree if and only if `start(v) < start(u) < finish(u) < finish(v)`.

  2. Edge *(u,v) ∈ E* is a forward edge if `start(u) < start(v) < finish(v) < finish(u)`.

  3. Edge *(u,v) ∈ E* is a cross edge if `start(v) < finish(v) < start(u) < finish(u)`.

### Applications of DFS

#### Cycle detection

**Claim**: *G = (V, E) has a cycle if and only if `DFS(G)` yields a back edge*

**Proof**:

If *(u, v)* is a back edge, together with the path on the DFS tree from *v* to *u*, it forms a cycle.

Conversely, suppose *G* has a cycle. Let *v* be the first vertex from the cycle discovered by `DFS(G)`. Let *(u, v)* be the preceding edge in the cycle. Since there is a path from *v* to every vertex in the cycle, all vertices in the cycle are now discovered and fully explored before *v* is popped from the stack. Hence the interval of *u* is contained in the interval of *v*. By Claim 1, *(u, v)* is a back edge.

#### Topological sorting in DAGs

  - An undirected acyclic graph has an extremely simple structure: it is a tree, hence a sparse graph (*O(n) edges*).

  - A directed acyclic graph (DAG) may be dense (*Ω(n2)* edges): e.g., *V = {1,...,n}, E = {(i,j)* if *i < j* }.

**Input**:

  - a set of tasks *{1, 2, ..., n}* that need to be performed

  - a set of dependencies, each of the form *(i, j)*, indicating that task *i* must be performed before task *j*.

**Output**: a valid order in which the tasks may be performed, so that all the dependencies are respected.

*Example: tasks are courses and certain courses must be taken before others.*

*How can we model this problem using a graph? What kind of graph must arise and why?*

##### Topological Ordering

A topological ordering of *G* is an ordering of its nodes as *1,2,...,n* such that for every edge *(i,j)*, we have *i < j*.

  - All edges point **forward** in the topological ordering.

  - It provides an order in which all tasks can be safely performed: when we try to perform task *j*, all tasks required to precede it have already been done.

**Claim**: *If G has a topological ordering, then G is a DAG.*

**Proof**: By contradiction *(exercise)*.

*Is the converse true: does every DAG have a topological ordering? And how can we find it?*

In a DAG, can *every* vertex have

  - *an outgoing edge?*

  - *an incoming edge?*

##### Source and sink

A **source** is a node with no incoming edges.

A **sink** is a node with no outgoing edges.

**Fact**: *every DAG has at least one source and at least one sink.*

The ndoe that we label *first* in the topological sorting must have no incoming edges. The above fact guarantees that such a node exists.

**Fact**: Let *G′* be the graph after a source node and its adjacent edges have been removed. Then *G′* is a DAG.

**Proof**: removing edges from *G* cannot yield a cycle!

This gives rise to a recursive algorithm for finding the topological order of a DAG. Its correctness can be shown by induction (use the two above facts to show induction step).

```
TopologicalOrder(G)
  1. Find a source vertex s and order it first.
  2. Delete s and its adjacent edges from G; let G' be the new graph.
  3. TopologicalOrder(G)
  4. Append the order found after s.
```

**Running time**: *O(n^2)*.

Let *G = (V, E)* be a DAG.

  - Run `DFS(G)`; compute *finish* times.

  - Process the tasks in **decreasing** order of finish times.

Running time: *O(m + n)*

Two linked lists: one stores the sources, the other stores the in-degrees of the corresponding vertices. Remove a source *s*, then subtract 1 from all vertices to which *s* has an out-ege. If any of those vertices now have 0 in-edges, you append it to the sources list.

Running time is `sum of deg(s) = O(m)`

##### Intuition

  - The task *v* with the largest finish has no incoming edges (if it had an incoming edge from some other task *u*, then u would have the largest finish). Hence *v* does not depend on any other task and it is safe to perform it first.

  - The same reasoning shows that the task *w* with the second largest finish has no incoming edges from any other task except (maybe) task *v*. Hence it is safe to perform *w* second.

  - And so on and so forth.

##### Formal proof

By the first claim there are no back edges in the DFS forest of a DAG. Thus every edge *(u, v) ∈ E* is either

  1. **forward/tree**: *start(u) < start(v) < finish(v) < finish(u)*

  2. or **cross** edge: *finish(v) < start(u) < finish(u)*

Hence for every *(u,v) ∈ E, finish(v) < finish(u)*.

Consider a task *v*. All tasks *u* upon which *v* depends, that is, all tasks *u* such that there is an edge *(u, v) ∈ E*, satisfy *finish(v) < finish(u)*.

Since we are processing tasks in decreasing order of finish times, all tasks *u* upon which *v* depends have already been processed before we start processing *v*.

## Exploring the connectivity of a graph

  - **Undirected graphs**: find all connected components

  - **Directed graphs**: find all *strongly connected components
(SCCs)*
    
    - `SCC(u)` = set of nodes that are reachable from u and have a path back to u

    - *SCCs* provide a hierarchical view of the connectivity of the graph:

      - on a top level, the meta-graph of SCCs has a useful and simple structure (coming up)

      - each meta-vertex of this graph is a fully connected subgraph that we can further explore.

### How can we find *SCC(u)* using BFS?

  1. Run `BFS(u)`; the resulting tree *T* consists of the set of nodes to which there is a path from *u*.

  2. Define *G^r* as the reverse graph, where edge *(i, j)* becomes edge *(j, i)*.

  3. Run `BFS(u)` in *G^r*; the resulting BFS tree *T′* consists of the set of nodes that have a path to *u*.

  4. The common vertices in *T*, *T′* compose the strongly connected component of *u*.

*What if we want **all** the SCCs of the graph?*

Consider the meta-graph of all SCCs of *G*.

  - Make a (super)vertex for every SCC.

  - Add a (super)edge from SCC *Ci* to SCC *Cj* if there is an edge from some vertex *u* of *Ci* to some vertex *v* of *Cj*.

*What kind of graph is the meat-graph of SCCs?*

This graph is a DAG.

#### Is there an SCS we could process first?

Suppose we had a **sink** SCC of *G*, that is, an SCC with no outgoing edges.

  1. *What will DFS discover starting at a ndoe of a **sink** SCC?*

  2. *How do we find a node that for sure lies in a **sink** SCC?*

  3. *How do we continue to find all other SCCs?*

**Fact**: *THe node assigned the **largest** finish time when we run `DFS(G)` belongs to a **source** SCC in G.

**Proof**: Let *G* be a directed graph. The meta-graph of its SCCs is a DAG. for an SCC C, let `finish(C) = max finish(v) for v in C`.

**Lemma**: Let Ci, Cj be SCCs in G. Suppose there is an edge (u,v) ∈ E such that u ∈ Ci and v ∈ Cj. Then finish(Ci) > finish(Cj).

  - This provides a direct way to find a node in a source SCC of G: pick the node with largest finish.

  - But we want a node in a sink SCC of G!

  - Consider Gr, the graph where the edges of G are reversed. *How do the SCCs of G and Gr compare?*

  - Run DFS on Gr: the node with the largest finish comes from a source SCC of Gr. This is a sink SCC of G!

##### Using this observation to find all SCCs

We now know how to find a sink SCC in *G*.

  1. Run `DFS(Gr)`; compute *finish* times.

  2. Run `DFS(G)` starting from the node with the largest *finish*: the nodes in the resulting tree *T* form a sink SCC in *G*.

*How do we find all remaining SCCs?*

  - Remove *T* from *G*; let *G'* be the resulting graph.

  - The meta-graph of SCCs of *G'* is a DAG, hence it has at least one sink SCC.

  - Apply the procedure above recursively on *G'*.

##### Algorithm for finding SCCs in directed graphs

```
SCC(G = (V, E))
  1. Compute Gr.
  2. Run DFS(Gr); compute finish(u) for all u.
  3. Run DFS(G) in decreasing order of finish(u).
  4. Output the vertices of each tree in the DFS forest of line 3 as an SCC.
```

**Remark**:
  
  - Running time: *O(n + m)* -- why?

  - Equivalently, we can (i) run `DFS(G)`, compute *finish* times; (ii) run `DFS(Gr)` by decreasing order of *finish*. *Why?*

##### Proof of Lemma

**Lemma**: Let Ci, Cj be SCCs in G. Suppose there is an edge (u,v) ∈ E such that u ∈ Ci and v ∈ Cj. Then finish(Ci) > finish(Cj).

There are two cases two consider:

  1. *start(u) < start(v)* (DFS starts at Ci)

    - Before leaving u, DFS will explore edge (u, v).

    - Since v ∈ Cj, all of Cj will now be explored.

    - Since there is no edge from Cj back to Ci (DAG!), all vertices in Cj will be assigned finish times before DFS backtracks to u and assigns a finish time to u. Thus *finish(Cj) < finish(u) ≤ finish(Ci)*

  2. *start(u) > start(v)* (DFS starts at Cj)

    - Since there is no edge from Cj to Ci, DFS will finish exploring Cj before it restarts from some vertex that will result in discovery of Ci. Thus finish(Cj) < start(u) < finish(u) ⇒ finish(Cj) < finish(Ci)
# September 22, 2015

## Applications of BFS
  
  - Connected components in undirected graphs

  - Testing bipartiteness

### An algorithm for *s-t* connectivity: breadth-first search

**Breadth-first search (BFS)**: explore *G* starting from *s* **outward in all possible directions**, adding reachable nodes on layer at a time.

  - First add all nodes that are joined by an edge to *s*: these nodes form the first layer.

    - *If G is unwieghted, these are the nodes at distance 1 from s*

  - Then add all nodes that are joined by an edge to a node in the first layer: these nodes form the second layer.

    - *If G is unweighted, these are the nodes at distance 2 from s*

  - And so on and so forth.

  - BFS naturally produces the **connected component** *R(s)* of vertex *s*, that is the set of nodes reachable from *s*.

    - Exploring the vertices in a different order can yield different algorithms for finding connected components (coming up)

  - *How can we produce **all** the connected components of G?*

    - Consider two distinct vertices *s* and *t* in *G*: *how do their connected components compare?*

#### Fact

*For any two vertices u and v their connected components are either the same or disjoint*

##### Proof

Consider any two nodes *s,t*, such that there is a path between them: their connected components are the same.

Now consider any two nodes *s,t* such that there is no path between them: their connected components are disjoint. If not, there is a ndoe *v* that belongs to both components, hence a bath between *s* and *v* and a path between *t* and *v*. THen there is a path between *s* and *t*, contradiction.

#### Finding all the connected components of a graph

`AllConnectedComponents(G=(V,E))`

  1. Start with an arbitrary node *s*; run `BFS(G,s)` and output the resulting BFS tree as one connected component.

  2. Continue with any node *u* that has not been visited by `BFS(G,s)`; run BFS from *u* and output the resulting BFS tree as one connected component.

  3. Repeat until all nodes in *V* have been visited.

#### Testing bipartiteness * graph 2-colorability

##### Testing bipartiteness

**Input**: a graph *G = (V, E)*

**Output**: yes if G is *bipartite*, no otherwise

Equivalent problem (why?):

**Input**: a graph *G = (V, E)*

**Output**: yes if and only if we can color all the vertices in *G* using at most 2 colors--say red and white--so that no edge has two endpoints with the same colors.

**Example**: A triangle is not 2-colorable

**Fact**: if a graph contains an odd-length cycle, then it is not 2-colorable.

So a **necessary** condition for a graph to be 2-colorable is that it does not contain odd-length cycles.

*Is this condition also **sufficient**, that is, if a graph does not contain odd-length cycles, then it is 2-colorable?*

*In other words, are odd cycles the only obstacle to bipartiteness?*

##### Algorithm for 2-colorability

BFS provides a natural way to 2-color a graph *G = (V, E)*

  - Start BFS from any vertex; color it red.

  - Color white all nodes in the first layer L1 of the BFS tree. If there is an edge between two nodes in L1, output no then stop.

  - Otherwise, continue from layer L1, coloring red the vertices in even layers and white in odd layers.

  - If BFS terminates and all nodes in *V* have been explored (hence 2-colored), output yes.

##### Correctness

To prove correctness, we must show the following statement.

If the algorithm outputs

  1. **yes**, then the 2-coloring it returns is a valid 2-coloring of *G*.

  2. **no**, then indeed *G* cannot be 2-colored by **any** algorithm (e.g., because it contains an odd-length cycle).

The next claim proves that this is indeed the case by examining when the algorithm succeeds to 2-color *G*. Recall that the only reason why the algorithm fails to 2-color *G* is because it found an edge between two nodes of the same layer.

##### Claim

Let G be a connected graph, and let L1, L2, ... be the layers produced by BFS starting at node s. Then exactly one of the following two is true.

  1. There is no edge of G joining two nodes of the same layer. Then G is bipartite and has no odd length cycles.

  2. There is an edge of G joining two nodes of the same layer. Then G contains an odd length cycle, hence is not bipartite.

##### Corollary

A graph is bipartite if and only if it contains no odd length cycle.

##### Proof

1. Assume that no edge in G joins two nodes of the same layer of the BFS tree.

By Claim 1, all edges in G not belonging to the BFS tree are

  - either edges between nodes in the same layer

  - or edges between nodes in adjacent layers

Our assumption implies that all edges of G not appearing in the BFS tree are between nodes in adjacent layers.

Since our coloring procedure gives such nodes different colors, the whole graph can be 2-colored, hence it is bipartite.

2. Assume that there is an edge (u, v) ∈ E between two nodes u and v on the same layer.

Obviously G is not 2-colorable by our algorithm: both endpoints of edge (u, v) are assigned the same color.

Our algorithm returns no, hence declares G non-bipartite.

*Can we show existence of an odd-length cycle and prove that G indeed is not 2-colorable by any algorithm?*

  - Let u,v appear at layer Lj and edge (u,v) ∈ E.

  - Let z be the lowest common ancestor of u and v in the BFS tree
(z might be s). Suppose z appears at layer Li with i < j.

  - Consider the following path in G: from z to u follow edges of the BFS tree, then edge (u, v) and back to z following edges of the BFS tree. This is a cycle starting and ending at z, consisting of (j − i) + 1 + (j − i) = 2(j − i) + 1 edges, hence of odd length.


## Depth-first search

**Depth-first search (DFS)*: starting from a vertex *s*, explore the graph as deeply as possible, then *backtrack*.

  1. Try the first edge out of *s*, towards some node *v*.

  2. Continue from *v* until you reach a *dead end*, that is a node whose neighbors have all been explored.

  3. *Backtrack* to the first node with an unexplored neighbor and repeat 2.

**Remark**: DFS answers *s-t* connectivity

### Similarities

  - Linear time algorithms that essentially can be used to perform the same tasks.

### Differences

  - DFS is more *impulsive*: when it discovers an *unexplored* node, it moves on to exploring it right away; BFS defers exploring until all nodes in the layer have been discovered.

  - DFS is naturally recursive and implemented using a *stack*.

    - A stack is a LIFO (last-in first-out) data structure implemented as a linked list: insert/extract the top element requires *O(1)* time.

### Pseudocode

```
DFS(G = (V, E))
  for u∈V do                            # O(n)
    explored[u] = 0
  end for
  for u∈V do
    if explored[u] == 0 then Search(u)  # O(n)
    end if
  end for

Search(u)
  previsit(u)
  explored[u] = 1
  for (u, v) ∈ E do                     # degree of u times
    if explored[v] == 0 then Search(v)  # constant
    end if
  end for
  postvisit(u)
```

Running time:

```
sum for u in V deg(u) = O(m) + O(n)
```

### Directed graphs: classification of edges

Graph edges that do not belong to the DFS tree(s) may be

  1. forward: from a vertex to a descendant (other than a child) (e.g., (5, 7) in G)

  2. back: from a vertex to an ancestor Examples: edges (3, 1), (7, 5) in G

  3. cross: from right to left (no ancestral relation), that is

    - from tree to tree (example: edge (5, 4) in G)

    - between nodes in the same tree but on different branches

### Undirected graphs: classification of edges

*Cross* and *forward* edges do not exist on undirected graphs

In undirected graphs, DFS only yields *back* and *tree* edges.

### Time intervals for vertices

Subroutines `previsit(u)`, `postvisit(u)` may be used to maintain a notion of *time*:
  
  - In `DFS(G)`, initialize a counter *time* to 0.

  - Increment the counter by 1 every time `previsit(u)`, `postvisit(u)` are accessed.

  - Store the times *start(u)* and *finish(u)* corresponding to the first and last time *u* was visited during `DFS(G)`.

  ```
  previsit(u)
    time = time + 1
    start(u) = time

  postvisit(u)
    time = time + 1
    finish(u) = time
  ```

### On the time intervals of vertices *u, v*

If we use an explicit stack, then
  
  - *start(u)* is the time when *u* is pushed in the stack
  - *finish(u)* is the time when *u* is popped from the stack (i.e., all its neighbors have been explored)

  1. *How do intervals [start(u), finish(u)], [start(v), finish(v)] relate?*

  2. What do the contents of the stack correspond to in the graph, if s was the first vertex pushed in the stack and v the last

**Note**: intervals of the form *[start(u), finish(u)]* are either fully-contained or disjoint

#### Claim: Back edges

Edge (u,v)∈E is a back edge in a DFS tree if and only if

  - start(v) < start(u) < finish(u) < finish(v).

##### Proof

If (u, v) is a back edge, the claim follows.

Otherwise, v was pushed in the stack before u and is still in the stack when u is pushed into it. Then v is on the path from s to u in the DFS tree, thus (u, v) is a back edge.

#### Identifying forward and cross edges

*What conditions must the start and finish numbers satisfy if*

  1. *(u, v) ∈ E is a forward edge in the DFS tree?*

  2. *(u,v) ∈ E is a cross edge in the DFS tree?*

  1. Edge (u,v) ∈ E is a forward edge if start(u) < start(v) < finish(v) < finish(u).

  2. Edge (u, v) ∈ E is a cross edge if start(v) < finish(v) < start(u) < finish(u).
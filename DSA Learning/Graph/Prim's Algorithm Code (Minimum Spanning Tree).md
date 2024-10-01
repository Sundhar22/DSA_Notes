

---
#### Problem Description:
- **Goal**: Given a connected, undirected graph with `n` nodes and weighted edges, return the list of edges that form the **Minimum Spanning Tree (MST)**.
- **Input**: 
  - `edges`: A list of edges where each edge is represented by three values `[n1, n2, weight]`, indicating an edge between nodes `n1` and `n2` with a specified weight.
  - `n`: The number of nodes in the graph.
- **Output**: A list of edges making up the minimum spanning tree.

---

### Approach:

1. **Graph Representation**:
   - Use an adjacency list to store graph data. Each node points to its neighbors and their respective edge weights. The graph is constructed by iterating over the given edges.

2. **Initialize a Min-Heap**:
   - Use a **min-heap** to manage the edges. The heap helps efficiently select the edge with the smallest weight.
   - Start with an arbitrary node (node 1 in this case) and push all of its neighbors into the min-heap.

3. **MST Construction**:
   - Initialize an empty list `mst` to store the edges of the Minimum Spanning Tree.
   - Use a set `visit` to keep track of the nodes that have been added to the MST.
   - While the set `visit` contains fewer than `n` nodes:
     - Pop the smallest edge from the heap.
     - If the target node of the edge has already been visited, skip it.
     - Otherwise, add the edge to the MST and mark the node as visited.
     - Add all the new node's neighbors to the heap if they haven't been visited.

4. **Return the MST**:
   - Once all nodes are visited, the MST is complete, and the list of edges is returned.
---



---

### Edge Cases:

1. **Disconnected Graph**:
   - The algorithm assumes the graph is connected. If it's not connected, there is no valid MST, and the code may loop indefinitely. To handle this, ensure input validity.
   
2. **Single Node (n = 1)**:
   - If the graph has only one node, the MST would have no edges, and the algorithm should return an empty list.

3. **Graph with Multiple Minimum Edges**:
   - When multiple edges have the same weight, the algorithm should still produce a valid MST since it selects any valid minimum edge at each step.

---

### Time Complexity:
- **Heap Operations**: Each edge insertion and deletion from the priority queue takes `O(log E)` time, where `E` is the number of edges.
- **Adjacency List Construction**: Building the adjacency list takes `O(E)` time.
- **Total Time Complexity**: `O(E log E)` where `E` is the number of edges.

---

### Space Complexity:
- **Adjacency List**: Stores `O(E)` edges.
- **Min-Heap**: At most, `O(E)` edges will be in the heap.
- **Visited Set**: Requires `O(V)` space where `V` is the number of nodes.
- **Total Space Complexity**: `O(E + V)`.

---

### Where to Use This Pattern:

- **Graph Problems**: This approach is specific to **Minimum Spanning Tree** problems, particularly when the graph is undirected and connected.
- **Prim's Algorithm**: Prim's algorithm is useful when:
  - The graph is dense.
  - You need to prioritize minimal connections step-by-step, starting from an arbitrary node.
  
- **Related Patterns**:
  - **Greedy Algorithms**: Prim's algorithm is a classic example of the greedy approach, as it locally selects the smallest edge at each step.
  - **Priority Queue (Min-Heap)**: Used to select the next edge with the smallest weight efficiently.

---

### Alternatives and Modifications:

1. **Kruskal’s Algorithm**:
   - Another popular algorithm for finding the MST is **Kruskal's Algorithm**, which sorts the edges by weight and uses a union-find data structure to avoid cycles.
   - Kruskal’s algorithm is better suited for **sparse graphs** because it processes edges directly and doesn’t require maintaining an adjacency list.

2. **Borůvka’s Algorithm**:
   - Useful when working with **parallel computing** environments, this algorithm selects the cheapest edge for each connected component and merges them, repeating until one component is left.

3. **Dijkstra’s Algorithm**:
   - Although Prim’s algorithm and Dijkstra’s algorithm seem similar (both use a greedy strategy and a priority queue), **Dijkstra’s algorithm** solves the **shortest path** problem rather than the MST problem.

---

### Example:

```java
int[][] edges = {
    {1, 2, 1},
    {1, 3, 4},
    {2, 3, 2},
    {2, 4, 3},
    {3, 4, 5}
};
int n = 4;
List<Integer[]> result = Prim.mst(edges, n);
```

- **Input**: 
  - `edges`: `[(1, 2, 1), (1, 3, 4), (2, 3, 2), (2, 4, 3), (3, 4, 5)]`
  - `n = 4` (number of nodes)
- **Output**: 
  - List of MST edges: `[(1, 2), (2, 3), (2, 4)]`
  

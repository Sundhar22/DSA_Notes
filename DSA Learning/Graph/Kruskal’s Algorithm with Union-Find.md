
---
#### Problem Description:
- **Goal**: Given a connected, undirected graph with `n` nodes and weighted edges, return the list of edges that form the **Minimum Spanning Tree (MST)**.
- **Input**: 
  - `edges`: A 2D array of edges where each element is `[n1, n2, weight]`, representing an edge between nodes `n1` and `n2` with a given weight.
  - `n`: Number of nodes in the graph.
- **Output**: A list of edges that form the minimum spanning tree.

---

### Approach (Kruskal's Algorithm):

1. **Union-Find Data Structure**:
   - The `UnionFind` class helps efficiently manage the disjoint sets of nodes.
   - **Path Compression** is used to flatten the tree during the `find()` operation, making future lookups faster.
   - **Union by Rank** ensures the smaller tree is always added under the larger tree, keeping the overall height of the tree small, optimizing time complexity.

2. **Edge Representation**:
   - The `Edge` class encapsulates the properties of each edge (`weight`, `n1`, `n2`) and implements the `Comparable` interface to allow sorting based on weight.
   
3. **Minimum Spanning Tree (MST) Construction**:
   - Use a **priority queue** (min-heap) to sort edges by weight.
   - The algorithm proceeds by repeatedly extracting the minimum weight edge and attempting to join its two nodes using the `UnionFind` structure.
   - If the nodes are already connected, the edge is discarded (to avoid cycles).
   - This process continues until exactly `n-1` edges are added to the MST, ensuring all nodes are connected with minimal edge weight.

---

```java
import java.util.*;

class UnionFind {
    private int[] par;
    private int[] rank;

    public UnionFind(int n) {
        par = new int[n + 1];
        rank = new int[n + 1];

        for (int i = 1; i <= n; i++) {
            par[i] = i;
            rank[i] = 0;
        }
    }

    // Find parent of n, with path compression.
    public int find(int n) {
        int p = par[n];
        while (p != par[p]) {
            par[p] = par[par[p]];
            p = par[p];
        }
        return p;
    }

    // Union by height / rank.
    // Return false if already connected, true otherwise.
    public boolean union(int n1, int n2) {
        int p1 = find(n1);
        int p2 = find(n2);
        if (p1 == p2) {
            return false;
        }

        if (rank[p1] > rank[p2]) {
            par[p2] = p1;
        } else if (rank[p1] < rank[p2]) {
            par[p1] = p2;
        } else {
            par[p1] = p2;
            rank[p2]++;
        }
        return true;
    }
}

// Given a list of edges of a connected undirected graph,
// with nodes numbered from 1 to n,
// return a list edges making up the minimum spanning tree.
class Edge implements Comparable<Edge> {
    int weight;
    int n1;
    int n2;

    public Edge(int weight, int n1, int n2) {
        this.weight = weight;
        this.n1 = n1;
        this.n2 = n2;
    }

    @Override
    public int compareTo(Edge other) {
        return Integer.compare(this.weight, other.weight);
    }
}

public class Main {

    public static List<Edge> minimumSpanningTree(int[][] edges, int n) {
        PriorityQueue<Edge> minHeap = new PriorityQueue<>();
        for (int[] edge : edges) {
            minHeap.add(new Edge(edge[2], edge[0], edge[1]));
        }

        UnionFind unionFind = new UnionFind(n);
        List<Edge> mst = new ArrayList<>();
        while (mst.size() < n - 1) {
            Edge edge = minHeap.poll();
            if (!unionFind.union(edge.n1, edge.n2)) {
                continue;
            }
            mst.add(edge);
        }
        return mst;
    } 
}
```

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
List<Edge> result = Main.minimumSpanningTree(edges, n);
```

- **Input**: 
  - `edges = [(1, 2, 1), (1, 3, 4), (2, 3, 2), (2, 4, 3), (3, 4, 5)]`
  - `n = 4` (number of nodes)
- **Output**: 
  - List of edges in the MST: `[(1, 2, 1), (2, 3, 2), (2, 4, 3)]`

---
### Edge Cases:

1. **Disconnected Graph**:
   - If the graph is not connected, there will be no valid MST, and the code may enter an infinite loop since the MST would require fewer than `n-1` edges.

2. **Single Node Graph (n = 1)**:
   - In this case, the MST would be empty since no edges exist.

3. **Multiple Minimum Weight Edges**:
   - When multiple edges have the same weight, the algorithm can handle it without issue, as it selects any valid minimum edge at each step.

---

### Time Complexity:
- **Sorting Edges**: Sorting all edges using a priority queue (min-heap) takes `O(E log E)`, where `E` is the number of edges.
- **Union-Find Operations**: The union and find operations with path compression and union by rank take nearly constant time, i.e., `O(α(V))` where `α` is the inverse Ackermann function, and `V` is the number of nodes. 
- **Total Time Complexity**: `O(E log E + V α(V))`, which simplifies to `O(E log E)` because sorting dominates for most practical purposes.

---

### Space Complexity:
- **UnionFind Data Structure**: Requires `O(V)` space for the parent and rank arrays.
- **Priority Queue (Min-Heap)**: Stores up to `O(E)` edges.
- **Total Space Complexity**: `O(E + V)`.

---

### Where to Use This Pattern:
- **Graph Problems**: Kruskal's algorithm is most suitable for solving **Minimum Spanning Tree (MST)** problems, particularly when:
  - The graph is **sparse** (few edges relative to the number of nodes).
  - The edges are pre-defined, and sorting them is computationally efficient.

- **Pattern Type**:
  - **Greedy Algorithm**: Kruskal's algorithm is a greedy algorithm as it always selects the smallest available edge that doesn't form a cycle.
  - **Union-Find**: This structure is frequently used in problems involving connected components, cycle detection, and clustering.

---

### Alternatives and Modifications:

1. **Prim’s Algorithm**:
   - **Prim's Algorithm** constructs the MST by starting with an arbitrary node and expanding the tree one edge at a time by selecting the smallest edge connected to the current MST.
   - Prim’s algorithm is typically more efficient for **dense graphs** (many edges).

2. **Borůvka's Algorithm**:
   - Like Kruskal’s algorithm, **Borůvka's Algorithm** also adds edges to the MST in a greedy fashion. However, it adds one edge per connected component in parallel, which makes it suitable for distributed computing environments.
---

### Key Points:
- **Efficient for Sparse Graphs**: Kruskal’s algorithm is ideal when the graph has many nodes but relatively fewer edges.
- **Cycle Detection**: The union-find data structure is perfect for ensuring no cycles are formed, a critical requirement for constructing the MST.
- **Real-World Use Cases**:
  - **Network Design**: Kruskal’s algorithm can be applied to design efficient networks (e.g., computer networks, transportation networks) by minimizing the cost of connecting nodes (computers, cities, etc.).
  - **Clustering**: It can also be used in clustering algorithms where you want to partition data into clusters based on distance or cost.
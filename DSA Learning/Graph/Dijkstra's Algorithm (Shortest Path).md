
---
#### Problem Description:
- **Goal**: Given a directed, weighted graph represented as edges between nodes and a source node, find the shortest path from the source node to every other node.
- **Input**:
  - `edges`: A 2D array where each element is `[s, d, w]`, representing an edge from node `s` to node `d` with weight `w`.
  - `n`: Number of nodes.
  - `src`: The source node from which to compute the shortest paths.
- **Output**: A map where the keys are node numbers, and the values are the shortest distances from the source node to each node.

---

### Approach (Dijkstra's Algorithm):

1. **Graph Representation**:
   - Use an adjacency list to represent the graph. Each node points to a list of its neighbors and their respective edge weights.
   
2. **Min-Heap for Greedy Selection**:
   - A **min-heap (priority queue)** is used to greedily select the next node with the smallest current distance.
   - Initialize the heap with the source node and a distance of 0.
   
3. **Shortest Path Calculation**:
   - Start by popping the node with the smallest distance from the heap.
   - If the node has already been processed (i.e., its shortest path is already known), skip it.
   - For each neighbor of the current node, if the neighbor hasn't been processed, calculate the new distance via the current node. If it's shorter than any previously known distance, add it to the heap.
   
4. **Termination**:
   - The algorithm ends once the heap is empty and all nodes have been processed. The resulting map contains the shortest distance to each node.
---
```java

import java.util.Map;
import java.util.HashMap;
import java.util.ArrayList;
import java.util.Queue;
import java.util.PriorityQueue;

public class Dijkstra {
    
    public static Map<Integer, Integer> shortestPath(int[][] edges, int n, int src) {
        Map<Integer, ArrayList<Integer[]>> adj = new HashMap<>();
        for (int i = 1; i < n + 1; i++) {
            adj.put(i, new ArrayList<Integer[]>());
        }
        for (int[] edge : edges) {
            // s = src, d = dst, w = weight
            int s = edge[0], d = edge[1], w = edge[2];
            adj.get(s).add(new Integer[] {d, w});
        }

        HashMap<Integer, Integer> shortest = new HashMap<>();
        Queue<int[]> minHeap = new PriorityQueue<>((n1, n2) -> (n1[0] - n2[0]));
        minHeap.add(new int[]{0, src});

        while(!minHeap.isEmpty()){
            int[] cur = minHeap.remove();
            int w1 = cur[0], n1 = cur[1];
            
            if (shortest.containsKey(n1)) {
                continue;
            }
            shortest.put(n1, w1);
            for (Integer[] pair: adj.get(n1)) {
                int n2 = pair[0], w2 = pair[1];
                if (!shortest.containsKey(n2)) {
                    minHeap.add(new int[]{w1 + w2, n2});
                }
            }
        }
        return shortest;
    }
}
```
---

### Example:

```java
int[][] edges = {
    {1, 2, 4},
    {1, 3, 1},
    {3, 2, 2},
    {2, 4, 5},
    {3, 4, 8}
};
int n = 4;
int src = 1;
Map<Integer, Integer> result = Dijkstra.shortestPath(edges, n, src);
```

- **Input**: 
  - `edges = [(1, 2, 4), (1, 3, 1), (3, 2, 2), (2, 4, 5), (3, 4, 8)]`
  - `n = 4` (number of nodes)
  - `src = 1` (source node)
- **Output**: 
  - Shortest distances from node 1 to all nodes: `{1=0, 3=1, 2=3, 4=8}`

---

### Edge Cases:

1. **Disconnected Graph**:
   - If there are nodes that cannot be reached from the source, they won’t be present in the result map. You could add a check to return `Integer.MAX_VALUE` or another sentinel value for unreachable nodes.
   
2. **Negative Weights**:
   - Dijkstra's algorithm does **not** work with negative weight edges. If negative edges exist, consider using **Bellman-Ford** or **Floyd-Warshall**.

3. **Single Node Graph**:
   - If `n = 1`, the shortest path from the source to itself is 0, and the result will contain only that node.

4. **No Edges**:
   - If the graph has nodes but no edges, the result will have the source node with a distance of 0, and all other nodes are unreachable.

---

### Time Complexity:
- **Heap Operations**: For each edge, inserting or updating the distance in the min-heap takes `O(log V)`, where `V` is the number of vertices.
- **Processing Each Edge**: Each edge is processed exactly once, so the time complexity is `O(E log V)`, where `E` is the number of edges and `V` is the number of vertices.
- **Total Time Complexity**: `O(E log V)`.

---

### Space Complexity:
- **Adjacency List**: Stores `O(E)` edges.
- **Min-Heap**: At most, `O(V)` nodes will be in the heap at any given time.
- **Result Map**: Stores `O(V)` distances.
- **Total Space Complexity**: `O(E + V)`.

---

### Where to Use This Pattern:
- **Graph Problems**: Dijkstra's algorithm is ideal for solving **single-source shortest path** problems, especially when:
  - The graph has **non-negative** weights.
  - You need to find the shortest path from a single source to all other nodes.

- **Pattern Type**:
  - **Greedy Algorithm**: Dijkstra’s algorithm is a greedy algorithm because it makes a locally optimal choice at each step by selecting the smallest distance node from the heap.
  - **Priority Queue (Min-Heap)**: This data structure is crucial to efficiently pick the node with the smallest distance.

---

### Alternatives and Modifications:

1. **Bellman-Ford Algorithm**:
   - If the graph contains **negative weight edges**, use **Bellman-Ford**. It has a time complexity of `O(V * E)` and can handle graphs with negative cycles.

2. **Floyd-Warshall Algorithm**:
   - For finding shortest paths between **all pairs of nodes**, consider using **Floyd-Warshall**. However, it has a higher time complexity of `O(V^3)`.

3. **A* Algorithm**:
   - If you're looking for the shortest path between a **specific pair** of nodes (instead of from one source to all), consider using the **A* algorithm**, which can be faster with a good heuristic.
---
### Key Points:
- **Efficient for Non-Negative Weights**: Dijkstra's algorithm is fast and efficient for graphs with non-negative edge weights.
- **Real-World Use Cases**:
  - **Network Routing**: Dijkstra’s algorithm is used in network routing protocols like OSPF (Open Shortest Path First) to determine the shortest paths in a network.
  - **GPS Navigation**: It’s used in navigation systems to find the shortest or fastest route between locations.
  - **Task Scheduling**: The algorithm can also be applied to scheduling problems where certain tasks depend on the completion of others with different costs associated.


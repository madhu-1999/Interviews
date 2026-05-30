#graph #dsa 
# Overview
- The idea is to maintain two sets of vertices. The first set contains the vertices already included in the MST, and the other set contains the vertices not yet included.
- At every step, it considers all the edges that connect the two sets and picks the minimum weight edge from these edges. After picking the edge, it moves the other endpoint of the edge to the set containing MST.
# Algorithm
```pseudo
algorithm Prim(G, start)
    input: 
        G = (V, E) is a connected, weighted graph
        start ∈ V is the starting node
    output:
        T = set of edges forming the minimum spanning tree

    T ← ∅
    visited ← set containing only start
    pq ← priority queue ordered by edge weight

    for each (start, v) ∈ E do
        pq.insert((start, v), weight(start, v))

    while |visited| < |V| do
        (u, v) ← pq.extractMin()
        if v ∉ visited then
            visited.insert(v)
            T.insert((u, v))
            for each (v, w) ∈ E do
                if w ∉ visited then
                    pq.insert((v, w), weight(v, w))

    return T
```

# Example
<iframe src="https://www.cs.usfca.edu/~galles/visualization/Prim.html" name="iframe" scrolling="yes" frameborder="1" marginheight="0px" marginwidth="0px" height="500" width="1000" allowfullscreen></iframe>
# Implementation
```java
// Finds cost of MST
public class Solution {
    public int primsMST(int V, Map<Integer, List<int[]>> graph) {
        int cost = 0;

        Set<Integer> visited = new HashSet<>();
        visited.add(0);

        Queue<int[]> pq = new PriorityQueue<>(Comparator.comparingInt(e -> e[2]));
        for (int[] edge : graph.get(0)) {
            pq.offer(edge);
        }

        while (visited.size() < V) {
            int[] edge = pq.poll();
            if (!visited.contains(edge[1])) {
                visited.add(edge[1]);
                cost += edge[2];
                for (int[] e : graph.get(edge[1])) {
                    pq.offer(e);
                }
            }
        }
        return cost;
    }
}
```
# Time and Space complexity
Time complexity: O((E+V) * log(V))
Space complexity: O(E+V) where V is the number of vertex and E is the number of edges

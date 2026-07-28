#graph #dsa 
# Prerequisite
[Union Find](Union%20Find.md)
[Minimum Spanning Tree](Minimum%20Spanning%20Tree.md)
# Algorithm
1. Sort all the edges in a non-decreasing order of their weight. 
2.  Pick the smallest edge. Check if it forms a cycle with the spanning tree formed so far. If the cycle is not formed, include this edge. Else, discard it. 
3.  Repeat step 2 until there are (V-1) edges in the spanning tree.
# Example
<iframe src="https://opendsa-server.cs.vt.edu/ODSA/Books/CS3/html/Kruskal.html" name="iframe" scrolling="yes" frameborder="1" marginheight="0px" marginwidth="0px" height="1000" width="1000" allowfullscreen></iframe>
# Implementation
```java
public class Solution {
    public int kruskalsMST(int V, int[][] edges) {
        // Min heap of edges by weight O(E)
        Queue<int[]> pq = new PriorityQueue<int[]>(Comparator.comparing(e -> e[2]));
        for (int[] e : edges) {
            pq.offer(e);
        }

        DisjointSet dSet = new DisjointSet(V);
        int cost = 0, count = 0;
        while (!pq.isEmpty()) {
            int[] edge = pq.poll();
            // If no cycle forms on picking edge
            if (dSet.find(edge[0]) != dSet.find(edge[1])) {
                dSet.union(edge[0], edge[1]);
                cost += edge[2];
                if (++count == V - 1)
                    break;
            }
        }

        return cost;
    }
    
class DisjointSet {
    private int[] parent, rank;

    public DisjointSet(int V) {
        this.parent = new int[V];
        this.rank = new int[V];

        for (int i = 0; i < V; i++) {
            parent[i] = i;
        }
    }

    // Path compressed find
    public int find(int i) {
        if (parent[i] != i) {
            return parent[i] = find(parent[i]);
        }
        return parent[i];
    }

    // Union by rank
    public void union(int x, int y) {
        int xRoot = find(x);
        int yRoot = find(y);

        // Already in same set
        if (xRoot == yRoot)
            return;

        if (rank[xRoot] < rank[yRoot]) {
            parent[xRoot] = yRoot;
        } else if (rank[yRoot] < rank[xRoot]) {
            parent[yRoot] = xRoot;
        } else {
            parent[yRoot] = xRoot;
            rank[xRoot]++;
        }
    }
}
```
## Time and Space complexity
Time complexity: O( E + E * logV) = O(E * logV)
Space complexity: O(E + V)
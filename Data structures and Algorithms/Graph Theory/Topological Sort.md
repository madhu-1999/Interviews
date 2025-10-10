#graph #dsa 

# Definition
Linear ordering of vertices such that for a directed edge u-> v u appears before v in ordering.
>[!example]
>**Input:** V=6 , E = [ [2,3], [3,1], [4,0], [4,1], [5,0], [5,2] ]  
![](https://media.geeksforgeeks.org/wp-content/uploads/20200529233009/Untitled-Diagram-337.png)
**Output:** 4 5 2 0 3 1
## Assumptions
+ Graph is a DAG. (can be used to find cycles in directed graphs)
# Consideration
+ Topological ordering is not unique.
# Algorithm using DFS
+ Pick an unvisited node
+ Beginning with selected node do a DFS exploring only unvisited nodes.
+ On recursive callback, add current node to topological ordering in reverse order.

Time complexity: O(V + E)
Space complexity: O(V) due to stack

```java
import java.util.ArrayList;
import java.util.List;
import java.util.Stack;

class Solution {

    private static int[] topologicalSort(int v, int[][] edges) {
        Stack<Integer> stack = new Stack<>();
        boolean visited[] = new boolean[v];

        List<Integer>[] adj = constructadj(v, edges);
        for (int i = 0; i < v; i++) {
            if (!visited[i]) {
                dfs(i, adj, visited, stack);
            }
        }

        int[] result = new int[v];
        int index = 0;
        while (!stack.isEmpty()) {
            result[index++] = stack.pop();
        }

        return result;
    }

    private static void dfs(int u, List<Integer>[] adj, boolean[] visited, Stack<Integer> stack) {
        visited[u] = true;
        for (int i : adj[u]) {
            if (!visited[i]) {
                dfs(i, adj, visited, stack);
            }
        }
        stack.push(u);
    }

    private static List<Integer>[] constructadj(int v, int[][] edges) {
        List<Integer>[] adj = new ArrayList[v];

        for (int i = 0; i < v; i++) {
            adj[i] = new ArrayList<>();
        }

        for (int[] edge : edges) {
            adj[edge[0]].add(edge[1]);
        }
        return adj;
    }

    public static void main(String[] args) {
        int v = 6;
        int[][] edges = { { 2, 3 }, { 3, 1 }, { 4, 0 },
                { 4, 1 }, { 5, 0 }, { 5, 2 } };

        int[] ans = topologicalSort(v, edges);
        for (int node : ans) {
            System.out.print(node + " ");
        }
        System.out.println();
    }
}
```
# Using BFS (Kahn's algorithm)
+ Add all nodes with in-degree 0 to queue.
+ While queue is not empty:
	+ Remove node from queue (r)
	+ For each outgoing node from r, decrement in-degree by 1
	+ If the in-degree of  any outgoing node becomes 0 add to queue.
+ If the queue is empty and there are still nodes in the graph, the graph contains a cycle and cannot be topologically sorted.
+ How to calculate in-degree?
	+ in-degree of a node = no of incoming edges

```java
import java.util.*;

public class GfG {

    // We mainly take input graph as a set of edges. This function is
    // mainly a utility function to convert the edges to an adjacency
    // list
    static List<Integer>[] constructadj(int V, int[][] edges) {
        List<Integer>[] adj = new ArrayList[V];

        for (int i = 0; i < V; i++) {
            adj[i] = new ArrayList<>();
        }

        for (int[] edge : edges) {
            adj[edge[0]].add(edge[1]);
        }

        return adj;
    }

    // Function to return list containing vertices in Topological order
    static int[] topologicalSort(int V, int[][] edges) {
        List<Integer>[] adj = constructadj(V, edges);
        int[] indegree = new int[V];

        // Calculate indegree of each vertex
        for (int i = 0; i < V; i++) {
            for (int neighbor : adj[i]) {
                indegree[neighbor]++;
            }
        }

        // Queue to store vertices with indegree 0
        Queue<Integer> q = new LinkedList<>();
        for (int i = 0; i < V; i++) {
            if (indegree[i] == 0) {
                q.offer(i);
            }
        }

        int[] result = new int[V];
        int index = 0;

        while (!q.isEmpty()) {
            int node = q.poll();
            result[index++] = node;

            for (int neighbor : adj[node]) {
                indegree[neighbor]--;
                if (indegree[neighbor] == 0) {
                    q.offer(neighbor);
                }
            }
        }

        // Check for cycle
        if (index != V) {
            System.out.println("Graph contains a cycle!");
            return new int[0];
        }

        return result;
    }

    public static void main(String[] args) {
        int V = 6;
        int[][] edges = {{0, 1}, {1, 2}, {2, 3}, {4, 5}, {5, 1}, {5, 2}};
        
        int[] result = topologicalSort(V, edges);

        if (result.length > 0) {
            for (int i : result) {
                System.out.print(i + " ");
            }
        }
    }
}
```

# Reference:
1. https://www.youtube.com/watch?v=eL-KzMXSXXI
2. https://www.geeksforgeeks.org/dsa/topological-sorting-indegree-based-solution/
3. https://www.geeksforgeeks.org/dsa/topological-sorting/#algorithm-for-topological-sorting-using-dfs
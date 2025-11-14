#graph #dsa 
# Overview
+ Given a weighted graph (directed or undirected) and a __src__ vertex, find the shortest path distances from the source vertex to all other vertices in the graph.
+ The graph cannot have negative edge weights.
# Algorithm Using Min Heap - O(E * log V) Time and O(V) space
1. Set `dist[source]=0` and all other distances as `infinity`.
2. Push the source node into the min heap as a pair `<distance, node>` → i.e., `<0, source>`.
3. Pop the top element (node with the smallest distance) from the min heap.
    1. For each adjacent neighbor of the current node:
    2. Calculate the distance using the formula:  
        **`**dist[v] = dist[u] + weight[u][v]**`**  
            If this new distance is shorter than the current `dist[v]`, update it.  
            Push the updated pair `<dist[v], v>` into the min heap
4. ****Repeat**** step 3 until the min heap is empty.
5. Return the distance array, which holds the shortest distance from the source to all nodes.

<iframe src="https://www.cs.usfca.edu/~galles/visualization/Dijkstra.html" name="iframe" scrolling="yes" frameborder="1" marginheight="0px" marginwidth="0px" height="700" width="1000"></iframe>



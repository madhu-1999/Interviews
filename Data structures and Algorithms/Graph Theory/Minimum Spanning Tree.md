#graph #dsa 
# Spanning Tree
Subgraph of directed/undirected graph such that 
1. each vertex in graph appears once
2. there exists only one path between any two vertices (acyclic) [E = V - 1]
3. Should not be disconnected i.e. no forests
4. The total cost (or weight) of the spanning tree is defined as the sum of the edge weights of all the edges of the spanning tree.
5. There can be many possible spanning trees for a graph. ($=V^{V-2}$)
![spanningtreedrawio](https://media.geeksforgeeks.org/wp-content/uploads/20231002184939/spanningtreedrawio.png)
# Minimum Spanning Tree (MST)
A spanning tree that has the **minimum** weight among all the possible spanning trees.
1. A graph can have multiple MSTs if some edge weights in the graph are same.![MSTdrawio](https://media.geeksforgeeks.org/wp-content/uploads/20231003111632/MSTdrawio.png)
2. If all edge weights in a graph are distinct, there can be only one MST.
# Finding MST of a graph
1. [[Krushkals MST algorithm]]
2. [[Prims MST algorithm]]
# Applications
1. Used in social network analysis to find relationships between individuals or groups.
2. Used in network design to find minimum cost of connections needed to connect all nodes.
# References
1. https://www.geeksforgeeks.org/dsa/spanning-tree/
2. https://www.geeksforgeeks.org/dsa/what-is-minimum-spanning-tree-mst/
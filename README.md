# Large Scale Data Processing: Final Project
Sean Chiang, Eileen (Yifan) Zhang, Joshua Yi

## Our Matchings

Unfortunately, we were unable to obtain any matchings for our input graphs. Our algorithm would work on smaller graphs, but as more and more iterations are run, it seems that we run out of memory to compute it all.
We've attempted to remedy this by creating dynamically allocated clusters on GCP, as well as increasing the amount of memory allocated to each node, but we would still run into the issue of running out of memory, whether it be via stack overflow or heap space.

Through our analysis, we've come to the conclusion that our issue might be with the number of AggregateMessages rounds that we run upon the graph, as another group with only 2 rounds was able to get at least 2 of the matchings.
We were not able to come up with a way to further reduce our messaging rounds, and as such we concluded that we are unable to obtain the matchings necessary for this deliverable.

## Graph matching
For the final project, you are provided 6 CSV files, each containing an undirected graph, which can be found [here](https://drive.google.com/file/d/1khb-PXodUl82htpyWLMGGNrx-IzC55w8/view?usp=sharing). The files are as follows:  

|           File name           |        Number of edges       |
| ------------------------------| ---------------------------- |
| com-orkut.ungraph.csv         | 117185083                    |
| twitter_original_edges.csv    | 63555749                     |
| soc-LiveJournal1.csv          | 42851237                     |
| soc-pokec-relationships.csv   | 22301964                     |
| musae_ENGB_edges.csv          | 35324                        |
| log_normal_100.csv            | 2671                         |  

Your goal is to compute a matching as large as possible for each graph. 

### Input format
Each input file consists of multiple lines, where each line contains 2 numbers that denote an undirected edge. For example, the input below is a graph with 3 edges.  
1,2  
3,2  
3,4  

### Output format
Your output should be a CSV file listing all of the matched edges, 1 on each line. For example, the ouput below is a 2-edge matching of the above input graph. Note that `3,4` and `4,3` are the same since the graph is undirected.  
1,2  
4,3  

## Proofs and Complexity
### Israeli Itai
The Israeli-Itai  is set up in the 2 stages along with 2 substages. The first stage is to find a subgraph of G, S, with a maximal degree of 2 or less. The second phase is to find a matching sparse subgraph. In stage 1 we randomly choose an edge that has an adjacent edge, and every vertex will then choose an incoming edge. In phase 2 each vertex will randomly choose an incident edge of S and if the edge belongs to the match of M is then removed from the graph G. As such the Isreali-itai system should complete at varying times depending on the quality of finding a good vertex and good edge. A bad vertex is if at least neighbors degrees is greater than its and a bad edge is if the endpoints are bad. So if it's not bad then it would be considered good. 
	   There are 5 claims that will be covered for the Israeli-Itai algorithm.

    1. The  probability  that  a  good  vertex  of positive  degree in  G i  has  a positive  degree in  S,  is greater than or equal to 1 - e^(-1/3)
Proof. Let us assume that the vertex of degree d>0 in G and the neighbors u1,...,ud. The vertex will have k neighbors, with k being greater than or equal to 1/3d+1 such that dk= d(uk) is less than or equal to d. If any u then choose an edge in stage 1.1 of the algorithm the d of  S (v)>0.  The probability that U did choose is 1/dk greater than 1/d. If u did not choose the correct edge then the probability is
    <img src="https://latex.codecogs.com/svg.image?\prod_{j=1}^{k}(1-1/d_{j})\leq&space;(1-1/d)^{d/3}=&space;[(1-1/d)^{d}]^{1/3}&space;<&space;e^{-1/3}" title="\prod_{j=1}^{k}(1-1/d_{j})\leq (1-1/d)^{d/3}= [(1-1/d)^{d}]^{1/3} < e^{-1/3}" />  
    Thus we can assume that the probability that we found an edge is greater than 1 - e^(-⅓). This is assuming that the edge in 1.1 stage of the algorithm is indeed in S. 

    2. The probability that a  vertex of positive degree  in  S  is incident  with  M i  is  greater than  or equal to  1/2.
Proof. Let us assume the graph S is a collection of cycles and path. We can assume that there has to be an isolated edge in M and thus the probability that M is incident with one of its endpoints is 1 as well. Therefore the probability that M is incident with another vertex in G would be 1/2.

    3. At  least  one-third of the  edges  of any graph are good.
Proof. Let us assume that there is an arbitrary graph  G(V, E) and the each edge of the graph is directed from the endpoint of a smaller degree to a higher degree endpoint. For edges with an equal degree endpoint will be categorized lexicographically(ordered like 123, 132, 213, 231, 321). We then will have an acyclic directed graph.  
  
  Since  each bad vertex v in V satisfies din(v) less than or equal to ½ dout(v) we can assume that it is possible to associate each edge e that’s entering a bad vertex v a pair of edges that are disjointed. For every 2 bad edges let’s say e1 != e2 of G the edges s1(e1), s1(e2), s2(e1), s2(e2) are distinct. s1(e), s2(e) are twin pairs with the parent edge being e.  
  
  A root edge is a bad edge that either has no parent edge or has a twin edge that is good. A leaf edge is an edge that has at least one good edge successor. Since all successor edges are unique we can prove claim 3 by showing the number of leaf edges are greater than non leaf bad edges.   
  
  Let r1,..rk be the root edge of G and we partition the bad edges into k edge disjoint directed acyclic subgraph D1,...,Dk. The graph D will start with a root and will contain its successors and their successor until it reaches the leaf edges.   
  
  If the D graphs were full binary trees then the number of non leaf edges of D will be one less than  the amount of leaf edges of D. But since D does not have to be a binary tree, because two edges can enter the same vertex. However we will still construct a binary tree T for each D whose vertices will correspond to the edges of D. The vertices v1 and v2 are children of v in T, and if the corresponding edges e1 and e2 correspond to v. The number of leaf edges will be greater than the number of non leaf edges.

    4. The probability  that  a  good edge of G is removed in phi(the ith phase) is at least  1/2[1 - e(-1/3)]. 
Proof. We remove an edge in phi from M or adjacent to an edge of M. And this claims follows claim 1 and claim 2. Now that we know that at least ⅓ of the edges of any graph are good we can see that the number of expected edges to be removed in phi will be at least  1/6[1- e^(-1/3)]. We then most to our final claim. 

    5. The expected number of phases of the algorithm is O(log|E|). 
Proof. Since the expected number of edges removed in phi is at least 1/6[1- e^(-1/3)], the proof follows.  
  
However, the issue that arises from this algorithm is the equal probability choices in stages 1.1 and 1.2. Which can be solved if we dedicate a processor for each edge to run in O(log|E|). Leading to a total complexity of O(log^2|E|) expected time with |E| processors. 

### Random Choice Operation
The RCO probability of succeeding is greater 1-e^(-1). The minimal case is if there is only a single i in which xi=1. If that is the case the probability would be rj= i is 1/d and the probability for all processes is (1- 1/d)^d < e^-1. Thus the probability for success is greater than or equal to  1- (1- 1/d)^d > 1-e^-1  
    If the number of nonzero entries is f then the probability of success is 1-e^-f. If f is an increasing function of d then it is approaching 1.  
    
### Blossom Path  
    The amount of iteration that the Blossom Algorithm will take  
Theorem - The blossom Algorithm requires at most n/2 calls to FIND_AUG_PATH function (a theoretical function that finds an augmented path, since we were unable to implement this on time)  
Proof. The maximal matching in a graph with n nodes can have at most n/2 edges. We also know that the augmenting path function being called will increase the number of matching by 1. Therefore from an empty matching we can call the function at most n/2 times.  
For each iteration of the augmenting path we will go through almost all the nodes in the graph and each node we will go through unmarked edges. There are 3 cases fro any given node and unmarked edge.   
 - Case1. Blossom Recursion:
   -  Within the blossom we go through all the nodes and edges and relabel the blossom nodes with a new id to identify them. Then going through all the nodes and edges in the graph we will get a cost of O(V+E)=O(m)  
 - Case2 Add to Forest:
   -  For edge e=(v,w), if (w,x) exists in M, x being the match of w, then we add the edges (v,w) and (w,x) to the Forest. We can append these edges in an O(1) operation. As such, the time complexity of this will be O(m) with at most m calls.  
 - Case3 Return Augmenting path:
   -  Once the augmented path is found the algorithm terminates and returns to a wrapper function to determine whether or not it should be reiterated. So for each Find_Aug_Path, case 3 will happen at most once and we can get a time complexity of O(n).  
  
Cost of Blossoming path:  
<img src="https://latex.codecogs.com/svg.image?Total&space;Cost=&space;\underbrace{O(n)}_{\text{Iterations}}*[\underbrace{O(m)}_{\text{Case&space;1}}&plus;(\underbrace{O(m)}_{\text{Case&space;2}}&plus;\underbrace{O(m)}_{\text{Case&space;3}})*&space;\underbrace{O(n)}_{\text{Blossom&space;Recursions}}]&space;=&space;O(n^{2}&space;m)" title="Total Cost= \underbrace{O(n)}_{\text{Iterations}}*[\underbrace{O(m)}_{\text{Case 1}}+(\underbrace{O(m)}_{\text{Case 2}}+\underbrace{O(m)}_{\text{Case 3}})* \underbrace{O(n)}_{\text{Blossom Recursions}}] = O(n^{2} m)" />
  
### Blossom Path Parallel:  
We can only parallelize finding the augmented path in a graph, since maximal matching has dependencies too sequential to parallelize. However, we can still add the alternating edge of the matching in parallel from the augmented path that is found sequentially.  
Fortunately, adding to the Forest will not have to be modified by parallelism. We can also delay adding the edges to the forest, allowing the other two cases(blossom being found and when an augmented path is returned) to run first. There is no need to add to the forest until, it is certain that given v will have no returns or blossom recursions. Because of this the call for add_to_forest will be O(1). If there are no augmenting paths or blossom found there is a need to do post- processing by adding new edges to the forest and check to see if there are any blossoms to be found.  
 
#### Complexity  
Same as before there will 3 main cases of the algorithm.
  -  Case1: Add to forest
    -  the first part being collecting edges that are need for a temporary array(O(1) depth). The second part is post processing, which is also O(1)
  - Case2: Blossom Recursion
    -  Every Blossom recursion has an O(1) depth when contracting the graphs in parallel. But in the Worst case scenario, when deg v is less than or equal to O(n) blossom recursions for a one forest node v. 
  - Case3: Return Augmenting Path
    -  With the same complexity of running the algorithm without parallel we would get an O(n)cost dues to it being a sequential call. 
  
Total Cost:  
<img src="https://latex.codecogs.com/svg.image?T_{\infty&space;}=&space;\underbrace{O(n)}_{\text{Iterations}}*[\underbrace{O(n)}_{\text{Forest&space;Nodes}}*&space;(\underbrace{O(n)}_{\text{Case1}}&plus;&space;\underbrace{O(n)}_{\text{Case2}s})*\underbrace{O(n)}_{\text{Blossom&space;Recursions}}&space;&plus;&space;\underbrace{O(n)}_{\text{Case3}}]=&space;O(n^{3})" title="T_{\infty }= \underbrace{O(n)}_{\text{Iterations}}*[\underbrace{O(n)}_{\text{Forest Nodes}}* (\underbrace{O(n)}_{\text{Case1}}+ \underbrace{O(n)}_{\text{Case2}s})*\underbrace{O(n)}_{\text{Blossom Recursions}} + \underbrace{O(n)}_{\text{Case3}}]= O(n^{3})" />  







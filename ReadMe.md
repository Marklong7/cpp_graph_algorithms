## Leading Question 

In this project, we want to build a visualization project. Specifically, we will first find pivot schools in the collaboration networks in a specific academic field and then create a graph representing these collaboration relationships. 

Our dataset was derived from the WOS database. After the necessary data processing approach, there are at least 2 columns left there, which are named "Univ_A" and "Univ_B". They matched together in a row because the university in "Univ_A" and the university in "Univ_B" once collaborated on a paper. 

Based on our dataset, we will construct a graph using an adjacency list. We will firstly use the BFS algorithm to traverse the graph to find if there exists several connected components. Based on connected components, the Brandes Algorithm will be employed to generate the centrality value for each node. The last step is to create a graph by the forced-directed graph drawing algorithm to show relative relationships between schools. The nodes’ position are stablized after balancing the “force” and the edges represent the relative distance according to the net force.

## Dataset Acquisition

We derived our data from the WOS database (https://www-webofscience-com.proxy2.library.illinois.edu/wos/woscc/advanced-search). The Web of Science is the world’s most trusted publisher-independent global citation database. Allows us to track ideas across disciplines and time from almost 1.9 billion cited references from over 171 million records. <br>

The search query that we used is: <br>
(WC=(Statistics & Probability)) AND (TMSO==("9.92 Statistical Methods")). Where "WC" is stands for "Web of Science Categories", and "TMSO" is stands for "Citation Topics Meso". We downloaded 1,000 highly cited articles data.

## Data Format

The data format of the raw dataset is ".xls". It has 72 columns and 1,000 rows, but we'll only select 1 columns in follow-up work, which are "Affiliations". We named the dataset "1k_stat_papers.xls".
But after data cleaning, we changed the format to ".csv". We named it "data_edge_617.csv" and data_vertex_262.csv.

## Data Correction

We use R, a programming language for statistical computing, to do the data cleaning and data correction job. Specifically, We first select only the "Affiliations" column, and then we drop the rows which do not have Affiliations record (NULL Data) or only have 1 author.

A representative row in Affiliations column looks like this:
"University of Wisconsin System; University of Wisconsin Madison; Duke University"

There are 2 data correction processes that we applied:

1. String split

``` After_split = data_select$Affiliations |> 
  str_split(";", simplify = TRUE)
```
 <br>
Then we will got a matrix with 3 columns: <br>
University of Wisconsin System | University of Wisconsin Madison | Duke University

2. deduplication:
As you can see from the example, "University of Wisconsin System; University of Wisconsin Madison" is are exactly refer to the same school. So, we decided to remove all duplicate data points. This duplication is usually caused by the US public university system.

3. find a partner pairs
After string split and dedupe, our example data will looks like this:
University of Wisconsin Madison | Duke University
So we got a partner university pair. This can represent one-time collaboration between these two universities

If there are more than 2 universities in a row, for example we got 4:
univ_A | univ_B | univ_C | univ_D <br>
the matching will be like: <br>
univ_A | univ_B <br>
univ_A | univ_C <br>
univ_A | univ_D <br>
univ_B | univ_C <br>
univ_B | univ_D <br>
univ_C | univ_D <br>
Mathematically, suppose N is an arbitrary school number. The number of pairs it can generate is $N\choose 2$.
$\{number\ of\ pairs} = \frac{n!}{2!(n-2)!}\quad$

## Data Storage

We will convert the dataset from dataframe-based structure to graph-based structure. We will store our data into an adjacency list. In each linked list in an adjacency list, we will store all the vertices that are connected to a single vertex. The time complexity is O(n^2) and space complexity is O(n+m), where n is the number of vertices and m is the number of edges. 

## Algorithm 

We are going to use BFS to do the traversal. The input will be a list of nodes and the output will be a matrix representing the connected components while each line refers to one connected component. The time complexity will be O(V+E), and the space complexity will be O(V).

Secondly, we will use Brandes Algorithm to generate centrality for each node in the graph. For each connected component, we will obtain a node with most centrality in each connected component. A list of nodes of most centrality will be our output. The time complexity is O(nm) and O(n + m) is the space complexity, where m is the number of edges and n is the number of nodes.
Define σ(s, t) as the number of shortest paths between nodes s and t. Define σ(s, t|v) as the number of shortest paths between nodes s and t that pass through v. Betweenness centrality is CB(v) = sigma(σ(s, t|v) / σ(s, t))
This will be our main algorithm to find pivot schools (most influential) for each academic circles.

Lastly, we will use forced-directed graph drawing to draw a graph representing the relationships between school and their collaboration. We calculate attractive and repulsive forces between the nodes in the graph and use the net forces to update each node's position. The mass of a node is determined by betweenness centrality. We use the Fruchterman-Reingold algorithm to calculate these forces where the attractive force is inversely related to the square of the nodal distance and the repulsive force is inversely related to the nodal distance. Therefore, our input is a list of centralities and random 2-d coordinates, and our output is a graph with stable nodes, and the edges represent the relative distance according to the net force. The time complexity is O(n^3) and space complexity is O(n),where n is the number of nodes.

## Timeline

11/4/22   Complete team contract and project proposal, Create GitHub repo

11/13/22  Acquire and correct data

11/18/22  Setup files, transfer data to graph

11/25/22  Implement the BFS, and implement betweenness centrality algorithm

12/03/22  Draw force-directed graph. Debug, write the report and perform presentation

12/12/22  Write the report and record a presentation, re-check our code, and post it on GitHub

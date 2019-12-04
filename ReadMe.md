# Shortest Path Algorithm Dijkstra's Algorithm
### Costain Nachuma Parallel Computing HW4

## About the data and General info
Programming was done in C++
Loni QB cluster was used test
To show the implementation: A simple graph was used.
FileName: edge.txt 
Was read and converted into a two dimensional graph
to allow for huge matrices. the graph is represented using a edge list
A serial program was implemented then parallel version to improve the performance


## Serial Version 

Shortest path problem is used to solve different optimization problems

Algorithm

For N sized graph

function Dijkstra

visited[s] <- true

###for all i do
    
    distance[i] <- (s,i)

###loop
    x <- i where distance[i] is mininmum for visited[i] is false
    visited[x] <- true
    for all i where visited [i] is false do
        if distance[i] > distance[x] _ (x ,i) then
        distance [i] <- distance [x] + (x,i)
         indicate that we came to i from x
       

its a greedy implementation finds shortest path from a given node to all other nodes
he algorithm uses a D (distance) array, where the yet shortest paths are
saved from s.It takes the closest node to s from the not ready nodes, and marks it as ready. 
Then looks for alternative paths through this node, and if finds one, then upgrades the appropriate distance array 
value


## Parallel Version 
The parallelization of the program should start with a decomposition of the problem space into sub-problems. 
It is not trivial to do it in this case, as no clear independent parts can be noted. First, we make the division 
between the nodes. This means, that in each part of our program where a loop for all nodes is given, we rewrite it to 
consider only a subset of the nodes. We can use a block scheduling or even a loop splitting.
 We used the latter.
 
    for(i=id;i<N;i+=nproc)
    
As there is a part where the closest of the nodes is searched, and there is a part where the alternating paths are 
calculated for each node. The latter can be clearly calculated independently on all the nodes. 
But the former is problematic: we search for global minimum, not for a minimum between some nodes.

This can be done in a parallel way as follows. We search for the minimum distance of a subset of nodes, then collect
the minimums by the master, which calculates the global minimum, and sends it back to the slaves.

Thus, after calculating the local x and distance[x] we construct a pair (pair[2]) of these two. The slaves send their 
pair to 
the master. The master receives the pair, and by each receiving calculates if the received value is smaller than the 
saved one. If so, the master interchanges the saved value with the received one. At the end the minimum values are 
broadcasted back, and each process updates the x and distance[x] value by the global minimum:


## Results
First, we can clearly see, that the speed-up is somehow limited. At first as we add more and more processes the program 
accelerates rapidly. Then, as we add more, the acceleration slows down, and even stops. At the end we can even see some points where adding more processors slows down the computation.

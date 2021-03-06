---
title: Path generators and stop conditions
layout: default
---

# Path generators and stop conditions

Path generators together with stop conditions will decide what strategy to use when generating a path through a model, and when to stop generating that path. Path generators can be daisy chained after each other. Multiple stop conditions can be used using logical OR and AND.

## ***Path generators***

A generator is an algorithm that decides how to traverse a model. Different generators will generate different test sequences, and they will navigate in different ways. Multiple generators can be daisy chained, concatenated. 

### random( *some stop condition(s)* )

Navigate through the model in a completely random manor. Also called "Drunkard’s walk", or "Random walk". This algorithm selects an out-edge from a vertex by random, and repeats the process in the next vertex.

### quick_random( *some stop condition(s)* )

Tries to run the shortest path through a model, but in a fast fashion. This is how the algorithm works:

1. Choose an edge not yet visited by random.<br>
2. Select the shortest path to that edge using Dijkstra's algorithm<br>
3. Walk that path, and mark all those edges being executed as visited.<br>
4. When reaching the selected edge in step 1, start all over, repeating steps 1->4.<br>
5. The algorithm works well an very large models, and generates reasonably short sequences. The downside is when used in conjunction with EFSM. The algorithm can choose a path which is blocked by a guard.

### a_star( *a stop condition that names a vertex or an edge* )

Will generate the shortest path to a specific vertex or edge.

### shortest_all_paths ==> (Not released yet)

Will calculate and generate the shortest path through the model. The cost for every edge is set to 1.  This algorithm is not recommended to use, because for larger models, and using data in the model (EFSM), it will take a considerable time to calculate.

<br>
## ***Stop conditions***

### edge_coverage( *an integer representing percentage of desired edge coverage* )

The stop criteria is a percentage number. When, during execution, the percentage of traversed edges is reached, the test is stopped. If an edge is traversed more than one time, it still counts as 1, when calculating the percentage coverage.

### vertex_coverage( *an integer representing percentage of desired vertex coverage* )

The stop criteria is a percentage number. When, during execution, the percentage of traversed states is reached, the test is stopped. If vertex is traversed more than one time, it still counts as 1, when calculating the percentage coverage.

### reached_vertex( *the name of the vertex to reach* )

The stop criteria is a named vertex. When, during execution, the vertex is reached, the test is stopped.

### reached_edge( *the name of the edge to reach* )

The stop criteria is a named edge. When, during execution, the edge is reached, the test is stopped.

### time_duration( *an integer representing the number of seconds to run* )

The stop criteria is a time, representing the number of seconds that the test generator is allowed to execute.

Please note, that the time is compared with the execution for the whole test. For example, this means, that if you have:

- 2 models
- with common shared states
- both having ```time_duration``` stop condition set to 60 seconds each

both models will stop executing after 60 seconds. Even if one of the models haven't been visited.

### never

This special stop condition will never halt the generator.

<br>
## ***Examples***

~~~
// Will never stop generating a path sequence. Executes for ever in a random fashion.
random(never)

// Walk randomly, until the vertex coverage has reached 100%
random(vertex_coverage(100))

// Walk randomly, until the edge coverage has reached 50%
random(edge_coverage(50))

// Walk randomly, until the vertex **v_SomeVertex** is reached.
random(reached_vertex(v_SomeVertex))

// Walk the shortest path to the edge **e_SomeEdge**, and the stop.
a_star(reached_edge(e_SomeEdge))

// Walk randomly, until the requirement coverage has reached 100%
random(requirement_coverage(100))

// Walk randomly for 500 seconds.
random(time(500))

// Walk randomly until the path sequence has reached a length of 24 elements of edges and vertices.
random(length(24))

// Walk randomly, until the edge coverage has reached 100% **or** we have executed for 500 seconds.
random(edge_coverage(100) or time(500))

// Walk randomly, until the edge coverage has reached 100% **or** we have executed for 500 seconds. (same as above) random(edge_coverage(100) || time(500))

// Walk randomly, until the edge coverage has reached 100% **and** we have reached the vertex **v_SomeVertex**.
random(reached_vertex(v_SomeVertex) && edge_coverage(100))

// Walk randomly, until the edge coverage has reached 100% **and** we have reached the vertex **v_SomeVertex**. (Same as above)
random(reached_vertex(v_SomeVertex) and edge_coverage(100))

// Walk randomly, until we have executed for 500 seconds **or** we have **both** reached vertex **v_SomeVertex** **and**  reached 100% vertex coverage.
random((reached_vertex(v_SomeVertex) and vertex_coverage(100)) || time(5000))

// Walk randomly, until the edge coverage has reached 100% **and** we have reached the vertex **v_SomeVertex**. Then we will start using the next strategy. Walk randomly for 1 hour
random(reached_vertex(v_SomeVertex) and edge_coverage(100)) random(time(3600))
~~~

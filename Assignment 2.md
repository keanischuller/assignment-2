---
date: 2024-03-22
output: pdf_document
title: Assignment 2
---

`{r setup, include=FALSE} knitr::opts_chunk$set(echo = TRUE)`

First, we attach the excel dataset I made called "fakebook" that shows
the adjacency of the seats. I renamed seats A, B, C, and D to 7, 8, 9,
and 10 respectively to make it easier for me to locate each seat.

``` {r}
library(readxl)
fakebook <- read_excel("fakebook.xlsx")
attach(fakebook)
```

We load igraph and create the adjacency matrix based on the dataset
given.

``` {r}
library(igraph)

# Create the adjacency matrix based on my dataset
adjacency_matrix <- matrix(
  c(
    0, 1, 0, 0, 0, 0, 0, 0, 0, 0, # Seat 1
    1, 0, 0, 0, 0, 0, 1, 0, 0, 0, # Seat 2
    0, 0, 0, 1, 1, 0, 0, 1, 1, 1, # Seat 3
    0, 0, 1, 0, 0, 0, 0, 0, 1, 0, # Seat 4
    0, 0, 1, 0, 0, 1, 0, 0, 0, 1, # Seat 5
    0, 0, 0, 0, 1, 0, 0, 1, 0, 1, # Seat 6
    0, 1, 0, 0, 0, 0, 0, 1, 1, 0, # Seat 7 (A)
    0, 0, 1, 0, 0, 1, 1, 0, 1, 1, # Seat 8 (B)
    0, 0, 1, 1, 0, 0, 1, 1, 0, 1, # Seat 9 (C)
    0, 0, 1, 0, 1, 1, 0, 1, 1, 0  # Seat 10 (D)
  ), nrow = 10, byrow = TRUE
)
```

We create a network graph based on the adjacency matrix that is
non-directional, since everyone can speak with anyone sitting in an
adjacent seat.

``` {r}
# Create the graph from the adjacency matrix
g <- graph_from_adjacency_matrix(adjacency_matrix, mode = "undirected", diag = FALSE)
```

We calculate the centrality values: degree, closeness, and betweenness.

``` {r}
# Calculate degree centrality
degree_centrality <- degree(g, v=7:10, mode="all", normalized=FALSE)

# Calculate closeness centrality
closeness_centrality <- closeness(g, vids=7:10, mode="all", normalized=TRUE)

# Calculate betweenness centrality
betweenness_centrality <- betweenness(g, v=7:10, directed=FALSE, normalized=TRUE)
```

We put the results together and print them.

``` {r}
# Combine results
centrality_measures <- data.frame(
  Seat = 7:10,
  Degree = degree_centrality,
  Closeness = closeness_centrality,
  Betweenness = betweenness_centrality
)

print(centrality_measures)
```

### Degree Centrality

-   Beneficial: High degree centrality indicates that a seat has direct
    connections to many other seats. Choosing a seat with high degree
    centrality would mean I'm in a position to easily communicate with a
    larger number of people directly surrounding me. This can be
    advantageous for networking, sharing information quickly, and
    integrating into the social fabric of Fakebook more seamlessly.

-   Not so beneficial: While being in a central position allows for
    greater visibility and access to information, it might also lead to
    stretching myself thin. If I want to develop deeper, more focused
    relationships, a seat with too many direct connections might spread
    my attention too thin, juggling conversations with so many people.

### Closeness Centrality

-   Beneficial: High closeness centrality suggests that a seat is, on
    average, close to all other seats in terms of the shortest paths.
    This could be advantageous for becoming a hub of information
    exchange, since I can quickly disseminate or gather information from
    across the bus. It allows me to foster rapid engagement with various
    individuals.

-   Not so beneficial: Similarly to the issue with high degree
    centrality, being too close to the majority of seats in the bus may
    make me spread myself too thing trying to network with everyone.

### Betweenness Centrality

-   Beneficial: High betweenness centrality indicates that a seat acts
    as a bridge within the network, controlling the flow of information
    between different groups of seats. This oculd allow me to get
    diverse viewpoints and information, while also boosting my
    reputation as a team player.

-   Not so beneficial: The drawback of being a bridge is that it might
    place me in a position where I'm facilitating others' interactions,
    which can be potentially stressful, especially as an intern who is
    new to Fakebook. There's also the risk of becoming a bottleneck in
    communication flows, where too much reliance on me can hinder direct
    interactions between others.

### Conclusion

In the context of developing informal connections at work, the choice of
seat would significantly influence the nature and quality of my
interactions. Based on the centrality values calculated, the best seat
to choose would be Seat 8 or Seat C. This is because it has the highest
degree, closeness, and betweeness centrality overall. Seat C offers the
best balance of being directly connected to others (degree centrality)
and being centrally located within the network (closeness centrality),
ensuring I'm well-positioned to meet a broad range of people and access
information efficiently (betweenness centrality).

To plot we have to add our measures as graph attributes. We plot the
graph with labels and centrality values.

``` {r}
# Sizing & labels
degree_centrality_norm <- degree_centrality / max(degree_centrality)
node_sizes <- degree_centrality_norm * 50 
node_labels <- paste(V(g)$name, "\nCentrality:", round(degree_centrality), ",",
                     round(closeness_centrality, 3), ",",  round(betweenness_centrality, 3), sep=" ")
par(mar=c(1, 1, 1, 1))

# Plot the graph
plot(g,
     layout = layout_with_kk(g),
     vertex.size=node_sizes,
     vertex.label=node_labels, 
     vertex.label.cex=0.8, 
     edge.arrow.size=0.3,
     vertex.label.dist = 0.5,
     main="Fakebook Bus Seats Network Graph",
     vertex.color="lavender",
     vertex.label.color = "black",
     vertex.label.family = "sans")

legend("bottomright",
       title="Centrality Measures", 
       cex=0.6, 
       bty="n", 
       legend=c("Degree Centrality , Closeness Centrality , Betweenness Centrality"),
       )
```

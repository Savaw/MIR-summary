# Chapter 21 - Link analysis

## The Web as a graph

two intuitions:

1. anchor text pointing to page B is a good description of page B
2. The hyperlink from A to B represents an endorsement of page B, by the creator of page A

### **Anchor text and the web graph**

- there is often a gap between the terms in a web page, and how web users would describe that web page
- many web pages are rich in graphics and images, and/or embed their text in these images

→ use of anchor texts are helpful

Anchor text terms are generally weighted based on frequency, with a penalty for terms that occur very often (using methods very similar to idf).

side-effects:

orchestrated anchor text may be a form of spamming. e.g. leads query for *evil empire* to somewhat unexpected

extended anchor text can be useful too.

**extended anchor text:** The window of text surrounding anchor text

## PageRank

**PageRank:** assigns to every node in  the web graph a numerical score between 0 and 1.

Consider a random surfer who begins at a web page and executes a random walk on the Web as follows: At each time step, the surfer proceeds from his current page A to a randomly chosen web page that A hyperlinks to.

The idea behind PageRank is that pages visited more often in this walk are more important.

**teleport operation**: surfer jumps from a node to any other node in the web graph

usage:

1. When at a node with no out-links, the surfer invokes the teleport operation.
2. At any node that has outgoing links, the surfer invokes the teleport operation with probability 0 < α < 1. (Typically, α might be 0.1.)

### Markov chains

- **a discrete-time stochastic process**: a process that occurs in a series of time-steps in each of which a random choice is made
- characterized by an N × N transition probability matrix P
- **transition probability** P_ij tells us the probability that the state at the next time step is j, conditioned on the current state being i.
- **Markov property:**
    
    ![Screen Shot 2022-01-13 at 13.21.18.png](Chapter%2021%20-%20Link%20analysis%20d4ca32a934584f719abcd9f92ada5001/Screen_Shot_2022-01-13_at_13.21.18.png)
    
- **stochastic matrix**: A matrix with non-negative entries that satisfies above equation.
    - key property: it has a principal left eigenvector corresponding to its largest eigenvalue, which is 1.
- **probability vector**: a vector all of whose entries are in the interval [0, 1], and the entries add up to 1.

We can view a random surfer on the web graph as a Markov chain:

- adjacency matrix A of the web graph: if there is a hyperlink from page i to page j, then Aij = 1, otherwise Aij = 0.
- derive the transition probability matrix P for our Markov chain from the N × N matrix A:
    1. If a row of A has no 1’s, then replace each element by 1/N.
    2. For other rows, Divide each 1 in A by the number of 1’s in its row
    3. Multiply the resulting matrix by 1 − α.
    4. Add α/N to every entry of the resulting matrix, to obtain P.
    

**Ergodic Markov Chain**

A Markov chain is said to be ergodic if there exists a positive integer T_0 such that for all pairs of states i, j in the Markov chain, if it is started at time 0 in state i then for all t > T_0, the probability of being in state j at time t is greater than 0.

For a Markov chain to be ergodic, two technical conditions are required:

1. **irreducibility** 
    - ensures that there is a sequence of transitions of non-zero probability from any state to any other
2. **aperiodicity**
    - ensures that the states are not partitioned into sets such that all state transitions occur cyclically from one set to another.

*Theorem*: For any ergodic Markov chain, there is a unique steady-state probability vector π that is the principal left eigenvector of P, such that if η(i, t) is the number of visits to state i in t steps, then

![Screen Shot 2022-01-13 at 13.37.10.png](Chapter%2021%20-%20Link%20analysis%20d4ca32a934584f719abcd9f92ada5001/Screen_Shot_2022-01-13_at_13.37.10.png)

where π(i) > 0 is the **steady-state probability** for state i.

→ This steady-state probability for a state is the PageRank of the corresponding web page

### The PageRank computation

Recall the definition of a left eigenvector:  $\vec{\pi}P=\lambda\vec{\pi}$

- if π is the probability distribution of the surfer across the web pages, he remains in the steady-state distribution π. Given that π is the steady-state distribution, we have that πP = 1π, so 1 is an eigenvalue of P.
- Thus if we were to compute the principal left eigenvector of the matrix P—the one with eigenvalue 1— we would have computed the PageRank values.

**power iteration**, elementary method for calculating pageRank:

The power iteration method simulates the surfer’s walk: begin at a state and run the walk for a large number of steps t, keeping track of the visit frequencies for each of the states. After a large number of steps t, these frequencies “settle down” so that the variation in the computed frequencies is below some predetermined threshold. We declare these tabulated frequencies to be the PageRank values.

- PageRank is a query independent measure of the static quality of each web page

### Topic-specific PageRank

consider teleporting to a random web page chosen non-uniformly.

- teleports to a random web page *on the topic of A* instead of teleporting to a uniformly chosen random web page
- We will not focus on how we collect all web pages on the topic A; in fact, we only need a non-zero subset S of A-related web pages, so that the teleport operation is feasible.
    - This may be obtained, for instance, from a manually built directory

Provided the set S of A-related pages is non-empty, it follows that there is a non-empty set of web pages Y ⊇ S over which the random walk has a steady-state distribution; let us denote this *A PageRank* distribution by $\vec{\pi}_{s}$ . For web pages not in Y, we set the PageRank values to zero. We call  $\vec{\pi}_{s}$ the **topic-specific PageRank** for A.

This can be used when the search engine knows what topic a user is interested in:

- users either explicitly register their interests
- the system learns by observing each user’s behavior over time.

What if a user is known to have a mixture of interests from multiple topics?

→ **personalized PageRank**

We can use the fact that the evolution of the probability distribution over the states of a Markov chain can be viewed as a linear system.

For instance, the personalized PageRank vector for the user whose interests are 60% sports and 40% politics can be computed as $0.6\vec{\pi}_{sports}+0.4\vec{\pi}_{politics}$

## Hubs and Authorities

insight:

there are two primary kinds of web pages useful as results for broad-topic searches:

1. **authoritative** **sources** of information on the topic
2. **hub pages** that are not in themselves authoritative sources of topic-specific information, but are hand-compiled lists of links to authoritative web pages on a specific topic

- A good hub page is one that points to many good authorities; a good authority page is one that is pointed to by many good hub pages.

We now assign two score to each document:

- **hub score**: h(v)
- **authority score**: a(v)

How to compute?

an iterative computation:

1. Initially, set h(v) = a(v) = 1
    - denote by v → y the existence of a hyperlink from v to y
2. Recompute scores using following equations:

![Screen Shot 2022-01-13 at 15.36.44.png](Chapter%2021%20-%20Link%20analysis%20d4ca32a934584f719abcd9f92ada5001/Screen_Shot_2022-01-13_at_15.36.44.png)

Recast the above equations equations into matrix-vector form:

A: the adjacency matrix of the subset of the web graph

![Screen Shot 2022-01-13 at 15.41.33.png](Chapter%2021%20-%20Link%20analysis%20d4ca32a934584f719abcd9f92ada5001/Screen_Shot_2022-01-13_at_15.41.33.png)

Substitue:

![Screen Shot 2022-01-13 at 15.42.03.png](Chapter%2021%20-%20Link%20analysis%20d4ca32a934584f719abcd9f92ada5001/Screen_Shot_2022-01-13_at_15.42.03.png)

Replace ← with = and introduce the (unknown) eigenvalues:

![Screen Shot 2022-01-13 at 15.43.31.png](Chapter%2021%20-%20Link%20analysis%20d4ca32a934584f719abcd9f92ada5001/Screen_Shot_2022-01-13_at_15.43.31.png)

the first line is equation for the eigenvectors of AA^T

the second becomes the equation for the eigenvectors of A^TA

The resulting computation thus takes the following form:

**HITS** or **Hyperlink-Induced Topic Search**

1. Assemble the target subset of web pages, form the graph induced by their hyperlinks and compute AA^T and A^TA.
2. Compute the principal eigenvectors of AAT and ATA to form the vector of hub scores h and authority scores a.
3. Output the top-scoring hubs and the top-scoring authorities

### Choosing the subset of the Web

procedure for compiling the subset of the Web for which to compute hub and authority scores:

1. Given a query (say leukemia), use a text index to get all pages containing leukemia. Call this the **root set** of pages.
2. Build the **base set** of pages, to include the root set as well as any page that either links to a page in the root set, or is linked to by a page in the root set.

then use the base set for computing hub and authority scores.

reasons:

1. A good authority page may not contain the query text
2. If the text query manages to capture a good hub page vh in the root set, then the inclusion of all pages linked to by any page in the root set will capture all the good authorities linked to by vh in the base set
3. Conversely, if the text query manages to capture a good authority page va in the root set, then the inclusion of pages which point to va will bring other good hubs into the base set.

some elements of cross-language retrieval are evident in this algorithm.
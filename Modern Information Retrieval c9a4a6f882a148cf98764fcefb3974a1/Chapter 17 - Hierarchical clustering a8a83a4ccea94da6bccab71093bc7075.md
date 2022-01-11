# Chapter 17 - Hierarchical clustering

In general, we select flat clustering when efficiency is important and hierarchical clustering when one of the potential problems of flat clustering (not enough structure, predetermined number of clusters, non-determinism) is a concern.

### Hierarchical agglomerative clustering

hierarchical agglomerative clustering (**HAC**):

- a Bottom up algorithm
**bottom up algorithms:** treat each document as a singleton cluster at the outset and then successively merge (or agglomerate) pairs of clusters until all clusters have been merged into a single cluster that contains all documents.
- visualized as a dendrogram
    
    ![Screen Shot 2022-01-11 at 16.33.43.png](Chapter%2017%20-%20Hierarchical%20clustering%20a8a83a4ccea94da6bccab71093bc7075/Screen_Shot_2022-01-11_at_16.33.43.png)
    
    - The y-coordinate of the horizontal line is the similarity of the two clusters that were merged → **combination similarity** (above dendrogram is rotated)
- fundamental assumption in HAC: the merge operation is monotonic
    - **monotonic**: if s1, s2, . . . , sK−1 are the combination similarities of the successive merges of an HAC, then s1 ≥ s2 ≥ . . . ≥ sK−1 holds
    

Cutting point criteria if we want a partition of disjoint clusters:

- Cut at a prespecified level of similarity
- Cut the dendrogram where the gap between two successive combination similarities is largest.
- Apply following equation (from previous chapter):
    
    ![Screen Shot 2022-01-11 at 16.45.18.png](Chapter%2017%20-%20Hierarchical%20clustering%20a8a83a4ccea94da6bccab71093bc7075/Screen_Shot_2022-01-11_at_16.45.18.png)
    
- Cut when reach a prespecified number of clusters K

**A simple, naive HAC algorithm:**

1. compute the N × N similarity matrix C
2. executes N − 1 steps of merging the currently most similar clusters:
    1. merge the two most similar clusters
    2. update rows and columns of the merged cluster i in C
    

## Single-link and complete-link clustering

base algorithm is the same as naive HAC

**single-link / single-linkage clustering:** 

- the similarity of two clusters is the similarity of their most similar members
- merge criterion is local - pay attention solely to the area where the two clusters come closest to each other

**complete-link / complete-linkage clustering:**

- the similarity of two clusters is the similarity of their most dissimilar members - choosing the cluster pair whose merge has the smallest diameter
- merge criterion is non-local ****- the entire structure of the clustering can influence merge decisions

These two algorithms reduce the assessment of cluster quality to a single similarity between a pair of documents and therefore both algorithms often produce undesirable clusters.

for example:

- single link problem: a chain of points can be extended for long distances without regard to the overall shape of the emerging cluster → **chaining**
- complete-link problem: it pays too much attention to outliers, points that do not fit well into the global structure of the cluster.

### Time complexity of HAC

naive: $\Theta(N^3)$

using priority queue: $\Theta(N^2logN)$

single-link: $\Theta(N^2)$

Q: Why we can improve single-link and not others?

A: single-link is **best-merge persistent** - i.e. Suppose that the best merge cluster for ωk is ωj in single-link clustering. Then after merging ωj with a third cluster ωi != ωk, the merge of ωi and ωj
will be ωk’s best merge cluster.

We can store an array named NBM (next best merge) for each cluster, and we only need to update this array for new merged cluster and there is no need to update value for others, so the update is O(N) and there is no need for a priority queue. But we cannot this for other similarity functions.

## Group-average agglomerative clustering (GAAC)

aka: group-average clustering, average-link clustering

Evaluates cluster quality based on all similarities between documents

![Screen Shot 2022-01-11 at 20.16.55.png](Chapter%2017%20-%20Hierarchical%20clustering%20a8a83a4ccea94da6bccab71093bc7075/Screen_Shot_2022-01-11_at_20.16.55.png)

$\vec{d}$ is the length-normalized vector of document d.

Since sum of individual vector similarities is equal to the similarities of their sums, we have:

(Only true if using dot product!)

![Screen Shot 2022-01-11 at 20.18.51.png](Chapter%2017%20-%20Hierarchical%20clustering%20a8a83a4ccea94da6bccab71093bc7075/Screen_Shot_2022-01-11_at_20.18.51.png)

## Centroid clustering

The similarity of two clusters is defined as the similarity of their centroids

![Screen Shot 2022-01-11 at 20.32.33.png](Chapter%2017%20-%20Hierarchical%20clustering%20a8a83a4ccea94da6bccab71093bc7075/Screen_Shot_2022-01-11_at_20.32.33.png)

- centroid clustering is not monotonic - **inversions** can occur. i.e. Similarity can increase during clustering (we define similarity as negative distance)
- other three clustering algorithms are monotonic. In a monotonic HAC algorithm, similarity is monotonically decreasing from iteration to iteration.

## Optimality of HAC

**combination similarity:** COMB-SIM of a clustering W = {ω1, . . . ,ωK} as the smallest combination similarity of any of its K clusters.

We define  $\Omega$ to be optimal if all clusterings $\Omega'$ with k clusters, k ≤ K, have lower combination similarities.

- Centroid clustering is not optimal because of inversions
- Complete-link clustering and GAAC are not optimal, example:
    
    ![Screen Shot 2022-01-11 at 22.21.16.png](Chapter%2017%20-%20Hierarchical%20clustering%20a8a83a4ccea94da6bccab71093bc7075/Screen_Shot_2022-01-11_at_22.21.16.png)
    
- Single link is optimal (prove by induction page 431)

GAAC is best choice for most applications

two exceptions:

- non vector representation
- the purpose of clustering is not to create a complete hierarchy or exhaustive partition of the entire document set
    - **first story detection** or **novelty detection:** the task of detecting the first occurrence of an event in a stream of news stories.

## Divisive clustering

aka: **top-down clustering**

We start at the top with all documents in one cluster. The cluster is split using a flat clustering algorithm. This procedure is applied recursively until each document is in its own singleton cluster.

in comparison to bottom-up:

- more complex
- more efficient if we do not generate a complete hierarchy
- more accurate hierarchies in some circumstances

## Cluster labeling

**Differential cluster labeling:** 

Selects cluster labels by comparing the distribution of terms in one cluster with that of other clusters.

- feature selection methods like MI and χ2 can be used
- a differential test + a penalty for rare terms → often gives the best labeling results

**Cluster-internal labeling:**

Computes a label that solely depends on the cluster itself, not on other clusters.

- e.g. the title of the document closest to the centroid
- e.g. use a list of terms with high weights in the centroid of the cluster
- efficient, but they fail to distinguish terms that are frequent in the collection as a whole from those that are frequent only in the cluster.

## Implementation notes

- Computational savings due to the inverted index are large if there are many zero similarities
- for dense centroids:
    - GAAN: $\Theta(MN^2logN)$ (M is the size of the vocabulary)
    - complete link: $\Theta(M_{ave}N^2logN)$ (M_ave is the average size of the vocabulary of a document.)
    - so complete link is more efficient
- to get read of dense centroids: (covered in last chapter)
    - truncating
    - medoids
- for large sets use HAC with flat clustering:
    - e.g. employ an HAC algorithm to compute seeds of high quality for K-means:
    Apply HAC to sqrt(N) subset of documents
    
    → **Buckshot algorithm:** combines the determinism and higher reliability of HAC with the efficiency of K-means.
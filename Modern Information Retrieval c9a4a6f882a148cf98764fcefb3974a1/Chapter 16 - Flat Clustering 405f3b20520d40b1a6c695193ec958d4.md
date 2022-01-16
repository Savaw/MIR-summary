# Chapter 16 - Flat Clustering

clustering → unsuprvised learning

key input to a clustering algorithm → distance measure

- **Flat clustering** creates a flat set of clusters without any explicit structure that would relate clusters to each other.
- **Hierarchical clustering** creates a hierarchy of clusters.

- **Hard clustering** computes a hard assignment – each document is a member of exactly one cluster.
- The assignment of **soft clustering** algorithms is soft – a document’s assignment is a distribution over all clusters. In a soft assignment, a document has fractional membership in several clusters.

## Clustering in information retrieval

**Cluster hypothesis:** Documents in the same cluster behave similarly with respect to relevance to information needs.

main applications of clustering in IR:

![Screen Shot 2022-01-11 at 11.12.06.png](Chapter%2016%20-%20Flat%20Clustering%20405f3b20520d40b1a6c695193ec958d4/Screen_Shot_2022-01-11_at_11.12.06.png)

1. Search result clustering
    - e.x. useful if a search term has different word senses. (jaguar)
2. Scatter-Gather
    - Scatter-Gather clusters the whole collection to get groups of documents that the user can select or gather. The selected groups are merged and the resulting set is again clustered until a cluster of interest is found.
3. Collection clustering
    - cluster whole collection
4. Language modeling 
    - exploits the cluster hypothesis directly for improving search results
    - return documents in same cluster → improve recall
5. Cluster-based retrieval
    - Find the clusters that are closest to the query and only consider documents from these
    clusters. e.x. to finding the nearest neighbors
    

## Problem statement

goal in hard flat clustering:

Given:

1. a set of documents D = {d1, . . . , dN}
2. a desired number of clusters K
3. an **objective function** that evaluates the quality of a clustering

we want to compute an assignment γ : D → {1, . . . , K} that minimizes (or, in other cases, maximizes) the objective function. In most cases, we also demand that γ is surjective, i.e., that none of the K clusters is empty.

A note on terminology:

An alternative definition of hard clustering is that a document can be a full member of more than one cluster.

**Partitional** clustering always refers to a clustering where each document belongs to exactly
one cluster.

**exhaustive** clusterings**:** assign each document to a cluster

**non-exhaustive** clusterings: some documents will be assigned to no cluster. → **exclusive**

**Cardinality:** the number of clusters denoted by K

selection of K:

- a good guess based on experience or domain knowledge.
- incorporate the selection of K into the objective function

## Evaluation of clustering

**internal criterion:** attaining high intra-cluster similarity and low inter-cluster similarity (via objective function)

alternative to internal criteria → direct evaluation in the application of interest

**external criterion:** evaluates how well the clustering matches the gold standard classes.

four external criteria of clustering quality:

1. **Purity**
    - each cluster is assigned to the class which is most frequent in the cluster, and then the accuracy of this assignment is measured by counting the number of correctly assigned documents and dividing by N.
        
        ![Screen Shot 2022-01-11 at 12.10.46.png](Chapter%2016%20-%20Flat%20Clustering%20405f3b20520d40b1a6c695193ec958d4/Screen_Shot_2022-01-11_at_12.10.46.png)
        
        C = {c1, c2, . . . , cJ} is the set of classes
        $\Omega$ = {ω1,ω2, . . . ,ωK} is the set of clusters
        
    - ‫اگه هر داکیومنت رو تو یه کلاستر بذاریم به purity یک می‌رسیم!
        - we cannot use purity to trade off the quality of the clustering against the number of clusters
2. **Normalized mutual information (NMI)**
    
    ![Screen Shot 2022-01-11 at 12.14.31.png](Chapter%2016%20-%20Flat%20Clustering%20405f3b20520d40b1a6c695193ec958d4/Screen_Shot_2022-01-11_at_12.14.31.png)
    
    - I is mutual information (MI):
        
        ![Screen Shot 2022-01-11 at 12.14.47.png](Chapter%2016%20-%20Flat%20Clustering%20405f3b20520d40b1a6c695193ec958d4/Screen_Shot_2022-01-11_at_12.14.47.png)
        
    - H is entropy:
        
        ![Screen Shot 2022-01-11 at 12.14.55.png](Chapter%2016%20-%20Flat%20Clustering%20405f3b20520d40b1a6c695193ec958d4/Screen_Shot_2022-01-11_at_12.14.55.png)
        
    - I(W;C) measures the amount of information by which our knowledge about the classes increases when we are told what the clusters are.
    - MI has the same problem as purity: a clustering with K = N has maximum MI
        - the denominator fix this problem → entropy tends to increase with the number of clusters
    - NMI is normalized, we can use it to compare clusterings with different numbers of clusters
    1. **Rand Index**
        
        ![Screen Shot 2022-01-11 at 12.22.50.png](Chapter%2016%20-%20Flat%20Clustering%20405f3b20520d40b1a6c695193ec958d4/Screen_Shot_2022-01-11_at_12.22.50.png)
        
        TP: two similar documents to the same cluster
        
        TN: two dissimilar documents to different clusters
        
        FP: two dissimilar documents to the same cluster
        
        FN: two similar documents to different clusters.
        
        - gives equal weight to false positives and false negatives.
    2. **F measure**
        - Separating similar documents is sometimes worse than putting pairs of dissimilar documents in the same cluster 
        → selecting a value β > 1 in F measure 
        → penalize false negatives more strongly than false positives
        
        ![Screen Shot 2022-01-11 at 12.26.19.png](Chapter%2016%20-%20Flat%20Clustering%20405f3b20520d40b1a6c695193ec958d4/Screen_Shot_2022-01-11_at_12.26.19.png)
        

## K-means

- objective: minimize the average squared Euclidean distance of documents from their cluster centers 
**cluster center:** mean or centroid μ of the documents in a cluster ω
    
    ![Screen Shot 2022-01-11 at 12.32.00.png](Chapter%2016%20-%20Flat%20Clustering%20405f3b20520d40b1a6c695193ec958d4/Screen_Shot_2022-01-11_at_12.32.00.png)
    
- assumes that documents are represented as length-normalized vectors in a real-valued space
- ideal cluster in K-means is a sphere with the centroid as its center of gravity. Ideally, the clusters should not overlap.

**residual sum of squares or RSS:**

A measure of how well the centroids represent the members of their clusters

![Screen Shot 2022-01-11 at 12.34.21.png](Chapter%2016%20-%20Flat%20Clustering%20405f3b20520d40b1a6c695193ec958d4/Screen_Shot_2022-01-11_at_12.34.21.png)

- objective function in K-means → RSS

**Algorithm**

1. select as initial cluster centers K randomly selected documents, **the seeds**.
2. repeat iteratively:
    1. **reassignment step:** reassigning documents to the cluster with the closest centroid
    2. **recompution step:** recomputing each centroid based on the current members of its cluster.

Termination condition can be one of:

- fixed number of iterations I
- No change in assignment of documents to clusters
- Centroids μ_k do not change between iterations
- RSS falls below a threshold
- the decrease in RSS falls below a threshold θ

**Convergence**

prove that RSS monotonically decreases in each iteration:

(monotonic: یکنوا)

- RSS decreases in the reassignment step since each vector is assigned to the closest centroid
- it decreases in the recomputation step because the new centroid is the vector v for which RSS_k reaches its minimum (مشتق رو صفر می‌ذاریم)

There is no guarantee that a global minimum in the objective function will be reached.

**outlier:** documents that are far from any other documents and therefore do not fit well into any cluster 

If an outlier is chosen as an initial seed it will result intot **singleton cluster** (a cluster with only one document)

**Seed selection**

1. excluding outliers from the seed set 
2. trying out multiple starting points and choosing the clustering with lowest cost
3. obtaining seeds from another method such as hierarchical clustering
4. compute seeds that are not selected from the vectors to be clustered:
    - e.x. select i random vectors for each cluster and use their centroid as the seed for this cluster

**Time Complexity**

computing one vector distance: $\Theta(M)$

reassignment step: KN distances → $\Theta(KNM)$

recomputation step: $\Theta(NM)$

Total for I iteration: $\Theta(IKNM)$

- K-means is linear in all relevant factors: iterations, number of clusters, number of vectors and dimensionality of the space.
- M usually is large → computing the distance is costly
    - Computing the distance between two documents is OK since their vectors are sparse
    - Centroids, however, are dense.
    - solutions:
        - Truncating centroids to the most significant k terms (e.g., k = 1000)
        - use **Medoids** instead of centroids:
            - **K-medoids algorithm**
            - medoid of a cluster: the document vector that is closest to the centroid.

### Cluster cardinality in K-means

how to find a good k:

- Use the value of K that minimizes RSS → fail since k=N is optimal
    - به جاش بیایم این کار رو کنیم:
    ‫ به ازای هر K بیایم i بار کلاستر کنیم و مینیمم مقدار RSS برای این‌ها رو بگیریم و نمودارش رو بکشیم.
    ‫اون نقطه‌هایی که شیب نمودار می‌شکنه (knee) بشن نقاط منتخبمون.
- imposes a penalty for each new cluster
    - generalized objective function that combines two elements:
        - **distortion:** a measure of how much documents deviate from the prototype of their clusters (e.g., RSS for K-means)
        - **model complexity** (e.g., the number of clusters)
    - for K-means:
        
        ![Screen Shot 2022-01-11 at 13.43.33.png](Chapter%2016%20-%20Flat%20Clustering%20405f3b20520d40b1a6c695193ec958d4/Screen_Shot_2022-01-11_at_13.43.33.png)
        
    - **Akaike Information Criterion (AIC)**
        
        ![Screen Shot 2022-01-11 at 13.45.05.png](Chapter%2016%20-%20Flat%20Clustering%20405f3b20520d40b1a6c695193ec958d4/Screen_Shot_2022-01-11_at_13.45.05.png)
        
        - −L(K), the negative maximum log-likelihood of the data for K clusters → measure of distortion
        - q(K), the number of parameters of a model with K clusters → measure of model complexity
        - Using this for k-means we will have above equation wiith lambda = 2M:
            
            ![Screen Shot 2022-01-11 at 13.48.04.png](Chapter%2016%20-%20Flat%20Clustering%20405f3b20520d40b1a6c695193ec958d4/Screen_Shot_2022-01-11_at_13.48.04.png)
            

## Model-based clustering

Model-based clustering assumes that the data were generated by a model and tries to recover the original model.

commonly used criterion for estimating the model parameters → maximum likelihood

in K-means: maximum likelihood and minimal RSS are equivalent criteria.

THIS SECTION IS NOT COMPLETE!
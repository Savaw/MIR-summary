# Chapter 18 - Matrix decompositions and latent semantic indexing

*Recap -* **term-document matrix:** rows represents a term, columns represents a document

## Linear algebra review

**rank of a matrix:** the number of linearly independent rows (or columns) in it

**diagonal matrix:** r*r matrix all of whose off-diagonal entries are zero

- it’s rank is equal to the number of non-zero diagonal entries

**identity matrix:** diagonal matrix that all diagonal entries are 1 → $I_r$

**eigenvalues** of a square M× M matrix C: all λ that satisfy in $C\vec{x}=\lambda\vec{x}$

- corresponding x is **right eigenvector**
- eigenvector corresponding to the eigenvalue of largest magnitude is called the **principal
eigenvector**.
- vectors y such that  $\vec{y}^TC=\lambda\vec{y}^T$ are **left eigenvectors**
- The number of non-zero eigenvalues of C is at most rank(C).

**characteristic equation**: $(C-\lambda I_M)\vec{x}=0$

- The eigenvalues of C are then the solutions of  $|(C-\lambda I_M)|=0$
    - |S| denotes the determinant of a square matrix S

Some other notes:

- The effect of small eigenvalues (and their eigenvectors) on a matrix-vector product is small.
- For a symmetric matrix S, the eigenvectors corresponding to distinct eigenvalues are orthogonal.

## Matrix decompositions

**matrix decomposition**: The process in which a square matrix can be factored into the product of matrices derived from its eigenvectors.

### Matrix diagonalization theorem

Let S be a square real-valued M × M matrix with M linearly independent eigenvectors. Then there exists an eigen decomposition

$S = U\Lambda U^{−1}$

where the columns of U are the eigenvectors of S and $\Lambda$ is a diagonal matrix whose diagonal entries are the eigenvalues of S in decreasing order.

![Screen Shot 2022-01-12 at 10.55.28.png](Chapter%2018%20-%20Matrix%20decompositions%20and%20latent%20sema%203a44e6ed189e4574ad34b43818318622/Screen_Shot_2022-01-12_at_10.55.28.png)

### Symmetric diagonalization theorem

Let S be a square, symmetric real-valued M× M matrix with M linearly independent eigenvectors. Then there exists a symmetric diagonal decomposition

$S = Q\Lambda Q^{−1}$

where the columns of Q are the orthogonal and normalized (unit length, real) eigenvectors of S, and $\Lambda$ is the diagonal matrix whose entries are the eigenvalues of S.
Further, all entries of Q are real and we have $Q^T=Q^{-1}$

## Term-document matrices and singular value decompositions

### singular value decomposition (SVD)

Let r be the rank of the M× N matrix C. Then, there is a singular value decomposition (SVD) of C of the form

$C = U\Sigma V^{T}$

where

1.  The eigenvalues λ1, . . . , λr of $CC^T$ are the same as the eigenvalues of $C^TC$
2. For 1 ≤ i ≤ r, let $\sigma_i = \sqrt{\lambda{i}}$, with λi ≥ λi+1. Then the M × N matrix $\Sigma$ is composed by setting $\Sigma_{ii} = \sigma_i$ for 1 ≤ i ≤ r, and zero otherwise.

The values σi are referred to as the **singular values** of C.

**reduced SVD** or **truncated SVD:** represent $\Sigma$ as an r × r matrix with the singular values on the diagonals and omit the rightmost M − r columns of U and the rightmost N − r columns of V corresponding to these omitted rows of $\Sigma$.

## Low-rank approximations

**Frobenius norm** of matrix X**:**

![Screen Shot 2022-01-12 at 11.49.54.png](Chapter%2018%20-%20Matrix%20decompositions%20and%20latent%20sema%203a44e6ed189e4574ad34b43818318622/Screen_Shot_2022-01-12_at_11.49.54.png)

- measures the discrepancy

Problem: Given an M × N matrix C and a positive integer k, we wish to find an  M × N matrix C_k of rank at most k, so as to minimize the Frobenius norm of the matrix difference X = C − C_k

- if k = rank of C then C_k = C and Frobenius norm will be zero
- else C_k → **low-rank approximation**

Solution using SVD:

1. Given C, construct its SVD in the form $C = U\Sigma V^{T}$
2. Derive from $\Sigma$ the matrix $\Sigma_k$ formed by replacing by zeros the r − k smallest singular values on the diagonal of $\Sigma$. 
3. Compute and output$C_k = U\Sigma_k V^{T}$ as the rank-k approximation to C.

This procedure yields the matrix of rank k with the lowest possible Frobenius error, proved by following theorem:

![Screen Shot 2022-01-12 at 11.58.09.png](Chapter%2018%20-%20Matrix%20decompositions%20and%20latent%20sema%203a44e6ed189e4574ad34b43818318622/Screen_Shot_2022-01-12_at_11.58.09.png)

- Consider the case where C is the term-document incidence matrix. Then the entry (i, j) in CC^T is the number of documents in which both term i and term j occur.

## Latent semantic indexing (LSI)

aka: **latent semantic analysis (LSA)**

vector space representation suffer from two problems in natural languages:

- synonymy: two different words have the same meaning → underestimate similarity between query q and document d
- polysemy: a term has multiple meanings → overestimate similarity between query q and document d

→ we can use the co-occurrences of terms to capture the latent semantic associations of terms and alleviate these problems.

**Latent semantic indexing**:

We use the SVD to construct a low-rank approximation C_k to the term-document matrix, for a value of k that is far smaller than the original rank of C. We thus map each row/column (respectively corresponding to a term/document) to a k-dimensional space; this space is defined by the k principal eigenvectors (corresponding to the largest eigenvalues) of CC^T and C^TC. 

Then, we use the new k-dimensional LSI representation as we did the original representation – to compute similarities between vectors.

A query vector q is mapped into its representation in the LSI space by the transformation:

![Screen Shot 2022-01-12 at 12.18.14.png](Chapter%2018%20-%20Matrix%20decompositions%20and%20latent%20sema%203a44e6ed189e4574ad34b43818318622/Screen_Shot_2022-01-12_at_12.18.14.png)

- We may in general view the low-rank approximation of C by Ck as a **constrained optimization** problem
- the SVD should bring together terms with similar co-occurrences
- computational cost of the SVD is significant
- As we reduce k, recall tends to increase
- a value of k in the low hundreds can actually increase precision on some query benchmarks
- LSI works best in applications where there is little overlap between queries and documents.
- LSI can be viewed as **soft clustering** by interpreting each dimension of the reduced space as a cluster and the value that a document has on that dimension as its fractional membership in that cluster.

two basic drawbacks:

- no good way of expressing negations (find documents that contain german but not shepherd)
- no way of enforcing Boolean conditions
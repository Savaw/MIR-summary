# Chapter 14 - Vector space classification

In this chapter we adopt the vector space model representation for text classification.

**Contiguity hypothesis:** Documents in the same class form a contiguous region and regions of different classes do not overlap.

Whether or not a set of documents is mapped into a contiguous region depends on the particular choices we make for the document representation: type of weighting, stop list etc.

## Rocchio classification

**decision boundaries:** the boundaries that separate classes

Rocchio uses centroids to define the boundaries.

**Centroid** of a class c is computed as the vector average or center of mass of its members:

![Screen Shot 2022-01-09 at 18.24.33.png](Chapter%2014%20-%20Vector%20space%20classification%20b9d257ec7a8d4a18bf5dbe242634d267/Screen_Shot_2022-01-09_at_18.24.33.png)

D_c: set of documents in D whose class is c

v(d): normalized vector of d

The boundary between two classes in Rocchio classification is the set of points with equal distance from the two centroids.

This set of points is always a line. The generalization of a line in M-dimensional space is a **hyperplane**, which we define as the set of points x that satisfy:

![Screen Shot 2022-01-09 at 18.27.06.png](Chapter%2014%20-%20Vector%20space%20classification%20b9d257ec7a8d4a18bf5dbe242634d267/Screen_Shot_2022-01-09_at_18.27.06.png)

w: M-dimensional normal vector of the hyperplane

b: a constant

classification rule: 
****classify a point in accordance with the region it falls into 
*or Equivalently* 
we determine the centroid μ(c) that the point is closest to and then assign it to c.

assignment criterion can be:

- Euclidean distance
- cosine similarity

The classes in Rocchio classification must be approximate spheres with similar radii:

- it ignores details of the distribution of points in a class
- Rocchio often misclassifies this type of **multimodal class**
    - multimodality example: a country than its name has changed
- Two-class classification (example: china or non-china) is rarely distributed like spheres
    - the need a modified decision rule of the form:
        
        ![Screen Shot 2022-01-09 at 18.38.42.png](Chapter%2014%20-%20Vector%20space%20classification%20b9d257ec7a8d4a18bf5dbe242634d267/Screen_Shot_2022-01-09_at_18.38.42.png)
        
    - or simplified to:
        
        ![Screen Shot 2022-01-09 at 18.39.23.png](Chapter%2014%20-%20Vector%20space%20classification%20b9d257ec7a8d4a18bf5dbe242634d267/Screen_Shot_2022-01-09_at_18.39.23.png)
        

**complexity:**

total training time: $\Theta(|D|L_{ave}+|C||V|)$

total test time: $\Theta(L_{a}+|C|M_a) = \Theta(|C|M_a)$ 

L_a: the numbers of tokens in test data

M_a: the numbers of types in test data

## k nearest neighbor (kNN)

- memory-based learning or instance-based learning

we assign each document to the majority class of its k closest neighbors where k is a parameter.

Decision boundaries in 1NN → concatenated segments of the Voronoi tessellation

**Voronoi tessellation** of a set of objects decomposes space into Voronoi cells, where each object’s cell consists of all points that are closer to the object than to other objects.

We can also weight the votes from neighbors by their cosine similarity:

![Screen Shot 2022-01-09 at 19.41.34.png](Chapter%2014%20-%20Vector%20space%20classification%20b9d257ec7a8d4a18bf5dbe242634d267/Screen_Shot_2022-01-09_at_19.41.34.png)

Sk(d) is the set of d’s k nearest neighbors and Ic(d′) = 1 iff d′ is in class c and 0 otherwise.

**Time complexity and optimality**

- with preprocessing:
    - total training time: $\Theta(|D|L_{ave})$
    - total test time: $\Theta(L_{a}+|C|M_{ave}M_a) = \Theta(|C|M_{ave}M_a)$
- without preprocessing:
    - total training time: $\Theta(1)$
    - total test time: $\Theta(L_{a}+|C|L_{ave}M_a) = \Theta(|C|L_{ave}M_a)$
    
- Test time is independent of the number of classes J → good for problems with large J
- with short documents and a large stop list, an inverted index may well cut the average test time by a factor of 10 or more.
- kNN is not optimal for problems with a non-zero **Bayes error rate** (the average error rate of classifiers learned by it for a particular problem)
    - the overall error of 1NN is twice the optimal error rate. For problems with Bayes error rate 0, the error rate of 1NN will approach 0 as the size of the training set increases.

## Linear versus nonlinear classifiers

**linear classifier:** a two-class classifier that decides class membership by comparing a linear combination of the features to a threshold.

![Screen Shot 2022-01-09 at 18.27.06.png](Chapter%2014%20-%20Vector%20space%20classification%20b9d257ec7a8d4a18bf5dbe242634d267/Screen_Shot_2022-01-09_at_18.27.06.png)

- Rocchio and Naive Bayes are linear classifiers
- Rocchio:
    - decision boundary:
        
        ![Screen Shot 2022-01-09 at 20.28.03.png](Chapter%2014%20-%20Vector%20space%20classification%20b9d257ec7a8d4a18bf5dbe242634d267/Screen_Shot_2022-01-09_at_20.28.03.png)
        
    - linear classifier:
        
        ![Screen Shot 2022-01-09 at 20.27.15.png](Chapter%2014%20-%20Vector%20space%20classification%20b9d257ec7a8d4a18bf5dbe242634d267/Screen_Shot_2022-01-09_at_20.27.15.png)
        
- NB:
    - decision rule:
        
        ![Screen Shot 2022-01-09 at 20.28.51.png](Chapter%2014%20-%20Vector%20space%20classification%20b9d257ec7a8d4a18bf5dbe242634d267/Screen_Shot_2022-01-09_at_20.28.51.png)
        
    - log odds:
        
        ![Screen Shot 2022-01-09 at 20.30.01.png](Chapter%2014%20-%20Vector%20space%20classification%20b9d257ec7a8d4a18bf5dbe242634d267/Screen_Shot_2022-01-09_at_20.30.01.png)
        
    - linear classifier:
        
        ![Screen Shot 2022-01-09 at 20.31.09.png](Chapter%2014%20-%20Vector%20space%20classification%20b9d257ec7a8d4a18bf5dbe242634d267/Screen_Shot_2022-01-09_at_20.31.09.png)
        
    

If there exists a hyperplane that perfectly separates the two classes, then
we call the two classes **linearly separable**.

- KNN is nonlinear.

## Classification with more than two classes

**any-of classification**: ****Classification for classes that are not mutually exclusive

- also called: **multilabel**, or **multivalue** classification:
- to solve:
    1. Build a classifier for each class
    2. apply each classifier separately

**one-of classification**: Classification for classes that are mutually exclusive

- also called: **multinomial**, **polytomous**, **multiclass**, or **single-label** classification.
- kNN is a (nonlinear) one-of classifier
- one-of classification with linear classifiers:
    1. Build a classifier for each class
    2. apply each classifier separately
    3. Assign the document to the class with
    • the maximum score,
    • the maximum confidence value,
    • or the maximum probability

tool for analyzing: 

**confusion matrix:** The confusion matrix shows for each pair of classes <c1, c2>, how many documents from c1 were incorrectly assigned to c2.

## bias-variance tradeoff

- Nonlinear classifiers are more powerful than linear classifiers
- But, there is no universally optimal learning method

We will look at how well the classifier estimates the conditional probability P(c|d) of a document being in a class. Our goal in text classification then is to find a classifier γ such that, averaged
over documents d, γ(d) is as close as possible to the true probability P(c|d).

mean squared error:

![Screen Shot 2022-01-10 at 17.16.13.png](Chapter%2014%20-%20Vector%20space%20classification%20b9d257ec7a8d4a18bf5dbe242634d267/Screen_Shot_2022-01-10_at_17.16.13.png)

E_d is the expectation with respect to P(d).

**optimal classifier** for a distribution: minimize MSE

recall: we defined a learning method $\Gamma$ as a function that takes a labeled training set D as input and returns a classifier γ

**learning error:** 

![Screen Shot 2022-01-10 at 17.21.35.png](Chapter%2014%20-%20Vector%20space%20classification%20b9d257ec7a8d4a18bf5dbe242634d267/Screen_Shot_2022-01-10_at_17.21.35.png)

E_D is the expectation over labeled training sets

**optimal learning method**: minimizes the learning error

better readability:

![Screen Shot 2022-01-10 at 17.23.24.png](Chapter%2014%20-%20Vector%20space%20classification%20b9d257ec7a8d4a18bf5dbe242634d267/Screen_Shot_2022-01-10_at_17.23.24.png)

![Screen Shot 2022-01-10 at 17.23.51.png](Chapter%2014%20-%20Vector%20space%20classification%20b9d257ec7a8d4a18bf5dbe242634d267/Screen_Shot_2022-01-10_at_17.23.51.png)

Note that d and D are independent of each
other. In general, for a random document d and a random training set D, D
does not contain a labeled instance of d.

**Bias:** Bias is the squared difference between P(c|d), the true conditional probability
of d being in c, and $\Gamma_D$(d), the prediction of the learned classifier, averaged over training sets.

- Bias is large if the learning method produces classifiers that are consistently wrong.
- Bias is small if
    1.  the classifiers are consistently right
    2. different training sets cause errors on different documents
    3. different training sets cause positive and negative errors on the same documents, but that average out to close to 0.
- Linear methods like Rocchio and Naive Bayes have a high bias for nonlinear problems

**Variance:** Variance is the variation of the prediction of learned classifiers, the average squared difference between  $\Gamma_D$(d) and its average  $E_D\Gamma_D$(d).

- Variance is large if different training sets D give rise to very different classifiers $\Gamma_D$.
- It is small if the training set has a minor effect on the classification decisions  $\Gamma_D$
makes, be they correct or incorrect.
- Variance measures how inconsistent the decisions are, not whether they are correct or incorrect.
- Linear learning methods have low variance
- Nonlinear methods like kNN have high variance → can model very complex boundaries
- High-variance learning methods are prone to **overfitting**  → overfitting increase MSE
    - In overfitting, the learning method also learns from noise.
- we can also think of variance as the **model complexity** or, equivalently, **memory capacity** of the learning method – how detailed a characterization of the training set it can remember and then apply to new data.
    - kNN’s capacity is only limited by the size of the training set
    - In contrast, the number of parameters of Rocchio is fixed – J parameters per dimension, one for each centroid – and independent of the size of the training set.

**bias-variance tradeoff:** 

When comparing two learning methods G1 and G2, in most cases the comparison comes down to one method having higher bias and lower variance and the other lower bias and higher variance. 

The decision for one learning method vs. another is then not simply a matter of selecting the one that reliably produces good classifiers across training sets (small variance) or the one that can learn classification problems with very difficult decision boundaries (small bias). Instead, we have to weigh the respective merits of bias and variance in our application and choose accordingly. This tradeoff is called the bias-variance tradeoff.
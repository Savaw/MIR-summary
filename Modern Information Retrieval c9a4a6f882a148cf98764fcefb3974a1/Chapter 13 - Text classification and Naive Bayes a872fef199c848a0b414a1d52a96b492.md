# Chapter 13 - Text classification and Naive Bayes

**Standing Query:** A standing query is like any other query except that it is periodically executed on a collection to which new documents are incrementally added over time.

**Classification:** Given a set of classes, we seek to determine which class(es) a given object belongs to.

Classification using standing queries is also called **routing** or **filtering.**

general classes in IR are usually referred to as **topics → text classification, text categorization, topic classification, or topic spotting.**

classification examples in information retrieval:

- preprocessing steps: document’s encoding, word segmentation, truecasing, identifying the language
- spam pages
- sexually explicit content
- **Sentiment detection** (classify review as positive or negative)
- email sorting
- Topic-specific or **vertical search** (Vertical search engines restrict searches to a particular topic.)
- ranking function in ad hoc information retrieval

classification types:

- manual
- hand-crafted rules
- machine learning-based → statistical text classification

## The text classification problem

![Screen Shot 2022-01-08 at 16.41.29.png](Chapter%2013%20-%20Text%20classification%20and%20Naive%20Bayes%20a872fef199c848a0b414a1d52a96b492/Screen_Shot_2022-01-08_at_16.41.29.png)

- supervised
- We denote the supervised learning method by $\Gamma$ and write $\Gamma$(D) = γ. The learning method $\Gamma$ takes the training set D as input and returns the learned classification function γ.
- **one-of** problem: each object belong to one class
- **any-of** problem: object is not restricted to belong to one class

## **Naive Bayes text classification**

- multinomial Naive Bayes or multinomial NB model, a probabilistic learning method

probability of a document d being in class c:

![Screen Shot 2022-01-08 at 16.50.55.png](Chapter%2013%20-%20Text%20classification%20and%20Naive%20Bayes%20a872fef199c848a0b414a1d52a96b492/Screen_Shot_2022-01-08_at_16.50.55.png)

P(t_k|c) is the conditional probability of term t_k occurring in a document of class c.

t_1, t_2, . . . , t_n_d are the tokens in d that are part of the vocabulary we use for classification

best class is the most likely or maximum a posteriori (MAP):

![Screen Shot 2022-01-08 at 16.53.46.png](Chapter%2013%20-%20Text%20classification%20and%20Naive%20Bayes%20a872fef199c848a0b414a1d52a96b492/Screen_Shot_2022-01-08_at_16.53.46.png)

- since there can be floating point underflow, we will use logarithm

Estimates for P(c) and P(t_k|c):

![Screen Shot 2022-01-08 at 16.57.21.png](Chapter%2013%20-%20Text%20classification%20and%20Naive%20Bayes%20a872fef199c848a0b414a1d52a96b492/Screen_Shot_2022-01-08_at_16.57.21.png)

![Screen Shot 2022-01-08 at 16.58.05.png](Chapter%2013%20-%20Text%20classification%20and%20Naive%20Bayes%20a872fef199c848a0b414a1d52a96b492/Screen_Shot_2022-01-08_at_16.58.05.png)

N_c: the number of documents in class c

N: the total number of documents

T_ct: the number of occurrences of t in training documents from class c

- We have made the positional independence assumption here.

problem: zero probability - reason: sparseness of training data

solution: smoothing

**add-one or Laplace smoothing:**

![Screen Shot 2022-01-08 at 17.03.04.png](Chapter%2013%20-%20Text%20classification%20and%20Naive%20Bayes%20a872fef199c848a0b414a1d52a96b492/Screen_Shot_2022-01-08_at_17.03.04.png)

- it can be interpreted as a uniform prior (each term occurs once for each class)

**time complexity of NB:**

complexity of computing the parameters: O(|C||V|) → the set of parameters consists of |C||V| conditional probabilities and |C| priors

preprocessing necessary for computing the parameters: Q(|D|L_ave) → one pass through the training data (L_ave is the average length of a document.)

total training time: $\Theta(|D|L_{ave}+|C||V|)$

total test time: $\Theta(L_{a}+|C|M_a) = \Theta(|C|M_a)$ 

L_a: the numbers of tokens in test data

M_a: the numbers of types in test data

→ both training and testing complexity are linear in the time it takes to scan the data since usually $|C||V| < |D|L_{ave}$ → NB is optimal

## The Bernoulli model

- multivariate Bernoulli model
- compare with multinomial NB:
    - The Bernoulli model estimates ˆP(t|c) as the fraction of documents of class c that contain term t
    - The multinomial model estimates ˆP (t|c) as the fraction of tokens or fraction of positions in documents of class c that contain term t
    - the Bernoulli model uses binary occurrence information, ignoring the number of occurrences, whereas the multinomial model keeps track of multiple occurrences.
- not good for long documents

## Properties of Naive Bayes

blah blah ...

assumptions:

- conditional independence
- positional independence

- optimal classifier (in the sense of minimal error rate on new data) for data where the independence assumptions do hold.

Q: How can NB be a good text classifier when its model of natural language is so oversimplified?
A: The answer is that even though the probability estimates of NB are of low quality, its classification decisions are surprisingly good.
The winning class in NB classification usually has a much larger probability than the other classes and the estimates diverge very significantly from the true probabilities. But the classification decision is based on which class gets the highest score. It does not matter how accurate the estimates are. Correct estimation implies accurate prediction, but accurate prediction does not imply correct estimation. NB classifiers estimate badly, but often classify well.

other pros of NB:

- excels if there are many equally important features that jointly contribute to the classification
decision
- somewhat robust to **noise features** and **concept drift** – the gradual change over time of the concept underlying a class like US president from Bill Clinton to George W. Bush
- main strength is its efficiency → one pass over data

when to choose:

- squeezing out a few extra percentage
points of accuracy is not worth the trouble in a text classification application
- a very large amount of training data is available and there is more to be
gained from training on a lot of data than using a better classifier on a smaller
training set
- if its robustness to concept drift can be exploited (=used)

## Feature selection

Feature selection is the process of selecting a subset of the terms occurring in the training set and using only this subset as features in text classification.

pros:

- more efficient by decreasing the size of the effective vocabulary
- increases classification accuracy by eliminating noise features

**noise feature**: is one that, when added to the document representation, increases the classification error on new data.

### Mutual information

MI measures how much information the presence/absence of a term contributes to making the correct classification decision on c.

![Screen Shot 2022-01-08 at 23.45.22.png](Chapter%2013%20-%20Text%20classification%20and%20Naive%20Bayes%20a872fef199c848a0b414a1d52a96b492/Screen_Shot_2022-01-08_at_23.45.22.png)

U is a random variable that takes values e_t = 1 (the document contains term t) and e_t = 0 (the document does not contain t)

C is a random variable that takes values e_c = 1 (the document is in class
c) and e_c = 0 (the document is not in class c)

for feature selection using MI we compute the utility measure as A(t, c) = I(U_t, C_c) and select the k terms with the largest values.

### $\Chi^2$  Feature selection

χ2 test is applied to test the independence of two events, where two events A and B are defined to be independent if P(AB) = P(A)P(B).

In feature selection, the two events are occurrence of the term and occurrence of the class.

We rank terms with respect to the following quantity:

![Screen Shot 2022-01-08 at 23.53.27.png](Chapter%2013%20-%20Text%20classification%20and%20Naive%20Bayes%20a872fef199c848a0b414a1d52a96b492/Screen_Shot_2022-01-08_at_23.53.27.png)

N is the observed frequency in D

E the expected frequency of t and c occurring together in a document assuming that term and class are independent.

- χ2 is a measure of how much expected counts E and observed counts N deviate from each other. A high value of X2 indicates that the hypothesis of independence, which implies that expected and observed counts are similar, is incorrect.

### Frequency-based feature selection

selecting the terms that are most common in the class.

- Frequency can be either defined as document frequency (the number of documents in the class c that contain the term t) or as collection frequency (the number of tokens of t that occur in documents in c).
- Document frequency is more appropriate for the Bernoulli model,
collection frequency for the multinomial model

## Evaluation of text classification

We will use **effectiveness** as a generic term for measures that evaluate the quality of classification decisions, including precision, recall, F1, and accuracy.
**Performance** refers to the computational efficiency of classification and IR systems in this book.

**Macroaveraging:** computes a simple aver age over classes.

**Microaveraging:** pools per-document decisions across classes, and then computes an effectiveness measure on the pooled contingency table.

- Macroaveraging gives equal weight to each class, where as microaveraging gives equal weight to each per-document classification decision.
- large classes dominate small classes in microaveraging → Because the F1 measure ignores
true negatives and its magnitude is mostly determined by the number of true positives
- To get a sense of effectiveness on small classes → compute macroaveraged results
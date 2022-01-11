# Chapter 11 - Probabilistic information retrieval

information needs → query reperestations

documents → document representations

Based on these two representations, a system tries to determine how well documents satisfy information needs.

## Review of basic probability theory

**chain rule:**

$P(A, B) = P(A ∩ B) = P(A|B)P(B) = P(B|A)P(A)$

**complementary:**

$P(\bar{A}, B) = P(B|\bar{A})P(\bar{A})$

$P(B) = P(B,\bar{A}) +  P(B,A)$

**Bayes’ Rule:**

$P(A|B) = \dfrac{P(B|A)P(A)}{P(B)}$

**odds of an event:**

$O(A) = \dfrac{P(A)}{P(\bar A)} = \dfrac{P(A)}{1 - P(A)}$

## The Probability Ranking Principle

$R: R_{d,q}$ : an indicator random variable that says whether d is relevant with respect to a given query

The obvious order in which to present documents to the user is to rank documents by their estimated probability of relevance with respect to the information need: P(R = 1|d, q).

→ **Probability Ranking Principle (PRP)**

**1/0 loss:** a binary situation where you are evaluated on your accuracy - You lose a point for either returning a nonrelevant document or failing to return a relevant document.

**Bayes Optimal Decision Rule:** the decision which minimizes the risk of loss, is to simply return documents that are more likely relevant than nonrelevant: (defined for a set not ordering)

d is relevant iff P(R = 1|d, q) > P(R = 0|d, q)

**Bayes Risk:** expected loss
→ The PRP is optimal, in the sense that it minimizes the expected loss (Bayes risk) under 1/0 loss

BUT it requires that all probabilities are known correctly which is usually not the case.

**The PRP with retrieval costs**

C1: cost of not retrieving a relevant document

C0: the cost of retrieval of a nonrelevant document

PRP says if for a specific document d and for all documents d′ not yet retrieved we have:

$C_0.P(R=0|d) - C_1.P(R=1|d) \le C_0.P(R=0|d') - C_1.P(R=1|d')$

then d is the next document to be retrieved.

## The Binary Independence Model (BIM)

Documents and queries are both represented as binary term incidence vectors:

A document d is represented by the vector $\vec x = (x_1, ..., x_M)$ where $x_t$ = 1 if term t is
present in document.

"Independence" means that terms are modeled as occurring in documents independently. The model recognizes no association between terms. This assumption is far from correct, but it nevertheless often gives satisfactory results in practice; it is the “naive” assumption of Naive Bayes models.

![Screen Shot 2021-12-14 at 17.58.42.png](Chapter%2011%20-%20Probabilistic%20information%20retrieval%201862234deabf409db588da5a9b45e7f9/Screen_Shot_2021-12-14_at_17.58.42.png)

P(x|R = 1,q) and P(x|R = 0,q) → the probability that if a relevant or nonrelevant document is retrieved

x → document representation

P(R = 1|q) and P(R = 0|q) → prior probability of retrieving a relevant or nonrelevant document respectively for a query q (estimate: percentage of relevant documents in the collection)

### Ranking function:

We wish to order returned documents by descending P(R =1|d, q). Under the BIM, this is modeled as ordering by P(R = 1|x,q).

Since only order is important, we can use ods:

![Screen Shot 2021-12-14 at 18.04.52.png](Chapter%2011%20-%20Probabilistic%20information%20retrieval%201862234deabf409db588da5a9b45e7f9/Screen_Shot_2021-12-14_at_18.04.52.png)

P(r=1|q)/P(r=0|q) is constant for a given query and we only need to use right-hand term.

Assumption 1: **Naive Bayes conditional independence assumption -** that the presence or absence of a word in a document is independent of the presence or absence of any other word (given the query)

![Screen Shot 2021-12-14 at 19.49.53.png](Chapter%2011%20-%20Probabilistic%20information%20retrieval%201862234deabf409db588da5a9b45e7f9/Screen_Shot_2021-12-14_at_19.49.53.png)

p_t = P(x_t = 1|R = 1, q) → the probability of a term appearing in a document relevant to the query, u_t = P(x_t = 1|R = 0, q) → the probability of a term appearing in a nonrelevant document

![Screen Shot 2021-12-14 at 20.15.10.png](Chapter%2011%20-%20Probabilistic%20information%20retrieval%201862234deabf409db588da5a9b45e7f9/Screen_Shot_2021-12-14_at_20.15.10.png)

Assumption 2: terms not occurring in the query are equally likely to occur in relevant and nonrelevant documents: that is, if q_t = 0 then p_t = u_t.

Then we need only consider terms in the products that appear in the query:

![Screen Shot 2021-12-14 at 20.16.52.png](Chapter%2011%20-%20Probabilistic%20information%20retrieval%201862234deabf409db588da5a9b45e7f9/Screen_Shot_2021-12-14_at_20.16.52.png)

![Screen Shot 2021-12-14 at 20.19.22.png](Chapter%2011%20-%20Probabilistic%20information%20retrieval%201862234deabf409db588da5a9b45e7f9/Screen_Shot_2021-12-14_at_20.19.22.png)

The resulting quantity used for ranking:

### Retrieval Status Value (RSV)

![Screen Shot 2021-12-14 at 20.20.53.png](Chapter%2011%20-%20Probabilistic%20information%20retrieval%201862234deabf409db588da5a9b45e7f9/Screen_Shot_2021-12-14_at_20.20.53.png)

**c_t: log odds ratios** for the terms in the query

ratio of the two ods:

1. the odds of the term appearing if the document is relevant (pt/(1 − pt))
2. the odds of the term appearing if the document is nonrelevant (ut/(1 − ut)

![Screen Shot 2021-12-14 at 20.21.53.png](Chapter%2011%20-%20Probabilistic%20information%20retrieval%201862234deabf409db588da5a9b45e7f9/Screen_Shot_2021-12-14_at_20.21.53.png)

$c_t$ = 0 → equal ods

$c_t$ > 0 → higher ods to appear in relevant document

$c_t$ < 0 → higher ods to appear in nonrelevant document

The $c_t$ quantities function as term weights in the model, and the document score for a query is$RSV_d=\Sigma_{x_t=q_t=1}c_t$.

### Probability estimates in theory

![Screen Shot 2021-12-14 at 21.28.36.png](Chapter%2011%20-%20Probabilistic%20information%20retrieval%201862234deabf409db588da5a9b45e7f9/Screen_Shot_2021-12-14_at_21.28.36.png)

- To avoid the possibility of zeroes (such as if every or no relevant document has a particular term) it is fairly standard to add 12 to each of the quantities in the center 4 terms of table. → simple form of **smoothing**.

**relative frequency:** count the number of times an event occurred divided by the total number of trials.

**maximum likelihood estimate (MLE):** Estimating the probability as the relative frequency.

**smoothing:** Simultaneously decreasing the estimated probability of seen events and increasing the probability of unseen events.

### Probability estimates in practice

assumption: relevant documents are a very small percentage of the collection, it is plausible to approximate statistics for nonrelevant documents by statistics from the whole collection

Under this assumption:

- $u_t={df_t}/{N}$ (the probability of term occurrence in nonrelevant documents for a query)
- $log[(1-u_t)/u_t] = log[(N-df_t)/df_t] \approx log N/df_t$

some ways for estimate p_t:

- use the frequency of term occurrence in known relevant documents
- Croft and Harper: using a constant. For instance, we might assume that p_t is constant over all terms x_t in the query and that p_t = 0.5. This means that each term has even odds of appearing in a relevant document.
- Greiff: pt = 1/3 + 2/3.dft/N.

### Probabilistic approaches to relevance feedback

1. Guess initial estimates of pt and ut
2. Use the current estimates of pt and ut to determine a best guess at the set of relevant documents R = {d : R_d,q = 1}.
3. Interact with the user to refine the model of R:
    
    V: some subset of documents which we have user relevance judgments for.
    VR ={d ∈ V, R_d,q = 1} ⊂ R
    VNR = {d ∈ V, Rd,q = 0}
    
4. Reestimate pt and ut on the basis of known relevant and nonrelevant documents:
    
    ![Screen Shot 2021-12-14 at 21.58.46.png](Chapter%2011%20-%20Probabilistic%20information%20retrieval%201862234deabf409db588da5a9b45e7f9/Screen_Shot_2021-12-14_at_21.58.46.png)
    
    It usually needs smoothing:
    
    ![Screen Shot 2021-12-14 at 21.58.39.png](Chapter%2011%20-%20Probabilistic%20information%20retrieval%201862234deabf409db588da5a9b45e7f9/Screen_Shot_2021-12-14_at_21.58.39.png)
    
    since the set of documents judged by the user is really small it is often better to combine the new information with the original guess in a process of Bayesian updating:
    
    ![Screen Shot 2021-12-14 at 22.01.09.png](Chapter%2011%20-%20Probabilistic%20information%20retrieval%201862234deabf409db588da5a9b45e7f9/Screen_Shot_2021-12-14_at_22.01.09.png)
    
    p_t(k) is the kth estimate for p_t. 
    
    kappa = 5 is appropriate.
    
5. Repeat from step 2.

**Pseudo-relevance feedback version of this algorithm:**

- we simply pretend that VR = V.
    
    ![Screen Shot 2021-12-14 at 22.09.57.png](Chapter%2011%20-%20Probabilistic%20information%20retrieval%201862234deabf409db588da5a9b45e7f9/Screen_Shot_2021-12-14_at_22.09.57.png)
    
    V_t → the subset of documents in V containing x_t
    
    If we assume that documents that are not retrieved are nonrelevant:
    
    ![Screen Shot 2021-12-14 at 22.10.36.png](Chapter%2011%20-%20Probabilistic%20information%20retrieval%201862234deabf409db588da5a9b45e7f9/Screen_Shot_2021-12-14_at_22.10.36.png)
    
    Then we can calculate c_t:
    
    ![Screen Shot 2021-12-14 at 22.12.57.png](Chapter%2011%20-%20Probabilistic%20information%20retrieval%201862234deabf409db588da5a9b45e7f9/Screen_Shot_2021-12-14_at_22.12.57.png)
    

### Major assumptions in the BIM

- a Boolean representation of documents/queries/relevance
- term independence
- terms not in the query don’t affect the outcome
- document relevance values are independent

Some extensions of the traditional probabilistic model:

### **Tree-structured dependencies between terms**

remove the assumption that terms are independent.

→ Tree Augmented Naive Bayes model by Friedman and Goldszmidt

### Okapi BM25: a non-binary model

a probabilistic model that incorporates term frequency (i.e., it’s nonbinary) and length normalization.
**BM25 weighting scheme** or **Okapi weighting**

simplest score for document d is just idf weighting of the query terms present:

![Screen Shot 2021-12-14 at 22.41.07.png](Chapter%2011%20-%20Probabilistic%20information%20retrieval%201862234deabf409db588da5a9b45e7f9/Screen_Shot_2021-12-14_at_22.41.07.png)

improve by factoring in the frequency of each term and document length:

![Screen Shot 2021-12-14 at 22.41.49.png](Chapter%2011%20-%20Probabilistic%20information%20retrieval%201862234deabf409db588da5a9b45e7f9/Screen_Shot_2021-12-14_at_22.41.49.png)

tf_td → the frequency of term t in document d

L_d → length of document d

L_ave → the average document length for the whole collection.

k1 → a positive tuning parameter that calibrates the document term frequency scaling

b → another tuning parameter (0 ≤ b ≤ 1) which determines the scaling by document length

**For long queries:**

![Screen Shot 2021-12-14 at 22.45.02.png](Chapter%2011%20-%20Probabilistic%20information%20retrieval%201862234deabf409db588da5a9b45e7f9/Screen_Shot_2021-12-14_at_22.45.02.png)

k3 → another positive tuning parameter that this time calibrates term frequency scaling of the query

- tuning parameters should ideally be set to optimize performance on a development test collection. if not possible reasonable values k1 = 1.2 , k3 = 2, b = 0.75
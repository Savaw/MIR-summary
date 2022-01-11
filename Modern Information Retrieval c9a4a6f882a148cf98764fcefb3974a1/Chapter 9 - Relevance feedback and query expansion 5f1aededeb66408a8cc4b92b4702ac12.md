# Chapter 9 - Relevance feedback and query expansion

problem:

**synonymy:** same concept referred by different words

solution:

- global methods
    - Query expansion/reformulation with a thesaurus or WordNet
    - Query expansion via automatic thesaurus generation
    - Techniques like spelling correction
- local methods
    - Relevance feedback
    - Pseudo relevance feedback aka Blind relevance feedback
    - (Global) indirect relevance feedback

## **Relevance Feedback (RF)**

idea: involve the user in the retrieval process so as to improve the final result set

### Rocchio algorithm

A way of incorporating relevance feedback information into the vector space model.

**Underlying theory**: We want to find a **query** vector, denoted as $\vec{q}$, that
maximizes similarity with relevant documents while minimizing similarity
with nonrelevant documents. If C_r is the set of relevant documents and C_nr
is the set of nonrelevant documents, then we wish to find:

$$
q_{opt} = \argmax_{q} [sim(\vec{q}, C_r) − sim(\vec{q}, C_{nr})],
$$

*sim* is defined in section 6. (cosine similarity: dot product divided by vector lengths)

so we will have:

یعنی کوئری ایده‌آلمون می‌شه تفاوت سنتروید داکیومنت‌های مرتبط منهای سنتروید داکیومنت‌های غیرمرتبط

$$
q_{opt} =\dfrac1{|C_r|}\Sigma_{d_j \in C_r} \vec{d_j} - \dfrac1{|C_{nr}|}\Sigma_{d_j \in C_{nr}} \vec{d_j}
$$

این کوئری آپتیمم برای وقتیه که همه‌ی کوئری‌های مرتبط و همه‌ی غیر مرتبط‌ها رو بدونیم که نمی‌دونیم.

‫پس در حالتی که اطلاعات کامل نداریم در rocchio پیشنهاد می‌شه که از این کوئری استفاده بشه:

$$
\vec{q_m} = \alpha\vec{q_0} + \beta\dfrac1{|D_r|}\Sigma_{d_j \in D_r} \vec{d_j} - \gamma\dfrac1{|D_{nr}|}\Sigma_{d_j \in D_{nr}} \vec{d_j}

$$

$q_0$ → initial query vector

$q_m$ → modified query

$D_r, D_{nr}$ → set of known relevant and nonrelevant documents

- RF can improve both recall and precision but most useful for recall
- Positive feedback also turns out to be much more valuable than negative feedback, and so most IR systems set γ < β.
- **Ide dec-hi**: only consider highest rank nonrelevant document (|D_r| = 1) - the most effective or at least the most consistent performer.

### Probabilistic relevance feedback

- build a classifier

A Naive Bayes probabilistic model:
If R is a Boolean indicator variable expressing the relevance of a document, then we can estimate $P(x_t=1|R)$ the probability of a term t appearing in a document, depending on whether it is relevant or not, as:

$P(x_t = 1|R = 1) = \dfrac{|VR_t|}{|VR|}$

$P(x_t = 1|R = 1) = \dfrac{df_t-|VR_t|}{N-|VR|}$

VR is the set of known relevant documents.

(more in chapter 11)

### When does relevance feedback work?

- the user has to have sufficient knowledge
- requires relevant documents to be similar to each other: they should cluster

when is not sufficient:

- Misspellings
- Cross-language information retrieval
- Mismatch of searcher’s vocabulary versus collection vocabulary
- documents not cluster
    - Subsets of the documents using different vocabulary
    - A query for which the answer set is inherently disjunctive
    - Instances of a general concept

problems with users:

- Users are often reluctant to provide explicit feedback
- do not wish to prolong the search interaction

practical problems: 

- long queries results in a high computing cost and long response time
    - A partial solution to this is to only reweight certain prominent terms in the relevant
    documents, such as perhaps the top 20 terms by term frequency

### Relevance feedback on the web

- little used in web search
- web search users are only rarely concerned with getting sufficient recall.
- very successful use of web link structure can also be viewed as implicit feedback

### Evaluation of relevance feedback strategies

1. compute a precision-recall graph
    - not fair - we should only evaluate with respect to documents not seen by the user.
2. use documents in the **residual collection** (the set of documents minus those assessed relevant)
    - the measured performance can then often be lower - the case if there are few relevant documents, and so a fair proportion of them have been judged by the user in the first round.

The relative performance of variant relevance feedback methods can be validly compared,
but it is difficult to validly compare performance with and without relevance feedback because the collection size and the number of relevant documents changes from before the feedback to after it.

1. have two collections
2. user studies → a time-based comparison (perhaps the best way)
    - how fast does a user find relevant documents with relevance feedback vs. another strategy (such as query reformulation)
    - how many relevant documents does a user find in a certain amount of time
    

### Pseudo relevance feedback

= **blind relevance feedback**

- automatic local analysis
- The method is to do normal retrieval to find an initial set of most relevant documents, to then assume that the top k ranked documents are relevant, and finally to do relevance feedback as before under this assumption.
- assumed that it work better than global analysis

**danger**: if the query is about copper mines and the top several documents are all about mines in Chile, then there may be query drift in the direction of documents on Chile.

### Indirect relevance feedback

using implicit feedback

ex: clicks on links
# Chapter 7 - Computing scores in a complete search system

**Index Elimination**
Basic Idea: only considers docs containing at least one query term

- High-idf Query Terms Only

**Champion Lists -** fancy list or top docs

- Precompute for each dictionary term *t,* the *r* docs of highest weight in *t*’s postings
- At query time, only compute scores for docs in the champion list

**Static Quality Scores**

- Relevance is being modeled by cosine scores
- **Authority** is typically a query-independent property of a document
    - *g(d):* Assign to each document *d* a query independent quality score in [0,1]
- Only consider documents which are considered as high-quality documents
- the posting lists are ordered by decreasing value of g(d)

**Net score**

some combination of g(d) together with the query-dependent score induced

e.g. net-score(*q,d*) = *g(d) +* cosine(*q,d*)

‫چون common ordering برای داکیومنت‌ها داریم می‌تونیم هم‌زمان پستینگ لیست ترم‌های کوئری رو پروسس کنیم و هم اسکورشون رو به دست بیاریم هم اجتماع پستینگ‌ها رو

**Champion Lists in *g(d)-*ordering**

- Maintain for each term a champion list of the *r* docs with highest *g(d) +* tf-idf*td*
- Order the postings by g(d)

**Impact ordering**

- Once tf_t,d gets smaller than a predefined threshold, the remaining postings in the list are skipped
- We sort each postings list by *wf_t,d*
    - چون دیگه کامن اردرینگ ندارن نمی‌شه همزمان پراسس کرد
    1. **early termination**
        - stop early after either
        • a fixed number of r docs
        • wft,d drops below some threshold
        - Take the union of the resulting sets of docs
    2. idf-ordered Terms
        - Look at them in order of decreasing idf
        - update score contribution from each query term
        - Stop if doc scores relatively unchanged
        

**High and Low Lists**

- For each term, we maintain two postings lists
- When traversing postings on a query, only traverse high lists first
- ‫اگه k تا نتیجه پیدا نشد بعد می‌ریم سراغ low ها

**Tiered indices**

- Break postings up into a hierarchy of lists (generalization of champion lists)
- اول فقط تایر وان‌ها رو می‌بینیم. اگر ریزالت به اندازه کافی پیدا نکردیم می‌ریم سراغ تایر بعدی
- Each tier should be an order of magnitude smaller than the next tier.

**Cluster Pruning**

- have document vectors
- pick 𝑁 documents randomly → **leaders**
- For each non-leader, we compute its nearest leader → **followers**

query time:

- find closest leader to query
- find documents in that leader cluster (followers)

**Query Term Proximity**
users prefer a document in which most or all of the query terms appear close to each other

- ω → the width of the smallest window in a document d that contains all the query terms, measured in the number of words in the window.
- the smaller that ω is, the better that d matches the query.
- **proximity-weighted scoring function**:
    - “hand coding” technique:
        - use query parser to issue a stream of queries:
            1. Run the user-generated query string as a phrase query
            2. run the two 2-term phrase queries
            3. run the individual query terms
        - **Evidence accumulation:** We need an aggregate scoring function that accumulates evidence of a document’s relevance from multiple sources
    - importance is assigned by machine learning (Chapter 15)

**Putting it all together:**

![Screen Shot 2022-01-14 at 19.12.25.png](Chapter%207%20-%20Computing%20scores%20in%20a%20complete%20search%20%20dac96433c24640c59feb5460ee512296/Screen_Shot_2022-01-14_at_19.12.25.png)

incomplete
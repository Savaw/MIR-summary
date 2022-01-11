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

net-score(*q,d*) = *g(d) +* cosine(*q,d*)

‫چون common ordering برای داکیومنت‌ها داریم می‌تونیم هم‌زمان پستینگ لیست ترم‌های کوئری رو پروسس کنیم و هم اسکورشون رو به دست بیاریم هم اجتماع پستینگ‌ها رو

**Champion Lists in *g(d)-*ordering**

- Maintain for each term a champion list of the *r* docs with highest *g(d) +* tf-idf*td*
- Order the postings by g(d)

**Impact ordering**

- Once tft,d gets smaller than a predefined threshold, the remaining postings in the list are skipped
- We sort each postings list by *wft,d*
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

todo
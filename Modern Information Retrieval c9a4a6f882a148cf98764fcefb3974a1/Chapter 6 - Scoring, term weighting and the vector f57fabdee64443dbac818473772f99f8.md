# Chapter 6 - Scoring, term weighting and the vector space model

**Jaccard Coefficient:**

|A ∩ B|/|A ∪ B|

برای دوتا مجموعه‌ی‫ A و B می‌شه سایز اشتراکشون تقسیم بر سایز اجتماعشون

$**tf_{t,d}$ (Term frequency):**

number of times that *t* occurs in *d*.

**log** frequency weight of term *t* in *d:*

$1+log (tf_{t,d})$ if tf > 0 else 0

Score for a document-query pair: sum over terms *t* in both *q and d*

$***df_t$* (Document frequency):**

the number of documents that t occurs in

$***idf_t$* (Inverse document frequency):**

$idf_t = log_{10}(N/df_t)$

N: کل داکیومنت‌ها

$***cf_t$* (Collection frequency):**

the number of times t occurs in collection

**tf-idf weight:**

product of its tf weight and its idf weight.
برای تاثیر rare terms

- idf affects the ranking of documents for queries with at least two terms.

برای یه کوئری مجموع این اسکور برای همه ترم‌هاش

### **Documents as Vectors**

Each document is now represented as a real-valued vector of tf-idf weights ∈R|*V*|.

**Vector Space Similarity:**

- Euclidean distance is a bad idea because Euclidean distance is large for vectors of different lengths.
- Angle
    - Rank documents in increasing order of the angle between query and document
    = 
    Rank documents in decreasing order of cosine(query, document)
- Cosine Similarity:
    
    ![Screen Shot 2021-11-14 at 00.29.24.png](Chapter%206%20-%20Scoring,%20term%20weighting%20and%20the%20vector%20f57fabdee64443dbac818473772f99f8/Screen_Shot_2021-11-14_at_00.29.24.png)
    
    - ضرب داخلی تقسیم بر طول وکتورها جهت نرمال‌سازی
    - در واقع می‌شه کسینوس زاویه‌ی بینشون

**Pivot Normalization:**

Cosine normalization produces weights that are too large for short documents and too
small for long documents. Adjust cosine normalization by linear adjustment: “turning” the average normalization on the pivot

**SMART notaion:**

![Screen Shot 2021-11-14 at 00.34.01.png](Chapter%206%20-%20Scoring,%20term%20weighting%20and%20the%20vector%20f57fabdee64443dbac818473772f99f8/Screen_Shot_2021-11-14_at_00.34.01.png)

Notation: ddd.qqq (document.query)

### Document Metadata

- Fields: take finite set of values (e.g., dates of authorship)
    - **parametric indexes**. There is one parametric index for each field
- Zones are similar to fields, except the contents of a zone can be arbitrary free text.
    - ‫می‌تونیم تو پستینگ‌ها سه‌تا saba.title, saba.body, saba.abstract داشته باشیم
    - ‫می‌تونیم تو یه دونه پستینگ بگیم این اکورنس مال کدوم zone عه
# Chapter 12 - Language models for information retrieval

The basic language modeling approach builds a probabilistic language model M_d from each document d, and ranks documents based on the probability of the model generating the query: P(q|M_d).

### **language model**

a function that puts a probability measure over strings drawn from some vocabulary. That is, for a language model M over an alphabet S:

![Screen Shot 2021-12-22 at 13.31.57.png](Chapter%2012%20-%20Language%20models%20for%20information%20retri%2068c23c9bdb9341deb79deecfe3f7cba9/Screen_Shot_2021-12-22_at_13.31.57.png)

example:

![Screen Shot 2021-12-22 at 13.34.18.png](Chapter%2012%20-%20Language%20models%20for%20information%20retri%2068c23c9bdb9341deb79deecfe3f7cba9/Screen_Shot_2021-12-22_at_13.34.18.png)

‫از احتمال stop/continue صرف نظر می‌کنن یه جاهایی.

‫مثلا اگر بخوایم دوتا مدل زبان رو با هم مقایسه کنیم و احتمال stop/continue شون یک باشه اصلا مهم نیست.

types:

- حالت کلی
    
    ![Screen Shot 2021-12-22 at 13.38.10.png](Chapter%2012%20-%20Language%20models%20for%20information%20retri%2068c23c9bdb9341deb79deecfe3f7cba9/Screen_Shot_2021-12-22_at_13.38.10.png)
    
- unigram LM:
    
    ![Screen Shot 2021-12-22 at 13.36.54.png](Chapter%2012%20-%20Language%20models%20for%20information%20retri%2068c23c9bdb9341deb79deecfe3f7cba9/Screen_Shot_2021-12-22_at_13.36.54.png)
    
    order does not matter → bag of words → **multinomial distribution**
    
- bigram LM:
    
    ![Screen Shot 2021-12-22 at 13.37.06.png](Chapter%2012%20-%20Language%20models%20for%20information%20retri%2068c23c9bdb9341deb79deecfe3f7cba9/Screen_Shot_2021-12-22_at_13.37.06.png)
    

here we have **bias-variance tradeoff**: With limited training data, a more constrained model tends to perform better.

**multinomial distribution:** (unigram LM)

![Screen Shot 2021-12-22 at 13.47.51.png](Chapter%2012%20-%20Language%20models%20for%20information%20retri%2068c23c9bdb9341deb79deecfe3f7cba9/Screen_Shot_2021-12-22_at_13.47.51.png)

L_d: length of document d

‫ضریب چندجمله‌ای: عبارت اول جمله‌ی سمت راست

‫از ضریب چندجمله‌ای هم وقتی می‌خوایم برای دوتا bag of words تو یک داکیومنت احتمال حساب کنیم می تونه صرف نظر شه.

### Q**uery Likelihood Model**

We construct from each document d in the collection a language model M_d

Goal: rank documents by P(d|q)

- P(q) is the same for all documents, so we ignore it
- P(d) is the prior - often treated as the same for all d

with these simplifications → P(d|q) ~ P(q|d) → rank document by P(q|d)

Under the multinomial unigram language model, which is equivalent to a multinomial Naive Bayes model, we have:

![Screen Shot 2021-12-22 at 15.39.50.png](Chapter%2012%20-%20Language%20models%20for%20information%20retri%2068c23c9bdb9341deb79deecfe3f7cba9/Screen_Shot_2021-12-22_at_15.39.50.png)

**Estimating the query generation probability P(q|d):**

![Screen Shot 2021-12-22 at 16.06.22.png](Chapter%2012%20-%20Language%20models%20for%20information%20retri%2068c23c9bdb9341deb79deecfe3f7cba9/Screen_Shot_2021-12-22_at_16.06.22.png)

M_d: Language model of d

tf_t,d: raw term frequency of term t in document d

L_d: number of tokens in d

problem: 

- if a query term does not appear in document probability will be zero
- the probability of words occurring once in the document is normally overestimated

solution: **smoothing**

in smoothing, if tf_t,d = 0 then:

$\hat{P}(t|M_d) \le cf_t/T$

cf_t: raw count of the term in the collection

T: is the raw size (number of tokens) of the entire collection.

**Jelinek-Mercer smoothing**

![Screen Shot 2021-12-22 at 16.14.56.png](Chapter%2012%20-%20Language%20models%20for%20information%20retri%2068c23c9bdb9341deb79deecfe3f7cba9/Screen_Shot_2021-12-22_at_16.14.56.png)

0 < λ < 1

M_c is a language model built from the entire documentcollection

- This mixes the probability from the document with the general collection frequency of the word
- **linear interpolation** language model

**Bayesian Updating Process (Bayesian Smoothing)**

![Screen Shot 2021-12-30 at 20.52.22.png](Chapter%2012%20-%20Language%20models%20for%20information%20retri%2068c23c9bdb9341deb79deecfe3f7cba9/Screen_Shot_2021-12-30_at_20.52.22.png)

‫آلفا و لامبدا می‌تونن ثابت نباشن. مثلا تابعی از طول کوئری باشن: برای کوئری‌های کوتاه، سموتینگ کم‌تر مناسبه (لامبدا بزرگ یا آلفا کوچیک) و برای کوئری‌های بلند سموتینگ بیشتر.

**summarize** - the retrieval ranking for a query q under the basic LM:

![Screen Shot 2021-12-30 at 20.55.40.png](Chapter%2012%20-%20Language%20models%20for%20information%20retri%2068c23c9bdb9341deb79deecfe3f7cba9/Screen_Shot_2021-12-30_at_20.55.40.png)

### Extended language modeling approaches

![Screen Shot 2021-12-30 at 21.09.54.png](Chapter%2012%20-%20Language%20models%20for%20information%20retri%2068c23c9bdb9341deb79deecfe3f7cba9/Screen_Shot_2021-12-30_at_21.09.54.png)

**model comparison:**

**Kullback-Leibler divergence:** model the risk of returning a document d as relevant to a query q

![Screen Shot 2021-12-30 at 21.08.48.png](Chapter%2012%20-%20Language%20models%20for%20information%20retri%2068c23c9bdb9341deb79deecfe3f7cba9/Screen_Shot_2021-12-30_at_21.08.48.png)

- measures how bad the probability distribution Mq is at modeling Md
- One disadvantage of using KL divergence as a ranking function is that scores are not comparable across queries.

**translation model:** 

lets you generate query words not in a document by translation to alternate terms with similar meaning

translation model can be represented by a conditional probability distribution T(·|·) between
vocabulary terms.

![Screen Shot 2021-12-30 at 21.12.17.png](Chapter%2012%20-%20Language%20models%20for%20information%20retri%2068c23c9bdb9341deb79deecfe3f7cba9/Screen_Shot_2021-12-30_at_21.12.17.png)
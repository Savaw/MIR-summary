# Chapter 8 - Evaluation in information retrieval

**Precision:** نسبت تعداد داکیومنت‌های مربوط بازگردونده شده به کل داکیومنت‌های بازگردونده شده

**Recall:** نسبت تعداد داکیومنت‌های مربوط بازگردونده شده به کل داکیومنت‌های مربوط - sensitivity

**Accuracy:** نسبت داکیومنت‌هایی که در دسته‌ی درستی قرار گرفتن

یعنی تعداد داکیومنت‌های مربوط بازگردونده شده بعلاوه‌ی تعداد داکیومنت‌های غیرمربوط بازگردونده نشده تقسیم بر کل تعداد داکیومنت‌ها

- ‫این معیار خوبی برای IR ها نیست.

**F measure:** 

weighted harmonic mean

![Screen Shot 2021-11-13 at 22.36.39.png](Chapter%208%20-%20Evaluation%20in%20information%20retrieval%2050e59602c61147ec87d484afdf5bdc35/Screen_Shot_2021-11-13_at_22.36.39.png)

F1: حالتی که آلفا یک دوم یا بتا یک باشه. → balanced F measure

F1 = 2PR/(P+R)

### Evaluation of ranked retrieval results:

**precision-recall curve:**

‫برای هر k، حساب کنیم که‫ precision و recall تو k تا نتیجه‌ی اول چیه و رو یه محور پریسیژن-ریکال بکشیمش‫.

**interpolated precision:**

‫P_interp برای یک سطح recall خاص r تعریف می‌شه بزرگ‌ترین precision ای که برای ریکال‌های بزرگ‌تر مساوی r وجود داره.

‫حالت مرسومش هم the 11-point interpolated average precision عه که برای ریکال ۰، ۰.۱، ۰.۲، ...، ۰.۹ و ۱ حساب می‌کنه.

**Precision/recall at k:**

‫پرسیژن/ریکال در کاتا داکیومنت اول

**R-precision:**

‫فرض کنیم R تا داکیومنت مرتبط داریم. پرسیژن R تا داکیومن اول بازگردونده شده می‌شه این

**Average Precision:**

consider rank position of each relevant doc K1, K2, ... KR
compute Precision@K for each K1, K2, ... KR
average precision = average of P@K

**MAP (Mean Average Precision):**

‫اگر q تا information need داشته باشیم، برای هر کدوم ap رو حساب می‌کنیم و بعد میانگین می‌گیریم.

**Reciprocal Rank:**

‫اگر r رتبه‌ی اولین داکیومنت مرتبط بازگردونده باشه‫ یک به روی r می‌شه این
1/rank

**MRR (Mean Reciprocal Rank):**

‫میانگین RR ها.

**Cumulative Gain (CG):**

Gain is accumulated starting at the top of the ranking and may be reduced, or discounted, at lower ranks.

Typical discount (for relevant document): 1 / log(rank)

let the ratings of n document be r1, ..., rn.

CG = r1 + ... + rn

let R(j, d) be the relevance score assessors gave to document d for query j.

**Discounted Cumulative Gain:**

DCG = r1 + r2/log2(2) + r3/log2(3) + ... + r_n/log2(n)

**Normalized Discounted Cumulative Gain:**

NDCG = DCG / ideal DCG

In book:

![Screen Shot 2021-11-13 at 23.32.24.png](Chapter%208%20-%20Evaluation%20in%20information%20retrieval%2050e59602c61147ec87d484afdf5bdc35/Screen_Shot_2021-11-13_at_23.32.24.png)

### Assessing relevance

**kappa statistic:**

‫برای اندازه گرفتن این که دو نفر چقد با هم agreement دارن

![Screen Shot 2021-11-13 at 23.35.32.png](Chapter%208%20-%20Evaluation%20in%20information%20retrieval%2050e59602c61147ec87d484afdf5bdc35/Screen_Shot_2021-11-13_at_23.35.32.png)

P(A): نسبت بارهایی که دو نفر یک نظر رو داشتن 

P(E): نسبت بارهایی که انتظار می‌ره اتفاقی یک نظر رو داشته باشن.

![Screen Shot 2021-11-13 at 23.38.53.png](Chapter%208%20-%20Evaluation%20in%20information%20retrieval%2050e59602c61147ec87d484afdf5bdc35/Screen_Shot_2021-11-13_at_23.38.53.png)
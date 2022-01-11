# Chapter 5 - Index compression

**Heaps’ law:** Estimating the number of terms

$M = kT^b$

M: Vocabulary size (number of distinct terms in collection)

T: number of tokens in collection

**Zipf’s law**: Modeling the distribution of terms

$cf_i = c/i$

cf_i: collection frequency of term in rank i

تعداد تکرار با عکس رنک (از نظر تعداد تکرار) متناسبه

(a power low)

**Dictionary compression:**

- fixed length array → wasteful
    - term: 20 bytes - document freq: 4 bytes - pointer to posting list: 4 bytes
- as one string
    - freq: 4 bytes - postings ptr: 4 bytes - term ptr (in string): 3 bytes
- Blocked storage
    - by grouping terms in the string into blocks of size k and keeping a term pointer only for the first term of each block
    - We store the length of the term in the string as an additional byte at the beginning of the term
    - tradeoff between compression and the speed of term lookup
- front coding
    - Sorted words commonly have long common prefix – store differences only
    - 8automata8automate9automatic10automation → 8**automat****a**1◊**e**2◊**ic**3◊**ion**

**Postings Compression:**

- Gap Encoding
    - ‫به جای نگه‌داشتن پوزیشن‌ها فاصله‌ی پوزیشن‌ها رو نگه می‌داریم
- Variable byte codes (VB)
    - 824 = 0b1100111000 → VB: **0**0000110-**1**0111000
    - ‫بیت پر ارزش هر بایت می‌شه continuation bit که فقط برای بایت آخر یکه
- Unary code
    - 4 → 11110
    - 7 → 11111110
- Gamma code
    - a pair of length and offset
    - In gamma code for number G, offset is G in binary, but with the leading 1 removed.
    - Length encodes the length of offset in unary
    - 25 = 0b11001 → Gamma code: 11110-1001
    - total bits: 2⎣log G⎦+ 1 bits
    - prefix-decodable - universal - parameter-free
# Chapter 19 - Web search basics

Early attempts at making web information “discoverable”:

1. full-text index search engines such as Altavista, Excite and Infoseek
    - presented the user with a keyword search interface supported by inverted indexes and ranking mechanisms building on those introduced in earlier chapters
2. taxonomies populated with web pages in categories
    - allowed the user to browse through a hierarchical tree of category labels
    

## Web characteristics

- Biggest challenge: decentralized content publishing with essentially no central control of authorship
    - dozens of (natural) languages and thousands of dialects
    - tremendous variation in grammar and style
- the web contained truth, lies, contradictions and suppositions on a grand scale

### The web graph

We can view the static Web consisting of static HTML pages together with the hyperlinks between them as a directed graph in which each web page is a node and each hyperlink a directed edge.

**anchor text:** text on a page with a hyperlink attached to it

**in-links:** hyperlinks into a page 

- **in-degree** has averaged from roughly 8 to 15

**out-links:** hyperlinks out of a page

- Number of links for a website distribution is widely reported to be a **power law**, in which the total number of web pages with in-degree i is proportional to 1/i^a. (a~2.1)
- several studies have suggested that the directed graph connecting web pages has a **bowtie** shape:
    
    ![Screen Shot 2022-01-12 at 17.24.25.png](Chapter%2019%20-%20Web%20search%20basics%201c9aba2910df4787bb5965e7e06dff5d/Screen_Shot_2022-01-12_at_17.24.25.png)
    

### Spam

First generation of spam: the manipulation of web page content for the purpose of appearing high up in search results for selected keywords

- e.g. Numerous repetitions of a keyword in a page to rank higher in a search engine whose scoring was based on term frequencies

**paid inclusion:** pay to have one’s web page included in the search engine’s index

Some other spamming methods:

- **cloaking:** the spammer’s web server returns different pages depending on whether the http request comes from a web search engine’s crawler.
- **doorway page**: contains text and metadata carefully chosen to rank highly on selected search keywords When a browser requests the doorway page, it is redirected to a page containing content of amore commercial nature.

**Search Engine Optimizers (SEO):** provide consultancy services for clients who seek to have their web pages rank highly on selected keywords.

**adversarial information retrieval:** the research sub-area that sprung up around the battle of SEOs and the web search engines

**link analysis:** To combat spammers who manipulate the text of their web pages is the exploitation of the link structure of the Web**.**

**link spam:** efforts in subverting link analysis

## Advertising as the economic model

- **cost per mil (CPM)**: the CPM cost to the company of having its banner advertisement displayed 1000 times.
    - goal: branding
    - **impressions:** the number of times it is displayed
- **cost per click (CPC)**
    - goal: induce a transaction

**sponsored search** or **search advertising:** 

1. accept bids from companies who wanted their web page shown on the query q.
2. return the pages of all advertisers who bid for q, ordered by their bids in response to the query q

Later “pure” search engines (**algorithmic search result**) with search advertising combined into a single user experience.

**search engine marketing (SEM)**: the profession of understanding how search engines do ranking and how to allocate marketing campaign budgets to different keywords and to different sponsored search engines

**click spam**: clicks on sponsored search results that are not from bonafide search users

## The search user experience

two principles by Google:

1. focus on relevance, specifically precision rather than recall in the first few results
2. a user experience that is lightweight; search engine pages are uncluttered

### User query needs

common queries categories:

1. **informational**
    - seek general information on a broad topic
    - users with informational queries typically try to assimilate information from multiple web pages
2. **navigational** 
    - seek the website or home page of a single entity that the user has in mind
    - the best measure of user satisfaction is precision at 1.
3. **transactional**
    - a prelude to the user performing a transaction on the Web – such as purchasing a product, downloading a file or making a reservation.

## Index size and estimation

Q: given two search engines, what are the relative sizes of their indexes?

A: question is imprecise, because:

1. a search engine can return web pages whose contents it has not (fully or even partially) indexed.
    - search engines generally index only the first few thousand words in a web page
    - a search engine is aware of a page p that is linked to by pages it has indexed, but has not indexed p itself
2. Search engines generally organize their indexes in various tiers and partitions, not all of which are examined on every search.
    - a web page deep inside a website may be indexed but not retrieved on general web searches; it is however retrieved as a result on a site-specific search
    

In spite of these issues, a number of techniques have been devised for crude estimates of the ratio of the index sizes of two search engines, E1 and E2:

underlying hypothesis: (far from true!)

- there is a finite size for the Web from which each search engine chooses a subset
- each engine chooses an independent, uniformly chosen subset

**capture-recapture method**

pick a random page from the index of E1 and test whether it is in E2’s index and symmetrically, test whether a random page from E2 is in E1. 

These experiments give us fractions x and y such that a fraction x of the pages in E1 are in E2, while a fraction y of the pages in E2 are in E1 and we have:
x|E1| ≈ y|E2|

**unbiased sampling:** 

(if we have access to neither E1 nor E2 index)

generate a random page from the entire (idealized, finite)Web

How?

1. **Random searches**: Begin with a search log of web searches; send a random search from this log to E1 and a random page from the results. 
    - Since such logs are not widely available outside a search engine, one implementation is to trap all search queries going out of a work group (say scientists in a research center) that agrees to have all its searches logged.
        - issues: (i) the bias from the types of searches made by the work group (ii) a random document from the results of such a random search to E1 is not the same as a random document from E1.
2. **Random IP addresses:** generate random IP addresses and send a request to a web server residing at the random address, collecting all pages at that server. 
    - biases: (i) many hosts might share one IP (virtual hosting) (ii) not accept http requests
3. **Random walks:** run a random walk starting at an arbitrary web page andThis walk would converge to a steady state distribution.
    - It works if the web graph were a strongly connected directed graph and it’s not!
    - time it takes to settle into steady state is unknown
4. **Random queries:** pick a page (almost) uniformly at random from a search engine’s index by posing a random query to it.
    - create query:
        1. amassing a sample web dictionary 
            - by crawling a limited portion of the Web
            - by crawling a manually-assembled representative subset of the Web
        2. conjunctive query with two or more randomly chosen words from this dictionary
    - process:
        1. use a random conjunctive query on E1 and pick from the top 100 returned results a page p at random.
        2. test p for presence in E2 by choosing 6-8 low-frequency terms in p and using them in a conjunctive query for E2.
    - issues:
        - Our sample is biased towards longer documents
        - Picking from the top 100 results of E1 induces a bias from the ranking algorithm of E1. Picking from all the results of E1 makes the experiment slower.
        - During the checking phase, a number of additional biases are introduced:
        for instance, E2 may not handle 8-word conjunctive queries properly.
        - Either E1 or E2 may refuse to respond to the test queries, treating them as robotic spam rather than as bona fide queries.
        - There could be operational problems like connection time-outs.
5. **Document random walk sampling:** a document is chosen by a random walk on a virtual graph derived from documents
    - nodes are documents; two documents are connected by an edge if they share two or more words in common.
    - The graph is never instantiated; rather, a random walk on it can be performed by moving from a document d to another by picking a pair of keywords in d, running a query on a search engine and picking a random document from the results.

## Near-duplicates and shingling

Simplest approach to detecting duplicates:

For each web page, compute a fingerprint that is a succinct (say 64-bit) digest of the characters on that page, whenever the fingerprints of two web pages are equal, we test whether the pages themselves are equal.

issue: **near duplication**

**Shingling:** 

Given a positive integer k and a sequence of terms in a document d, define the k-shingles of d to be the set of all consecutive sequences of k terms in d. 

→ two documents are near duplicates if the sets of shingles generated from them are nearly the same

**Method:** We compute Jaccard coefficient - |S(d1) ∩ S(d2)|/|S(d1) ∪ S(d2)| - for the set of shingles of two documents. if it exceeds a preset threshold, we declare them near duplicates.

**How to do efficiently**?

We map every shingle into a hash value over a large space.

incomplete
# Chapter 20 - Web crawling and indexes

**Web crawling** is the process by which we gather pages from the Web, in order to index them and support a search engine. 

objective: quickly and efficiently gather as many useful web pages as possible, together with the link structure that interconnects them.

spider: web crawler

### Features a crawler *must* provide

- **Robustness**:
    - Crawlers must be designed to be resilient to spider traps.
- **Politeness**
    - Respect implicit and explicit policies regulating the rate at which a crawler can visit web servers.

### Features a crawler *should* provide

- **Distributed**
    - execute in a distributed fashion across multiple machines.
- **Scalable**
    - permit scaling up the crawl rate by adding extra machines and bandwidth.
- **Performance and efficiency**
    - make efficient use of various system resources including processor, storage and network bandwidth.
- **Quality**
    - be biased towards fetching “useful” pages first.
- **Freshness**:
    - operate in continuous mode: it should obtain fresh copies of previously fetched pages.
- **Extensible**
    - designed to be extensible in many ways – to cope with new data formats, new fetch protocols, and so on.
    - This demands that the crawler architecture be modular.

## Crawling

basic operation of any hypertext crawler:

1. begins with one or more URLs that constitute a **seed set**.
2. picks a URL from this seed set, then fetches the web page at that URL
3. fetched page is then parsed, to extract both the text and the links from the page
    1. The extracted text is fed to a text indexer
4. extracted links (URLs) are added to a **URL frontier**, which at all times consists of URLs whose corresponding pages have yet to be fetched by the crawler.

### Crawler architecture

*follows the design of the **Mercator** crawler*

![Screen Shot 2022-01-12 at 22.14.08.png](Chapter%2020%20-%20Web%20crawling%20and%20indexes%20d5de991cf67e45a4810a67d0da406e60/Screen_Shot_2022-01-12_at_22.14.08.png)

- **URL filter** is used to determine whether the extracted URL should be excluded from the frontier based on one of several tests.
    - the crawl may seek to exclude certain domains (say, all .com URLs) – in this case the test would simply filter out the URL if it were from the .com domain.
    - Many hosts on the Web place certain portions of their websites off-limits to crawling, under a standard known as the **Robots Exclusion Protocol**. This is done by placing a file with the name robots.txt at the root of the URL hierarchy at the site.
- **URL normalization**: often the HTML encoding of a link from a web page p indicates the target of that link relative to the page p and thus must be normalized.

**Distributing the crawler**

- scaling
- geographically distributed to fetch “near” hosts

![Screen Shot 2022-01-12 at 22.43.18.png](Chapter%2020%20-%20Web%20crawling%20and%20indexes%20d5de991cf67e45a4810a67d0da406e60/Screen_Shot_2022-01-12_at_22.43.18.png)

- **host splitter:** dispatch each surviving URL to the crawler node responsible for the URL
- The “Content Seen?” module in the distributed architecture is, however, complicated by several factors:
    - document fingerprints/shingles cannot be partitioned based on host name
    - very little locality in the stream of document fingerprints/shingles - caching does not help
    - Documents change over time and so, in the context of continuous crawling, we must be able to delete their outdated fingerprints/shingles from the content-seen set(s). In order to do so, it is necessary to save the fingerprint/shingle of the document in the URL frontier.

### DNS resolution

- DNS resolution is a well-known bottleneck in web crawling.
    - caching can help
- the lookup implementations in standard libraries are generally synchronous. other crawler threads at that node are blocked until the first request is completed.
    - implement their own DNS resolver as a component of the crawler

### The URL frontier

Two important considerations:

1. prioritizing
    - high-quality pages that change frequently should be prioritized for frequent crawling
    - the priority of a page should be a function of both its change rate and its quality
2. politeness
    - insert a gap between successive fetch requests to a host that is an order of magnitude
    larger than the time taken for the most recent fetch from that host
    

![Screen Shot 2022-01-12 at 22.55.48.png](Chapter%2020%20-%20Web%20crawling%20and%20indexes%20d5de991cf67e45a4810a67d0da406e60/Screen_Shot_2022-01-12_at_22.55.48.png)

submodules:

- **F front queues**
    - FIFO
    - implement the prioritization
    - a prioritizer first assigns to the URL an integer priority i between 1 and F based on its fetch history and the URL is appended to the ith of the front queues
- **B back queues**
    - FIFO
    - implement politeness
    - Each of queus:
        - nonempty while the crawl is in progress
        - only contains URLs from a single host
            - An auxiliary table T is used to maintain the mapping from hosts to back queues
- **a heap**
    - one entry for each back queue, the entry being the earliest time t_e at which the host corresponding to that queue can be contacted again.
    

process:

1. A crawler thread requesting a URL from the frontier extracts the root of this heap and (if necessary) waits until the corresponding time entry t_e. 
2. takes the URL u at the head of the back queue j corresponding to the extracted heap root, and proceeds to fetch the URL u.
3. After fetching u, the calling thread checks whether j is empty. If so, it picks a front queue and
extracts from its head a URL v. 
    - The choice of front queue is biased (usually by a random process) towards queues of higher priority, ensuring that URLs of high priority flow more quickly into the back queues.
4. We examine v to check whether there is already a back queue holding URLs from its host.
    1. If so, v is added to that queue and we reach back to the front queues to find another candidate URL for insertion into the now-empty queue j. 
    2. This process continues until j is non-empty again. 
    3. In any case, the thread inserts a heap entry for j with a new earliest time t_e based on the properties of the URL in j that was last fetched (such as when its host was last contacted as well as the time taken for the last fetch), then continues with its processing.

- The designers of Mercator recommend a rough rule of three times as many back queues as crawler threads.
- solution for memory problems: let most of the URL frontier reside on disk.

## Distributing indexes

Two alternative index implementations:

1. partitioning by **terms**, aka **global index organization**
    - the dictionary of index terms is partitioned into subsets, each subset (along with postings) residing at a node
    - query is routed to the nodes corresponding to its query terms
    - allows greater concurrency
    - it’s difficult in practice:
        - Multi-word queries require the sending of long postings lists between sets of nodes for merging
        - Load balancing the partition is governed not by an a priori analysis of relative term frequencies, but rather by the distribution of query terms and their co-occurrences, which can drift with time or exhibit sudden bursts.
        - Achieving good partitions is a function of the co-occurrences of query terms and entails the clustering of terms to optimize objectives that are not easy to quantify.
2. partitioning by **documents**, aka **local index organization**
    - each node contains the index for a subset of all documents
    - Each query is distributed to all nodes, with the results from various nodes being merged before presentation to the user
    - trades more local disk seeks for less inter-node communication.
    - difficulty: global statistics used in scoring – such as idf – must be computed across the entire document collection
        - solution: computed by distributed “background” processes that periodically refresh the node indexes with fresh global statistics
    - How to partition of documents to nodes?
        1. assign all pages from a host to a single node (following crawler partitioning)
            - problem: on many queries, a preponderance (=majority) of the results would come from documents at a small number of hosts (and hence a small number of index nodes).
        2. hash of each URL into the space of index nodes
            - more uniform
        3. partition the document collection into indexes of documents that are more likely to score highly on most queries and low-scoring indexes with the remaining documents. 
            - We only search the low-scoring indexes when there are too few matches in the high-scoring indexes, as described in Chapter 7.

## Connectivity servers

web search engines require a connectivity server

**connectivity server:** supports fast connectivity queries on the web graph.

**connectivity queries:** connectivity queries are which URLs link to a given URL? and which
URLs does a given URL link to? 

→ we wish to store mappings in memory from URL to out-links, and from URL to in-links.

Applications:

- crawl control
- web graph analysis
- sophisticated crawl optimization
- link analysis

### **How to store links**

- each web page is represented by a unique integer

**naively**

explicitly represent each link by its two end points, each a 32-bit integer

**efficient** (for responding to queries of the form *which pages link to p?*)

- build an **adjacency table** that resembles an inverted index: it has a row for each web
page, with the rows ordered by the corresponding integers
- The row for any page p contains a sorted list of integers, each corresponding to a web page that links to p.

→ cuts the space taken by 50%

ideas for further reduce the storage:

1. **Similarity between lists:** Many rows of the table have many entries in common.
    - explicitly represent a prototype row for several similar rows, the remainder can be succinctly expressed in terms of the prototypical row.
    - e.g. all pages in a website have links to *about, terms of use, copyright notice, etc*
2. **Locality**: many links from a page go to “nearby” pages – pages on the same host, for instance. 
    - in encoding the destination of a link, we can often use small integers and thereby save space.
3. We use **gap encodings** in sorted lists: rather than store the destination of each link, we store the offset from the previous entry in the row.

**Full technic**

1. order URLs lexicographically (the domain name part of the URL should be inverted)
2. assign each URL its position in this ordering as the unique identifying integer
3. walk down the table, encoding each table row in terms of the seven preceding rows
    - under the lexicographic ordering of URLs, it is very likely that the pages from a website appear as contiguous rows in the table
    - e.g. encode each row in form of “the same as the row at offset *a* (meaning, *a* rows earlier in the table), with *b* replaced by *c* adding in *d*”.
    - why seven:
        1.  the offset can be expressed with only 3 bits; this choice is optimized empirically
        2. fixing the maximum offset to a small value like seven avoids having to perform an expensive search among many candidate prototypes
    - also using gap encodings for storing numbers

→ the series of techniques outlined here appears to use as few as 3 bits per link, on average – a dramatic reduction from the 64 required in the naive representation

**answer support connectivity queries**

1. index lookup from (a hash of) the URL to its row number
2. reconstruct these entries
    - this could lead through many levels of indirection
        - A heuristic for controlling this:
        - demand a threshold of similarity between the current row and the candidate prototype when constructing table
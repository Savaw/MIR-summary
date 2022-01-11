# Chapter 1 - Boolean retrieval

**Information retrieval (IR)** is finding material (usually documents) of
an unstructured nature (usually text) that satisfies an information need
from within large collections (usually stored on computers).

Distinguished by the scale at which they operate:

web search                                                                   personal information retrieval

  ←|———————————|—————————————————|→

                             enterprise, institutional,

                    and domain-specific search

**Grepping:** The simplest form of document retrieval is for a computer to do this sort of linear scan through documents.

Why we need more than grepping?:

- To process large document collections quickly
- To allow more flexible matching operations (e.x. *Near*)
- To allow ranked retrieval

→ So we need **indexes.**

Binary term-document **Incidence matrix:** A matrix in which we specify which documents have which terms.

**Terms:** indexed units (similar to words)

**Boolean retrieval model**: A model for information retrieval in which we can pose any query which is in the form of a Boolean expression of terms, that is, in which terms are combined with the operators AND, OR, and NOT. The model views each document as just a set of words.

**Document**: whatever units we have decided to build a retrieval system over.

**Collection** or **Corpus:** the group of documents over which we perform retrieval as.

**Ad hoc retrieval:** The most standard IR task. ****In it, a system aims to provide documents from within the collection that are relevant to an arbitrary user information need, communicated to the system by means of a one-off, user-initiated query. 

**Information need** is the topic about which the user desires to know more

**Query** is what the user conveys to the computer in an attempt to communicate the information need. 

A document is **relevant** if it is one that the user perceives as containing information of value with respect to their personal information need. 

**Effectiveness** of an IR system: the quality of its search results

Two key statistics about IR systems:

- **Precision**: What fraction of the returned results are relevant to the information need?
- **Recall**: What fraction of the relevant documents in the collection were returned by the system?

**Inverted Index:**

We keep a **dictionary** of terms (sometimes also referred to as a vocabulary or lexicon). Then for each term, we have a list that records which documents the term occurs in. Each item in the list – which records that a term appeared in a document (and, later, often, the positions in the document) – is conventionally called a **posting**. The list is then called a **postings list** (or inverted list), and all the postings lists taken together are referred to as the **postings**.

**Building an inverted index:**

1. Collect the documents to be indexed
2. Tokenize the text, turning each document into a list of tokens
3. Do linguistic preprocessing, producing a list of normalized tokens, which are the indexing terms
4. Index the documents that each term occurs in by creating an inverted index, consisting of a dictionary and postings.

**Processing Boolean queries:**

**simple conjunctive query**: A *AND* B

1. Locate A in the Dictionary
2. Retrieve its postings
3. Locate B in the Dictionary
4. Retrieve its postings
5. Intersect (also referred to as merging) the two postings lists

Using simple merge algorithm (using two pointers): the complexity of querying is theta(N), where N is the number of documents in the collection.

**Query optimization** is the process of selecting how to organize the work of answering a query so that the least total amount of work needs to be done by the system.

**Ranked retrieval models:** users largely use **free text queries**, that is, just typing one or more words rather than using a precise language with operators for building up query expressions, and the system decides which documents best satisfy the query.

**Proximity operator** is a way of specifying that two terms in a query must occur close to each other in a document, where **closeness** may be measured by limiting the allowed number of intervening words or by reference to a structural unit such as a sentence or paragraph.

**Term frequency** the number of times a term occurs in a document.
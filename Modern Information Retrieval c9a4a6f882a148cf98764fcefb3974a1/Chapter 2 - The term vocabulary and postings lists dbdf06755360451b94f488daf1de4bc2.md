# Chapter 2 - The term vocabulary and postings lists

## **Document delineation and character sequence decoding**

1. Obtaining the character sequence in a document
The sequence of characters may be encoded by one of various single byte or multibyte encoding schemes, such as Unicode UTF-8, or various national or vendor-specific standards. We need to determine the correct encoding.
2. Choosing a document unit
For example, we take each file in a folder as a document or We may well wish to index each chapter or paragraph as a mini-document.

## **Determining the vocabulary of terms**

1. Tokenization
    1. A **token** is an instance of a sequence of characters in some particular document that are grouped together as a useful semantic unit for processing.
    2. A **type** is the class of all tokens containing the same character sequence.
    3. A **term** is a (perhaps normalized) type that is included in the IR system’s dictionary.
2. Dropping common terms: stop words
    1. **stop words:** some extremely common words which would appear to be of little value in helping select documents.
    2. **stop list:** list of stop words the members of which are then discarded during indexing.
3. Normalization (equivalence classing of terms)
    1. **Token normalization** is the process of canonicalizing tokens so that matches occur despite superficial differences in the character sequences of the tokens. The most standard way to normalize is to implicitly create **equivalence classes**, which are normally named after one member of the set.
    2. Must take care of:
        - Accents and diacritics
        - Capitalization/case-folding
4. Stemming and lemmatization
    1. **Stemming** usually refers to a crude heuristic process that chops off the ends of words.
    2. **Lemmatization** usually refers to doing things properly with the use of a vocabulary and morphological analysis of words, normally aiming to remove inflectional endings only and to return the base or dictionary form of a word, which is known as the **lemma**. 

## **Faster postings list intersection via skip pointers**

- **Skip pointer:** Skip pointers are effectively shortcuts that allow us to avoid processing parts of the postings list that will not figure in the search results.
- **Skip list:** postings list augmented with skip pointers (at indexing time)

where to place skip pointers? There is a tradeoff. A simple heuristic for placing skips, which has been found to work well in practice, is that for a postings list of length P, use √P evenly-spaced skip pointers.

## Positional postings and phrase queries

**Phrase queries:** search for exact phrase for example "Stanford university"

### Biwords indexes

Consider every pair of consecutive terms in a document as a phrase, In this model, we treat each of these biwords as a vocabulary term.
Longer phrases can be processed by breaking them down to biwords.

→ There may be false positives → need to verify found documents.

**phrase index**: if the index includes variable length word sequences

### Positional indexes

store postings of the form docID: <position1, position2, . . . >

- For processing a phrase query, same general technique is used as before, but rather than simply checking that both terms are in a document, you also need to check that their positions of appearance in the document are compatible with the phrase query being evaluated.
- the complexity of a Boolean query is Q(T) rather than Q(N)

### Combination schemes

A combination strategy uses a phrase index, or just a biword index, for certain queries and uses a positional index for other phrase queries. 

use biword for:

- common queries
- where the individual words are common but the desired phrase is comparatively rare

another strategy:

**next word index** which records terms that follow it in a document.
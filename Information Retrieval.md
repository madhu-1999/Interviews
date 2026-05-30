#ir #nlp 
```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
include: 
exclude: 
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
# Definition
Information retrieval (IR) is finding material (usually documents) of an unstructured nature (usually text) that satisfies an information need, from within large collections (usually stored on computers).
# Terminology
## Document
Unit on which IR system is built on ex: book chapters, memos, web pages.
Formally represented as a set of terms.
## Collection/corpus
Collection of documents over which IR is performed.
## Term
Keyword in document.
Commonly used, low information words (stop words) like _a, an, the_ are not included.
## Query
Formal expression of the user's information need (Think SQL)
## Relevance
A document is relevant if the user perceives it to have some content of value with regards to the information need, either in the form of an exact match or expressing the concept in different words.
The effectiveness of an IR system is measured using: 
_precision_: How many results are actually relevant
_recall_: How many relevant results were actually returned
## Tolerant retrieval
Relates to supporting _inexact_ matching of query terms to find terms that are similar but. not identical to the user's input (useful when user makes spelling errors, autocomplete).

# Boolean retrieval model
Classic IR model where documents are retrieved based on whether they satisfy conditions of a query that uses Boolean logic.

A user's query uses logical operators like AND, OR, and NOT to create a rule for retrieval. The system then returns all documents that match the rule.
The model can be defined formally as follows:

- Let $T = \{t_1, \space t_2, \space t_3...\space t_k\}$ be a set of all index terms.
- A document $D_j$ is any subset of $T$
- A query $Q$ is a Boolean expression: $Q = (t_a \lor t_b)\space\land\space (\neg t_c\lor t_d)\space\land...$    
Retrieval is the process of identifying the set of all documents $\{D_j\}$ that satisfy the query $Q$.

Ex: Let the set of terms $T$ be:
$$T = \{\text{probability, decision-making, Bayes principle, Bayesian epistemology}\}$$
Then the set of documents is:
$$D = \{D_1, \space D_2,\space D_3\}$$
where,
$$D_1 = \{\text{probability, decison-making}\}$$
$$D_2 = \{\text{probability, Bayes principle}\}$$
$$D_3 = \{\text{probability, Bayesian epistemology}\}$$
Let the Query $Q$ be:
$$Q = \text{probability}\space\land\space\text{decision-making}$$
Then the relevant documents are retrieved as:
$$S_1 = \{D_1,D_2,D_3\}$$$$S_2=\{D_1\}$$
where $S_1$ = set of documents containing _probability_ and $S_2$ = set of documents containing _decision-making_
The query $Q$ is resolved as: $Q = \{D_1,D_2,D_3\}\space\cap\space\{D_1\} = \{D_1\}$ 
## Advantages
+ Easy to implement
+ Intuitive
## Disadvantages
+ Can be hard to translate a information need into a Boolean expression
+ All terms are equally weighted
+ No partial matching
+ No ranking of the documents
+ The model frequently returns either too few or too many documents in response to a user query.
# Inverted Index
How to store documents efficiently for retrieval? one way is to use inverted index.
Maps each term to list of documents (and optionally positions) where that term appears. 
Ex: “banana” → [Doc1, Doc2]
## Basic implementation
```run-python
# Sample documents
docs = {
    1: "the cat sat on the mat",
    2: "the dog sat in the fog",
    3: "the cat chased the dog",
}

# Building inverted index (hash table)
inverted_index = {}

# Define stop list
stop_list = ['the', 'on', 'in']

for doc_id, text in docs.items():
    for word in text.split():
        if word in stop_list:
            continue
        if word not in inverted_index:
            inverted_index[word] = set()
        inverted_index[word].add(doc_id)

print("Inverted Index:")
for term, posting in inverted_index.items():
    print(term, ":", posting)
```
The index is queried term by term and query operators (AND, OR, NOT) are applied to the results to get the final output.
```run-python
def boolean_and(term1, term2):
    return inverted_index.get(term1, set()) & inverted_index.get(term2, set())

def boolean_or(term1, term2):
    return inverted_index.get(term1, set()) | inverted_index.get(term2, set())

def boolean_not(term, all_docs):
    return all_docs - inverted_index.get(term, set())

all_docs = set(docs.keys())

print("cat AND dog:", boolean_and("cat", "dog"))
print("cat OR dog:", boolean_or("cat", "dog"))
print("NOT cat:", boolean_not("cat", all_docs))

```
## Positional implementation
```run-python ln:True
# Sample documents
docs = {
    1: "the cat sat on the mat",
    2: "the dog sat in the fog",
    3: "the cat chased the dog",
}

# Building inverted index (hash table)
pos_index = {}

# Define stop list
stop_list = ['the', 'on', 'in']

for doc_id, text in docs.items():
    for pos, word in enumerate(text.split()):
        if word in stop_list:
            continue
        if word not in pos_index:
            pos_index[word] = {}
        if doc_id not in pos_index[word]:
            pos_index[word][doc_id] = []
        pos_index[word][doc_id].append(pos)

print("Positional Inverted Index:")
for term, postings in pos_index.items():
    print(term, ":", postings)

```

The index is queried term by term and query operators (AND, OR, NOT) are applied to the results to get the final output.
```run-python
# All document IDs
all_docs = set(docs.keys())

def get_docs(term):
    return set(pos_index.get(term, {}).keys())

def boolean_and(t1, t2):
    return get_docs(t1) & get_docs(t2)

def boolean_or(t1, t2):
    return get_docs(t1) | get_docs(t2)

def boolean_not(t):
    return all_docs - get_docs(t)

print("cat AND dog     →", boolean_and("cat", "dog"))
print("cat OR dog      →", boolean_or("cat", "dog"))
print("NOT cat         →", boolean_not("cat"))

```
## Using skip pointers
In a normal inverted index, posting lists are **sorted** lists of document IDs. 
When doing **A AND B**, we merge the lists **linearly** ($S_1\cap S_2$), element by element →  **O(|A| + |B|)**.
But if lists are long, this is slow.

Suppose we are looking at 41 (upper) and 11 (lower).
11 is smaller and the skip successor of 11 on the lower list is 31.
So we can skip to 31 directly instead of traversing every element, since lists are sorted.
![[Screenshot 2025-12-07 at 8.14.06 PM.png]]
Then the following questions arise: 
1. How to decide where to place skip pointers?
2. How many skip pointers to place?
### ___Placing skips___
More skips $\rightarrow$ shorter skip spans $\rightarrow$ more likely to skip, but lots of comparisons to skip pointers. 
Fewer skips → few pointer comparisons, but long skip spans $\rightarrow$ few successful skips.

A simple heuristic, is to place $\sqrt L$ skip pointers __evenly__ for a postings list of length $L$ . 
Uniform spacing reduces comparisons in all regions.
```
Index:   0   1   2   3   4   5   6   7   8   9  ...
Posts: [d0, d1, d2, d3, d4, d5, d6, d7, d8, d9, ...]
         |----------------|----------------|
       skip → 4        skip → 8

```

### ___Implementation___
```run-python
import math

# 1. Build a posting list with skip pointers
def add_skip_pointers(postings):
    n = len(postings)
    skip_len = int(math.sqrt(n))

    skips = {}
    if skip_len > 1:
        for i in range(0, n, skip_len):
            j = i + skip_len
            if j < n:
                skips[i] = j  # pointer from index i → j
    return skips
  
# 2. Intersect two lists using skips  
def intersect_with_skips(A, B):
    i = j = 0
    result = []

    skipsA = add_skip_pointers(A)
    skipsB = add_skip_pointers(B)

    while i < len(A) and j < len(B):
        if A[i] == B[j]:
            result.append(A[i])
            i += 1
            j += 1

        elif A[i] < B[j]:
            # Can we skip ahead in A?
            if i in skipsA and A[skipsA[i]] <= B[j]:
                i = skipsA[i]      # jump
            else:
                i += 1             # step
        else:
            # Can we skip ahead in B?
            if j in skipsB and B[skipsB[j]] <= A[i]:
                j = skipsB[j]
            else:
                j += 1

    return result

A = [3, 7, 10, 20, 21, 35, 70, 71, 72]
B = [1, 7, 15, 20, 35, 50, 60]

print(intersect_with_skips(A, B))
```

Time complexity: $O(\sqrt n)$ where $n$ is length of postings list.
### ___Summary___
- Skip pointers **speed up intersections**, especially for **long posting lists**.
- They allow the algorithm to **jump over irrelevant segments** rather than comparing step-by-step.
- Best used for **Boolean AND** queries.    
- Do not help much with:
    - Short posting lists    
    - OR queries    
    - Phrase/positional queries
# Phrase queries
Queries which need to match phrase text exactly ex: "stanford university".
Note that it is not the same as stanford $\cap$ university.
So, _"I went to Stanford University"_ is a match, but _"I went to university at Stanford"_ is not.
## Biword indexes
One way to answer such queries is to use biword indexes.
Ex: "the quick brown fox." will give bigrams
	[ "quick brown", "brown fox"] 
after preprocessing, and the postings list could look like:
	"quick brown" → [doc1, doc7, doc10]

Longer phrase queries can be broken down into groups of bigrams.
Ex:  "the quick brown fox" phrase query will result in a search query:
	"quick brown" $\cap$ "brown fox"

However, this method can result in false positives, since it ensures 2 word adjacency but ___NOT___ phrase continuity.
Ex: Text: "the quick brown fox jumped over the brown dog"
Query: "quick brown dog"
	"quick brown" $\cap$ "brown dog"
		T                        T
will evaluate to true and return the text "the quick brown fox jumped over the brown dog" as a match even though the full phrase "quick brown dog" does not exist in the text.
## Positional indexes
A [[#Positional implementation|positional inverted index]] can be used to solve this issue, since it stores position indices for a given term in a document, we can compare if each term in phrase query is adjacent in a particular document.

```run-python
# Positional inverted index: term → {doc_id : [positions]}
pos_index = {
    'new':   {1: [0], 2: [4]},
    'york':  {1: [1], 2: [5]},
    'city':  {1: [2], 3: [10]},
}

def phrase_query(terms, pos_index):
    if not terms:
        return set()

    # Get postings for the first term
    first_term = terms[0]
    if first_term not in pos_index:
        return set()

    candidate_docs = set(pos_index[first_term].keys())
        
    # Intersect doc sets for all terms
    for term in terms:
        if term not in pos_index:
            return set()
        candidate_docs &= set(pos_index[term].keys())

    results = set()

    # Check positional alignment for each candidate doc
    for doc in candidate_docs:
        # Start with positions of first term
        positions = pos_index[first_term][doc]

        # Try to extend each possible starting position
        for p in positions:
            match = True
            # Check all other terms
            for i in range(1, len(terms)):
                term = terms[i]
                if (p + i) not in pos_index[term][doc]:
                    match = False
                    break
            if match:
                results.add(doc)
                break

    return results

query = ["new", "york", "city"]
print(phrase_query(query, pos_index))

```
# Data structures for dictionaries
 Inverted indexes are constructed using dictionaries, but what data structures can be used to create dictionaries, under the hood?
## Hash table
Each term is hashed to an index in a hash table.
### Advantages
- **Fast lookup** (best for Boolean IR).
- Exact term lookup
### Disadvantages
- **No ordered traversal** (cannot support prefix queries like “com*”).
- Growing the table is expensive.
- Collisions require chaining or probing.
## Search Trees
AVL / Red Black Tree
Terms stored lexicographically in a balanced BST.
### Advantages
+ Lookup guaranteed O(log n).
+ Supports **ordered operations** (prefix search, range queries).
- Good for dynamic insertion/deletion.
### Disadvantages
- Slower than hash tables for exact lookup.
- Pointer overhead increases memory usage.
## B-Trees / B+ Trees
### Advantages
- Very efficient for **external storage** (disk-based IR).
- Supports prefix queries.
- Supports sorted traversal.
### Disadvantages
- More complex than hash tables.
- Slightly slower than in-memory hash tables for simple lookup.
# Wildcard queries
Used when:
+ the user is uncertain of the spelling of a query term (e.g., Sydney vs. Sidney) which leads to the wildcard query S\*dney.
+ the user is aware of multiple variants of spelling a term and (consciously) seeks documents containing any of the variants (e.g., color vs. colour)
Three types:
	Prefix matching: \*nal
	Suffix matching: intern\*
	Substring matching: inte\*al
## Using B-trees
B- trees support _prefix matching_ natively.
B- tree stores all terms in $T$ in sorted order. Each leaf node contains a ptr to posting list data for the leaf node term.
```
                         [internal, interpret]
                       /             |            \
   [inter, interest]   [internal, international, internet]  [interpreting, interview]

```
For a prefix query: _intern\*_
- Binary search for the start key: `"intern"`
- Then sequential scan forward until prefix ends.

For _suffix matching_, we need to construct a reverse B-tree, since it is not normally supported by a B-tree.
	Reverse all terms: Ex: internal -> lanretni etc..
	Store reversed terms in B-tree in sorted order
	Convert suffix query to prefix query by reversing it. Ex: _*ship_ to _pihs*_ 
	Do prefix search for reversed query _pihs*_ 

For _substring matching_, use combination of B-tree and reverse B-tree.
	Ex: pro\*cent $\rightarrow$ suffix query _pro*_  $\cap$  prefix query _*cent_
## Permuterm index
Special form of an inverted index that supports all types of wildcard queries.

For every term $t_i\in T$ we:
	Append $\$$ at the end of term ($z_i$) 
	Store all rotations of the augmented term, linking it to the original term ($t_i$), in a dictionary (usually a B-tree).
Ex: Assume $t$ = "hello"
	index['hello'] = ["hello$", "ello$h", "llo$he", "lo$hel" "o$hell", "$hello"]

For any wildcard query:
	Append $\$$ at the end of the query
	rotate it such that \* appears at the end of it
	Do a prefix search
Ex: 
	X* $\rightarrow$ X*$ $\rightarrow$ $X*
	X $\rightarrow$ X$
	\*X $\rightarrow$ \*X$ $\rightarrow$ X$\*
	\*X\* $\rightarrow$ X\*
	X\*Y $\rightarrow$ X\*Y$ $\rightarrow$ Y$X\*

### ___Time and space complexity___
Let $N$ = number of terms; avg word length = $L$
Index construction time and space complexity: $O(N\times L)$
Query time: $O(log(N\times L) + K)$
### ___Disadvantages___
✘ **Huge memory overhead** (L× growth)  
✘ Slower index construction  
✘ Many rotations → large disk/memory usage  
✘ Sluggish for very frequent query patterns  
✘ Not ideal for very large vocabularies
## k-gram index
A **k-gram index** is an index that maps **all substrings of length k** (called k-grams) to the vocabulary terms that contain those k-grams.
It is much more memory-efficient than a permuterm index.

For every term $t_i\in T$ we:
	Add $\$$ at the start and end of the term 
	Extract all k-grams and map to original term $t_i$ 
Ex: $t_i$ = "cat" k=3
	cat $\rightarrow$ \$cat\$ $\rightarrow$ [$ca, cat, at$]
	 $ca $\rightarrow$ cat $\rightarrow$ carbon$\rightarrow$ ...
	 cat $\rightarrow$ cat $\rightarrow$ placate $\rightarrow$ ...
	 at$ $\rightarrow$ cat $\rightarrow$ chat $\rightarrow$ ...

For any wildcard query:
	Append $\$$ at start and end of the query
	Extract k-grams from static parts (i.e. no \*)
	Intersect lists
	Post filter to remove false positives
Ex:
	c\*r $\rightarrow$ \$c\*r\$ $\rightarrow$ $c $\cap$ r$
	mon* $\rightarrow$ \$mon\*\$ $\rightarrow$ $m $\cap$ mo $\cap$ on $\cap$ n$ (k = 2)

Why do we need post-filtering?
Because k-gram intersection only guarantees **substring containment**, not exact wildcard structure
Look at the mon* query breakdown:  _moon_ satisfies the boolean expression $m $\cap$ mo $\cap$ on $\cap$ n$ but it does not match the wildcard structure.
Thus, we must always filter at the end to remove results that don't match the wildcard structure.
### ___Advantages___
✔ Efficient wildcard lookup  
✔ Memory efficient (much smaller than permuterm index)  
✔ Supports `in*rn`, `*tion`, `a*b*c` patterns
### ___Disadvantages___
✘ Cannot answer queries directly → must post-filter  
✘ Still slower than prefix B-tree search  
✘ Higher query time than permuterm  
✘ False positives possible  
✘ No guarantee of fast performance if query pieces are very short (like single characters)
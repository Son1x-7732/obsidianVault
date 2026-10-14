Yes. And I think you're identifying the real problem correctly: **you've been successfully implementing the assignment, but you've been building it as a sequence of isolated features rather than one mental model.** After 10–15 steps, `TF`, `IDF`, `Counter`, `docmap`, `index`, BM25, etc. start becoming a pile of disconnected names.

So let's stop coding for a moment.

I want you to make a **proper handwritten reference sheet**. Don't try to memorize this. Your copy should become the map you can look at whenever you forget.

I'll explain this from **raw movie data → preprocessing → index → TF → IDF → TF-IDF → BM25 → search**, using one concrete movie throughout.

---

# PART 1 — What are we actually building?

At the highest level, you're building a **search engine for movies**.

Imagine you have 45,000 movies.

A user types:

```
animated family
```

You want your program to answer:

> Which movies are most relevant to this query?

The naive way would be:

```
for every movie:
    look through its title and description
    see whether query words occur
    somehow decide which movie is most relevant
```

But we want something much smarter and faster.

So we've been gradually building:

```
                 MOVIE DATA
                     ↓
               PREPROCESSING
                     ↓
              INVERTED INDEX
                     ↓
        ┌────────────┴────────────┐
        ↓                         ↓
   WHERE is word?             HOW MANY?
   (index)                    (TF)
        │                         │
        └────────────┬────────────┘
                     ↓
                  IDF
                     ↓
                TF-IDF
                     ↓
             BM25 improvements
                     ↓
              BM25 SEARCH
                     ↓
             RANKED MOVIES
```

**This entire course is basically one pipeline.**

Write that diagram in your copy.

---

# PART 2 — First understand your movie data

Your `movies.json` looks conceptually like this:

```
{
    "movies": [
        {
            "id": 15,
            "title": "The Adventures of Mowgli",
            "description": "A young boy grows up in the jungle with his animal friends."
        },
        {
            "id": 30,
            "title": "Day of the Animals",
            "description": "Animals attack humans in a terrifying wilderness."
        }
    ]
}
```

Obviously your actual dataset has thousands of movies.

A **movie object** is one dictionary:

```
movie = {    "id": 15,    "title": "The Adventures of Mowgli",    "description": "A young boy grows up in the jungle with his animal friends."}
```

This is extremely important.

Write this:

```
MOVIE OBJECT
────────────────────────────────
movie["id"]          → 15
movie["title"]       → "The Adventures of Mowgli"
movie["description"] → "...jungle..."
```

Whenever you see:

```
movie["title"]
```

you're saying:

> From this movie dictionary, give me the value associated with `"title"`.

---

# PART 3 — `load_movies()`

You wrote:

```
def load_movies():    with open("data/movies.json") as f:        data = json.load(f)    return data["movies"]
```

What does this do?

The JSON file is essentially:

```
data
 │
 └── "movies"
       │
       ├── movie 1
       ├── movie 2
       ├── movie 3
       └── ...
```

So:

```
data["movies"]
```

gives you the list of movie objects.

Therefore:

```
movies = load_movies()
```

gives:

```
movies
  ↓
[
    {id: ..., title: ..., description: ...},
    {id: ..., title: ..., description: ...},
    ...
]
```

Write:

```
load_movies()
    ↓
JSON file
    ↓
list of movie dictionaries
```

---

# PART 4 — Why preprocessing?

Suppose the movie says:

```
"The Brave Bear!"
```

And the user searches:

```
brave
```

We don't want the punctuation and capitalization to interfere.

So we created:

```
tokenize_text()
```

Its job is:

```
raw text
   ↓
lowercase
   ↓
remove punctuation
   ↓
split into words
   ↓
remove stopwords
   ↓
stemming
   ↓
tokens
```

Example:

```
"The Brave Bears are hunting!"
```

might become approximately:

```
["brave", "bear", "hunt"]
```

depending on the stopwords and stemmer.

---

# PART 5 — Stopwords

Some words aren't very useful for search:

```
the
is
a
an
of
and
to
...
```

These are called **stopwords**.

You load them with:

```
load_stopwords()
```

So:

```
"The bear is in the forest"
```

might become:

```
bear forest
```

instead of:

```
the bear is in the forest
```

Write:

```
STOPWORDS
= common words that provide little search meaning
```

---

# PART 6 — Stemming

Then we use:

```
PorterStemmer()
```

Why?

Because these words are related:

```
hunt
hunting
hunted
hunts
```

Stemming attempts to reduce related forms to a common stem.

So:

```
"hunting"
    ↓
"hunt"
```

This helps search.

Write:

```
STEMMING
Different word forms
        ↓
common root/stem
```

---

# PART 7 — The most important function: `tokenize_text()`

You currently have:

```
def tokenize_text(text, stopwords):    translator = str.maketrans("", "", string.punctuation)    text = text.lower()    text = text.translate(translator)    tokens = text.split()    tokens = [token for token in tokens if token not in stopwords]    tokens = [stemmer.stem(token) for token in tokens]    return tokens
```

Its **only responsibility** is:

> Convert raw text into the form that our search engine understands.

Write this in big letters:

```
RAW TEXT
   ↓
tokenize_text()
   ↓
PROCESSED TOKENS
```

Everything else in the search engine depends on this.

---

# PART 8 — Why do we need an Inverted Index?

Now imagine 50,000 movies.

You search:

```
bear
```

You don't want to scan all 50,000 descriptions.

Instead, we build a structure like:

```
index = {    "bear": {15, 30, 87, 102},    "anim": {15, 30, 50},    "hunt": {30, 87, 99}}
```

This is called an **inverted index**.

The key idea:

```
WORD → DOCUMENTS CONTAINING THAT WORD
```

Write this:

```
INVERTED INDEX

term
  ↓
set of document IDs
```

Example:

```
"bear" → {15, 30, 87}
```

means:

> Movies 15, 30 and 87 contain the processed term `"bear"`.

---

# PART 9 — Why a SET?

We use:

```
set()
```

because a document should appear only once.

Suppose movie 15 contains:

```
bear bear bear bear
```

We still want:

```
"bear" → {15}
```

NOT:

```
"bear" → {15, 15, 15, 15}
```

The number of occurrences is a **different piece of information**.

That's where TF comes in.

This distinction is extremely important:

```
INDEX
─────
WHERE does the word occur?

TF
──
HOW MANY TIMES does it occur in a document?
```

---

# PART 10 — Your three major dictionaries

This is probably the most useful thing to write in your notebook.

You have three different structures.

## ① `index`

```
self.index
```

Answers:

> Which documents contain this term?

Example:

```
{    "bear": {15, 30, 87},    "hunt": {30, 87},    "anim": {15, 30}}
```

Think:

```
TERM → DOCUMENTS
```

---

# 11 — ② `docmap`

```
self.docmap
```

Answers:

> Given a document ID, what is the actual movie?

Example:

```
{    15: {        "id": 15,        "title": "The Adventures of Mowgli",        "description": "..."    },    30: {        "id": 30,        "title": "Day of the Animals",        "description": "..."    }}
```

Think:

```
DOCUMENT ID → MOVIE OBJECT
```

This is why you can do:

```
movie = inverted_index.docmap[15]
```

and then:

```
movie["title"]
```

---

# 12 — ③ `term_frequencies`

```
self.term_frequencies
```

Answers:

> How many times does each term occur in this document?

Example:

```
{    15: Counter({        "bear": 4,        "anim": 3,        "hunt": 1    })}
```

Think:

```
DOCUMENT → TERM → COUNT
```

So:

```
self.term_frequencies[15]["bear"]
```

means:

> How many times does `"bear"` occur in movie 15?

Answer:

```
4
```

---

# PART 13 — One movie through the entire system

This is the example you specifically asked for.

Let's pretend movie 15 is:

```
movie = {    "id": 15,    "title": "The Brave Bear",    "description": "A brave bear goes hunting with other bears."}
```

First we combine title + description:

```
text = f"{movie['title']} {movie['description']}"
```

We get:

```
The Brave Bear A brave bear goes hunting with other bears.
```

After tokenization, imagine we get:

```
tokens = [    "brave",    "bear",    "brave",    "bear",    "go",    "hunt",    "other",    "bear"]
```

Now watch what happens.

---

## `index`

For `"bear"`:

```
index["bear"].add(15)
```

So:

```
index["bear"]
```

could become:

```
{15, 30, 87}
```

This tells us:

> Movie 15 contains bear.

It doesn't tell us how many times.

---

## `term_frequencies`

We count:

```
bear → 3
brave → 2
go → 1
hunt → 1
other → 1
```

So:

```
term_frequencies[15] = Counter({    "bear": 3,    "brave": 2,    "go": 1,    "hunt": 1,    "other": 1})
```

Now:

```
get_tf(15, "bear")
```

returns:

```
3
```

---

## `doc_lengths`

We also count:

```
len(tokens)
```

which is:

```
8
```

So:

```
doc_lengths[15] = 8
```

This answers:

> How long is document 15?

---

# PART 14 — Now we get to TF

**TF = Term Frequency.**

TF answers:

> How many times does this term occur in this particular document?

For our movie:

```
movie 15
bear → 3
brave → 2
hunt → 1
```

So:

```
TF(15, "bear") = 3
```

Write:

```
TF
=
HOW MANY TIMES does a term occur
INSIDE ONE DOCUMENT?
```

---

# PART 15 — Why isn't TF enough?

Suppose:

```
Movie A:
bear bear bear bear bear

Movie B:
bear hunting guide for beginners
```

Raw TF says:

```
A → bear = 5
B → bear = 1
```

So TF strongly favors A.

But maybe B is actually more relevant to someone searching:

```
bear hunting
```

That's one reason BM25 eventually improves TF.

But there's another problem.

---

# PART 16 — IDF

**IDF = Inverse Document Frequency.**

This asks a completely different question.

TF asks:

> How common is the term **inside this document**?

IDF asks:

> How common is the term **across the entire collection of documents**?

This distinction is worth writing in a box:

```
TF
────────────────────
Within ONE document
"How many times?"
────────────────────

IDF
────────────────────
Across ALL documents
"How rare/common?"
────────────────────
```

---

# PART 17 — Why do we need IDF?

Imagine your dataset has 10,000 movies.

Term:

```
"movie"
```

might appear in:

```
8,000 movies
```

It's not very useful for distinguishing movies.

But:

```
"mowgli"
```

might appear in:

```
15 movies
```

That's much more informative.

Therefore:

```
COMMON TERM
→ low IDF

RARE TERM
→ high IDF
```

Write that.

---

# PART 18 — Your original TF-IDF

Your IDF formula was:

\[ IDF = \log\left(\frac{N+1}{df+1}\right) \]

where:

```
N  = total number of documents
df = number of documents containing the term
```

For example:

```
N = 10,000

"bear" appears in 1,000 documents
df = 1,000
```

versus:

```
"mowgli" appears in 15 documents
df = 15
```

Mowgli gets a higher IDF.

---

# PART 19 — TF-IDF combines them

Now you finally combine the two ideas:

\[ TF\text{-}IDF = TF \times IDF \]

This means:

```
TF
↓
Is this word important INSIDE this document?

×

IDF
↓
Is this word rare/useful ACROSS the collection?

=

TF-IDF
↓
How important is this term in this document?
```

This is the first real **ranking score** you built.

---

# PART 20 — Why BM25?

TF-IDF has weaknesses.

### Problem 1 — TF grows too much

If:

```
TF = 1
```

and:

```
TF = 100
```

TF gives 100× more weight.

BM25 says:

> Additional occurrences should give diminishing returns.

That's **TF saturation**.

---

# PART 21 — BM25 TF saturation

You implemented:

\[ BM25TF = \frac{TF(k_1+1)} {TF+k_1} \]

with:

```
k1 = 1.5
```

So:

```
raw TF     BM25 TF
-------------------
1          1.00
2          1.43
5          1.92
10         2.17
20         2.33
```

Write:

```
BM25 TF
= TF with diminishing returns
```

---

# PART 22 — Problem 2: long documents

A long document naturally has more opportunities for words to appear.

So a 2,000-word document shouldn't automatically beat a 100-word document just because it has more occurrences.

That's why you added:

```
doc_lengths
```

and:

```
__get_avg_doc_length()
```

---

# PART 23 — Document length normalization

You calculate:

\[ length\_norm = 1-b+b\left(\frac{docLength}{avgDocLength}\right) \]

where:

```
b = 0.75
```

Then BM25 TF becomes:

\[ BM25TF = \frac{TF(k_1+1)} {TF+k_1(lengthNorm)} \]

So:

```
long document
→ length_norm > 1
→ denominator increases
→ score decreases

short document
→ length_norm < 1
→ denominator decreases
→ score increases
```

Write exactly that.

---

# PART 24 — BM25 IDF

You also replaced your old IDF with BM25's version:

\[ BM25IDF = \log\left( \frac{N-df+0.5}{df+0.5}+1 \right) \]

Again:

```
N  = total documents
df = documents containing the term
```

The important point is:

**BM25 IDF is still about rarity across documents.**

It has nothing to do with how many times the word occurs in one document.

---

# PART 25 — Finally: BM25

Now everything comes together.

For **one document + one term**:

\[ BM25 = BM25TF \times BM25IDF \]

That's the method you are currently adding:

```
def bm25(self, doc_id, term):    bm25_tf = self.get_bm25_tf(doc_id, term)    bm25_idf = self.get_bm25_idf(term)    return bm25_tf * bm25_idf
```

Think:

```
                  ONE TERM
                     │
          ┌──────────┴──────────┐
          ↓                     ↓
       BM25 TF              BM25 IDF
          │                     │
          │                 rarity across
          │                  all documents
          │
     frequency +
     saturation +
     document length
          │                     │
          └──────────┬──────────┘
                     ↓
              MULTIPLY THEM
                     ↓
               BM25 SCORE
```

---

# PART 26 — What about a multi-word query?

Suppose the user searches:

```
animated family
```

After preprocessing:

```
["anim", "famili"]
```

We calculate:

```
BM25(document, "anim")
+
BM25(document, "famili")
```

For example:

```
Movie 15:

BM25(15, "anim")   = 3.8
BM25(15, "famili") = 3.4

Total = 7.2
```

Another movie:

```
Movie 30:

BM25(30, "anim")   = 4.1
BM25(30, "famili") = 3.0

Total = 7.1
```

Then:

```
Movie 15 → 7.2
Movie 30 → 7.1
```

Movie 15 ranks higher.

**This is what `bm25_search()` is doing.**

---

# PART 27 — Your entire data model

This is the page I REALLY want you to write down.

## The four things stored by `InvertedIndex`

### 1. `index`

```
TERM → SET OF DOCUMENT IDs

"bear" → {15, 30, 87}
```

Purpose:

> WHERE does this term occur?

---

### 2. `docmap`

```
DOCUMENT ID → MOVIE OBJECT

15 → {
    "id": 15,
    "title": "...",
    "description": "..."
}
```

Purpose:

> WHAT is document 15?

---

### 3. `term_frequencies`

```
DOCUMENT ID → TERM → COUNT

15 → {
    "bear": 3,
    "brave": 2,
    "hunt": 1
}
```

Purpose:

> HOW MANY TIMES does the term occur in this document?

---

### 4. `doc_lengths`

```
DOCUMENT ID → TOTAL TOKEN COUNT

15 → 8
30 → 15
87 → 6
```

Purpose:

> HOW LONG is this document?

---

# PART 28 — Then the functions make sense

Write this table.

|Function|Question it answers|
|---|---|
|`load_movies()`|Where is my raw movie data?|
|`load_stopwords()`|Which common words should I ignore?|
|`tokenize_text()`|How do I turn text into searchable tokens?|
|`tokenize_single_term()`|Give me exactly one processed term|
|`build()`|Build all my search data|
|`__add_document()`|Add one movie to the index|
|`get_documents(term)`|Which documents contain this term?|
|`get_tf(doc, term)`|How many times does this term occur here?|
|`get_bm25_idf(term)`|How rare is this term across all documents?|
|`get_bm25_tf(doc, term)`|What is its saturated/normalized frequency here?|
|`bm25(doc, term)`|What is this term's BM25 score in this document?|
|`bm25_search(query)`|Which documents best match the whole query?|

---

# PART 29 — The entire project in one example

Let's say the user types:

```
bear hunting
```

### Step 1 — raw query

```
"bear hunting"
```

### Step 2 — tokenize

```
["bear", "hunt"]
```

### Step 3 — find documents

Using:

```
index["bear"]index["hunt"]
```

Maybe:

```
bear → {15, 30, 50}
hunt → {30, 50, 90}
```

---

### Step 4 — for document 30

Get:

```
tf_bear = get_tf(30, "bear")tf_hunt = get_tf(30, "hunt")
```

Suppose:

```
bear = 4
hunt = 2
```

---

### Step 5 — normalize TF

```
get_bm25_tf(30, "bear")
get_bm25_tf(30, "hunt")
```

Maybe:

```
bear → 1.8
hunt → 1.4
```

---

### Step 6 — calculate rarity

```
get_bm25_idf("bear")
get_bm25_idf("hunt")
```

Maybe:

```
bear → 2.0
hunt → 3.0
```

---

### Step 7 — calculate individual BM25

```
bear:
1.8 × 2.0 = 3.6

hunt:
1.4 × 3.0 = 4.2
```

---

### Step 8 — total query score

```
3.6 + 4.2 = 7.8
```

So:

```
Document 30 → BM25 score = 7.8
```

Do this for every document.

---

### Step 9 — sort

```
Document 30 → 7.80
Document 50 → 7.31
Document 15 → 6.92
Document 90 → 5.44
```

---

### Step 10 — top 5

Return the highest scoring movies.

That's a search engine.

---

# PART 30 — What your code has actually accomplished

You've gone from:

```
"I have a JSON file containing movies"
```

to:

```
"I can rank movies according to how relevant they are to a query."
```

Specifically, you've implemented:

### Foundation

- JSON loading
- stopword loading
- tokenization
- punctuation removal
- lowercasing
- stemming

### Search infrastructure

- inverted index
- document map
- cached index
- cached document map

### TF-IDF

- term frequencies
- document frequency
- TF
- IDF
- TF-IDF

### BM25 components

- BM25 IDF
- TF saturation using `k1`
- document length normalization using `b`
- document lengths
- average document length
- BM25 TF

### Current final step

You're now at:

```
BM25 TF
   +
BM25 IDF
   ↓
BM25 score
   ↓
BM25 search/ranking
```

---

# PART 31 — Your handwritten cheat sheet

If I were you, I'd actually write these **pages** in your notebook.

## Page 1 — Project purpose

```
RAG/Search Engine Assignment
─────────────────────────────

Goal:
Given a movie query, find and rank relevant movies.

PIPELINE:

Movie JSON
    ↓
Tokenization
    ↓
Inverted Index
    ↓
TF + IDF
    ↓
TF-IDF
    ↓
BM25 improvements
    ↓
BM25
    ↓
Ranking
```

---

## Page 2 — Data structures

```
index:
TERM → DOCUMENT IDs

docmap:
DOCUMENT ID → MOVIE OBJECT

term_frequencies:
DOCUMENT ID → TERM → COUNT

doc_lengths:
DOCUMENT ID → TOTAL TOKEN COUNT
```

Then your concrete example:

```
movie = {    "id": 15,    "title": "The Brave Bear",    "description": "A brave bear goes hunting..."}
```

And:

```
docmap[15]
→ movie object

index["bear"]
→ {15, 30, 87}

term_frequencies[15]["bear"]
→ 3

doc_lengths[15]
→ 8
```

---

## Page 3 — TF vs IDF

Write this **exactly**:

```
TF = importance within ONE document

TF(term, document)
= how many times term occurs in document


IDF = importance across ALL documents

IDF(term)
= how rare term is across collection
```

Then:

```
TF high
→ term is important in this document

IDF high
→ term is rare across documents
```

---

## Page 4 — TF-IDF

```
TF-IDF = TF × IDF
```

Purpose:

```
high TF
+
high IDF
=
important term for this document
```

---

## Page 5 — BM25

```
BM25 = BM25_TF × BM25_IDF
```

BM25 TF:

```
Raw TF
 ↓
Saturation (k1)
 ↓
Document length normalization (b)
 ↓
BM25 TF
```

BM25 IDF:

```
Document frequency
 ↓
BM25 IDF
```

Then:

```
BM25 TF × BM25 IDF
        ↓
   BM25 score
```

---

## Page 6 — Query ranking

```
Query:
"animated family"

      ↓ tokenize

["anim", "famili"]

      ↓

For EACH document:

BM25(doc, "anim")
+
BM25(doc, "famili")

      ↓

total score

      ↓

sort highest → lowest

      ↓

top N movies
```

---

# And NOW you should move to the next assignment

You're currently at the exact point where the pieces are ready to be combined.

Before writing more code, I strongly recommend you **actually write these six pages in your copy**. Don't copy every implementation detail—write the _questions each structure/function answers_. That's what will let you reconstruct the code later instead of memorizing it.

Then the next coding step is very small conceptually:

```
def bm25(self, doc_id, term):    ...
```

which means:

> **For ONE document and ONE term, combine my BM25 TF and BM25 IDF.**

Then:

```
def bm25_search(self, query, limit):    ...
```

means:

> **For EVERY document, calculate the score for EVERY query term, add them, sort them, and return the best ones.**

That's the culmination of essentially everything you've built so far.
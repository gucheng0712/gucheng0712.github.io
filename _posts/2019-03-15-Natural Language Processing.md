---
layout: post
title:  "Natural Language Processing (ML)"
date:   2019-3-15
categories: [Python, Ai]
tags: [Python, Ai]
icon: fa-code
---
# **What is Natural Language Processing?**

`Natural Language Processing (NLP)` refers to the ability of `machines` to `understand` and explain the way human `write and speak`.

It is widely used in information retrieval, sentiment analysis, information extraction, machine translation, and more.

>**Operations of NLP include**:

1. `Tokenization`
2. `Stopword removal`
3. `Stemming and Lemmatization`
4. `Part-of-speech tagging`
5. `Named entity recognition`


<p align="center"> <img src="/static/assets/img/blog/nlp.jpg" width="70%"></p>

---
## **Tokenization**

`Tokenization` is a process of understanding a sentence or a phrase by `splitting` them into substrings, words and punctuation.

<p align="center"> <img src="/static/assets/img/blog/token.jpg" width="40%"></p>

> **Code Example:**

```python
from nltk.tokenize import word_tokenize, sent_tokenize

string = "The science of today is the technology of tomorrow."

# Split the sentence into words
tokenized_words = word_tokenize(string)
print("Word Tokenization: ", tokenized_words)

# Back to the scentence from words
print("Sentence Tokenization: ",sent_tokenize(string))
```

> **Output**:

```
Word Tokenization: ['The', 'science', 'of', 'today', 'is', 'the', 'technology', 'of', 'tomorrow', '.']

Sentence Tokenization: ['The science of today is the technology of tomorrow.']
```

---
## **Stopword Removal**

`Stopword Removal` involves `filtering out` words which do not contain significant information. Mostly these are words that are often used as connective tissue such as `as, the, be, are` etc.

<p align="center"> <img src="/static/assets/img/blog/swr.jpg" width="40%"></p>

> **Code Example:**

```python
from nltk.corpus import stopwords

string = "The science of today is the technology of tomorrow."
# Load stop words
stop_words = stopwords.words( 'english')
# Remove stop words
nonstop_words = []
for word in tokenized_words:
    if word not in stop_words:
        nonstop_words.append(word)
print('Nonstop Words: ', nonstop_words) 

```

> **Output**:

```
Nonstop Words:  ['The', 'science', 'today', 'technology', 'tomorrow', '.']
```


---
## **Stemming**

`Stemming` reduces `inflection` in words to their root forms by `removing` the `suffixes or prefixes` used with a word. As a result, stemming a word or sentence may `result` in words that are `not actual words`.

<p align="center"> <img src="/static/assets/img/blog/stemming.jpg" width="40%"></p>

> **Code Example:**

```python
from nltk.tokenize import word_tokenize
from nltk.stem.porter import PorterStemmer

string = "The science of today is the technology of tomorrow."
input_words = word_tokenize(string)

# Create stemmer
porter = PorterStemmer()

# Apply stemmer to each word    
[porter.stem(word) for word in tokenized_words]

```

> **Output**:

```
Words after Stemming:  ['the', 'scienc', 'of', 'today', 'is', 'the', 'technolog', 'of', 'tomorrow', '.']
```

---
## **Lemmatization**

`Lemmatization`, unlike Stemming, reduces the inflected words properly ensuring that the `root` word `belongs` to the language. For example, runs, running, ran are all forms of the word run, therefore run is the lemma (or dictionary form) of all these words.

<p align="center"> <img src="/static/assets/img/blog/lemmatization.jpg" width="40%"></p>

> **Code Example:**

```python
from nltk.tokenize import word_tokenize
from nltk.stem import WordNetLemmatizer

string = "The sciences of today are tomorrow's technologies."
input_words = word_tokenize(string)

# Create lemmatizer
lemmatizer = WordNetLemmatizer()

# Apply lemmatizer to each word
[lemmatizer.lemmatize(word) for word in tokenized_words]

```

> **Output**:

```
Words after Lemmatization:  ['The', 'science', 'of', 'today', 'is', 'the', 'technology', 'of', omorrow', '.']
```

---

>**End --Cheng Gu**


---
title: "CS50-AI Week 6. Project 2: Questions"
permalink: /_pages/cs50/week6-questions
tags:
  - cs50
  - Python
toc: true
---

```python
import nltk
import sys
import os
from math import log

FILE_MATCHES = 1
SENTENCE_MATCHES = 1


def main():

    # Check command-line arguments
    if len(sys.argv) != 2:
        sys.exit("Usage: python questions.py corpus")

    # Calculate IDF values across files
    files = load_files(sys.argv[1])
    file_words = {
        filename: tokenize(files[filename])
        for filename in files
    }
    file_idfs = compute_idfs(file_words)

    # Prompt user for query
    query = set(tokenize(input("Query: ")))

    # Determine top file matches according to TF-IDF
    filenames = top_files(query, file_words, file_idfs, n=FILE_MATCHES)

    # Extract sentences from top files
    sentences = dict()
    for filename in filenames:
        for passage in files[filename].split("\n"):
            for sentence in nltk.sent_tokenize(passage):
                tokens = tokenize(sentence)
                if tokens:
                    sentences[sentence] = tokens

    # Compute IDF values across sentences
    idfs = compute_idfs(sentences)

    # Determine top sentence matches
    matches = top_sentences(query, sentences, idfs, n=SENTENCE_MATCHES)
    for match in matches:
        print(match)


def load_files(directory):
    """
    Given a directory name, return a dictionary mapping the filename of each
    `.txt` file inside that directory to the file's contents as a string.
    """

    filenames = dict()

    for file in os.listdir(directory):
        with open(os.path.join(directory, file), encoding="utf-8") as content:
            filenames[file] = content.read()

    return filenames


def tokenize(document):
    """
    Given a document (represented as a string), return a list of all of the
    words in that document, in order.
    Process document by coverting all words to lowercase, and removing any
    punctuation or English stopwords.
    """

    results = []
    punctuation = [',', '.', '"', ';',
                   '(', ')', ':', '``', '`', "''", "'", '=', '%']

    for token in nltk.word_tokenize(document.lower()):
        if token.lower() not in punctuation and token.lower(
        ) not in nltk.corpus.stopwords.words("english"):
            results.append(token.lower())

    return results


def compute_idfs(documents):
    """
    Given a dictionary of `documents` that maps names of documents to a list
    of words, return a dictionary that maps words to their IDF values.
    Any word that appears in at least one of the documents should be in the
    resulting dictionary.
    """

    idf = {}
    count = {}

    for document in documents:

        for words in set(documents[document]):
            if words not in count:
                count[words] = 1
            else:
                count[words] += 1

    for word in count:
        idf[word] = log(len(documents) / count[word])

    return idf


def top_files(query, files, idfs, n):
    """
    Given a `query` (a set of words), `files` (a dictionary mapping names of
    files to a list of their words), and `idfs` (a dictionary mapping words
    to their IDF values), return a list of the filenames of the the `n` top
    files that match the query, ranked according to tf-idf.
    """

    result = {}

    for file, content in files.items():
        value = 0

        for word in query:
            if word in content:
                value += word.count(word) * idfs[word]

        if value != 0:
            result[file] = value

    return [x[0]
            for x in sorted(result.items(), key=lambda x: x[1], reverse=True)][:n]


def top_sentences(query, sentences, idfs, n):
    """
    Given a `query` (a set of words), `sentences` (a dictionary mapping
    sentences to a list of their words), and `idfs` (a dictionary mapping words
    to their IDF values), return a list of the `n` top sentences that match
    the query, ranked according to idf. If there are ties, preference should
    be given to sentences that have a higher query term density.
    """

    output = []

    for sentence in sentences:
        # sentence, idf and density
        scores = [sentence, 0, 0]

        for word in query:
            if word in sentences[sentence]:
                # idfs
                scores[1] += idfs[word]

                # density
                scores[2] += sentences[sentence].count(
                    word) / len(sentences[sentence])

        output.append(scores)

    output.sort(key=lambda x: (x[1], x[2]), reverse=True)

    top_sentence = []
    for i in range(n):
        top_sentence .append(output[i][0])
    return top_sentence


if __name__ == "__main__":
    main()
```

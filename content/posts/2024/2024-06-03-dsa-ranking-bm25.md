---
title: "Ranking: BM25+"
author: [ "Denis Nutiu" ]
date: '2024-06-03'
categories:
  - "Kotlin"
  - "Data Structures & Algorithms"
permalink: /2024/04/17/the-linked-list
# image: /hugo-content/2024-06/dsa-ranking.jpg
tags:
  - ranking
  - bm25+
  - search
  - search engine
  - algorithms
  - kotlin
layout: post
published: false
---

# Introduction

The BM25+ is a ranking algorithm used to `rank` text documents based on a relevance score.

It is used in search engines to determine which documents are the most relevant given a terms query.

The algorithm implemented in this article is based
on
the [Improvements to BM25 and Language Models Examined](https://www.cs.otago.ac.nz/homepages/andrew/papers/2014-2.pdf)
paper.

# Implementation In Kotlin

The algorithm
is [implemented in Kotlin](https://github.com/dnutiu/dsa/blob/master/src/main/kotlin/ranking/bm25/Bm25Plus.kt) and
is accompanied by [unit-tests](https://github.com/dnutiu/dsa/blob/master/src/test/kotlin/ranking/bm25/BM25PlusTest.kt).



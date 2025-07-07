# elasticsearch-ondemand

# Module 3: Search

## 1. Full Text Queries

In this lesson, you will learn how to search using full text queries. You will also learn how a score is calculated for each document that is a hit and by default, the ‘hits’ in the query response are sorted by this score. Watch the video below to get started.

By the end of this lesson, you will be able to:

- Define a query that searches for one or more text fields using the Query DSL
- Identify how the query response is affected byt the document score

```
**QUESTION 1:** The way the multi_match query is executed depends on the type parameter. For example, setting “type”: “phrase” runs a match_phrase.
**ANSWER:** True. The type parameter specifies what type of query is executed for multi_match.

**QUESTION 2:** Search results are always sorted by score.
**ANSWER:** False. You can change the sort order with the sort parameter.

**QUESTION 3:** What is the default number of hits returned from a query using the Query DSL?
**ANSWER:** By default, 10 hits are returned. You can change this using the size parameter.
```

## 2. Term-level Queries

In this lesson, you’ll learn how to define term-level queries that return an exact match of the original string as it occurs in the documents. You will also implement queries to search for numbers, dates, and IPs by creating ranges. Finally, at the end of this lesson, you will perform a sync search to handle long-running searches. Watch the video below to learn more.



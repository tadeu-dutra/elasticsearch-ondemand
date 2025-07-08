# elasticsearch-ondemand

# Module 3: Search

## 3.1 Full Text Queries

In this lesson, you will learn how to search using full text queries. You will also learn how a score is calculated for each document that is a hit and by default, the ‘hits’ in the query response are sorted by this score. Watch the video below to get started.

By the end of this lesson, you will be able to:

- Define a query that searches for one or more text fields using the Query DSL
- Identify how the query response is affected byt the document score

Ex: match, match_phrase, multi_match, query_string, ...

```
**QUESTION 1:** The way the multi_match query is executed depends on the type parameter. For example, setting “type”: “phrase” runs a match_phrase.
**ANSWER:** True. The type parameter specifies what type of query is executed for multi_match.

**QUESTION 2:** Search results are always sorted by score.
**ANSWER:** False. You can change the sort order with the sort parameter.

**QUESTION 3:** What is the default number of hits returned from a query using the Query DSL?
**ANSWER:** By default, 10 hits are returned. You can change this using the size parameter.
```

## 3.2 Term-level Queries

In this lesson, you’ll learn how to define term-level queries that return an exact match of the original string as it occurs in the documents. You will also implement queries to search for numbers, dates, and IPs by creating ranges. Finally, at the end of this lesson, you will perform a sync search to handle long-running searches. Watch the video below to learn more.

By the end of this lesson, you will be able to:

- Define term-level queries to find documents based on exact terms
- Perform an async search to handle long-running searches

Ex: term, range, exists, fuzzy, regexp, wildcard, ids, ...

```
**QUESTION 1:** The Query DSL can only be used for full text queries.
**ANSWER:** False. The Query DSL enables you to do any kind of query.

**QUESTION 2:** In an asynchronous search, is_partial is always set to true while the query is being executed.
**ANSWER:** True. While the query is being executed, is_partial is set to true.

**QUESTION 3:** In a range query, you can set up open-ended rangeds such as "price less than $50" or dates greater than or equal to January 1st, 2025.
**ANSWER:** True. Range queries allow you to create exact, relative, or open-ended ranges.
```

## 3.3 Combining Queries

In this lesson, you'll learn how to create more complex search requests using the bool query to combine one or more Boolean clauses.

By the end of this lesson, you'll be able to:

- Combine multiple queries using one or more boolean clauses
- Identify which boolean clauses contribute to a document's score
- Describe the importance of using filters in your queries

The "bool" Query combines one or more boolean clauses: must, filter, must_not, should.
- **must** clause must match a document attribute to be a hit and it does contribute to the score. Ex: Find all blogs with content agent and locale en-us
- **filter** clause must match a document attribute to be a hit and it doesn't contribute to the score (usually used for yes/no type queries). Ex: Find all blogs with content agent and locale en-us (not scored)
- **must_not** is used to exclude documents that match a query and it doesn't contribute to the score. Ex: Finds all blogs that mention agent in the content field that are not written in English
- **should** used to boost documents that match a query.
  - Queries in a **should** clause contribute to the score
  - Documents that do not match the queries in a **should** clause are returned as hits too
  - Use **minimum_should_match** to specify the number or percentage of should clauses returned
 
Always try to use **filter** as much as possible, skipping score calculation will speed up a search query

```
**QUESTION 1:** The must clause is the only required clause in a bool query.
**ANSWER:** False. All clauses are optional when using a bool query.

**QUESTION 2:** If a document matches a filter clause, the score is increased by a factor of 2.
**ANSWER:** False. A filter clause has no effect on a document's score.

**QUESTION 3:** Would the following query be best searched in a query context or filter context? "match": {"locale": "en-us"}
**ANSWER:** Filte context. You are looking for an exact match, which is a yes or no type query. 
```

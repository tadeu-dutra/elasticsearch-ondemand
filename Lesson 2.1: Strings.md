# Lesson 2.1: Strings

In this lesson, you’ll learn how Elasticsearch handles strings in your documents and how text is analyzed using an analyzer. You will also learn the differences between two data types: Strings vs Keywords.

In this lab, you will learn about the differences between text and keyword field data types by performing the following tasks:

    Create and compare match queries on an index
    Use the _analyze API to understand how text analysis works
    Examine how text and keyword data field types affect the ability to sort data in Discover

## a) Query blogs index

Write a match query to search the blogs index for Steve in the authors.first_name field. How many hits does the query return?

REQUEST:

```
GET blogs/_search
{
  "query": {
    "match": {
      "authors.first_name": "Steve"
    }
  }
}
```

RESPONSE:

```
{
  "took": 28,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 33,
      "relation": "eq"
    },
    "max_score": 5.303483,
    "hits": [⇔]
  }
}
```

## b) Update Query Text

Update the previous query to search for steve instead of Steve. How many hits does the query return, and why?

REQUEST:

```
GET blogs/_search
{
  "query": {
    "match": {
      "authors.first_name": "steve"
    }
  }
}
```

RESPONSE:

```
{
  "took": 23,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 33,
      "relation": "eq"
    },
    "max_score": 5.303483,
    "hits": [⇔]
  }
}
```
NOTE: Both queries are run on the authors.first_name field, which is a text data type. Text field types are analyzed, and all terms in the string are lowercase.

## c) Update Query Field

Update the query to use the authors.first_name.keyword field instead of the authors.first_name field. How many hits does the query return, and why?

REQUEST:

```
GET blogs/_search
{
  "query": {
    "match": {
      "authors.first_name.keyword": "steve"
    }
  }
}
```

RESPONSE:

```
{
  "took": 3,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 0,
      "relation": "eq"
    },
    "max_score": null,
    "hits": []
  }
}
```
NOTE: The query is run on the authors.first_name.keyword field, which is a keyword data type. Keyword field types are not analyzed and return only exact matches. Though, the query should return 0 hits. 

## d) Update Query for Result

Using the authors.first_name.keyword field, update the query to return the same amount of hits as the first query.

REQUEST:

```
GET blogs/_search
{
  "query": {
    "match": {
      "authors.first_name.keyword": "Steve"
    }
  }
}
```

RESPONSE:

```
{
  "took": 10,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 33,
      "relation": "eq"
    },
    "max_score": 5.2560606,
    "hits": [⇔\
  }
}
```
NOTE: Capitalizing the name Steve in the query should return the same amount of hits as the first query.

## e) Analyze API

Use the Analyze API to see how the standard analyzer affects the string United Kingdom. What tokens are output by the request?

REQUEST:

```
GET _analyze
{
  "text": "United Kingdom",
  "analyzer": "standard"
}
```

RESPONSE:

```
{
  "tokens": [
    {
      "token": "united",
      "start_offset": 0,
      "end_offset": 6,
      "type": "<ALPHANUM>",
      "position": 0
    },
    {
      "token": "kingdom",
      "start_offset": 7,
      "end_offset": 14,
      "type": "<ALPHANUM>",
      "position": 1
    }
  ]
}
```
NOTE: Two tokens are output, united and kingdom.

## f) Standard versus English Analyzer

Compare the output of the Analyze API on the string Nodes and Shards using the standard analyzer versus the english analyzer. What tokens are output by each analyzer?

REQUEST (Standard)

```
GET _analyze
{
  "text": "Nodes and Shards",
  "analyzer": "standard"
}
```

RESPONSE (Standard)

```
{
  "tokens": [
    {
      "token": "nodes",
      "start_offset": 0,
      "end_offset": 5,
      "type": "<ALPHANUM>",
      "position": 0
    },
    {
      "token": "and",
      "start_offset": 6,
      "end_offset": 9,
      "type": "<ALPHANUM>",
      "position": 1
    },
    {
      "token": "shards",
      "start_offset": 10,
      "end_offset": 16,
      "type": "<ALPHANUM>",
      "position": 2
    }
  ]
}
```
NOTE: There are three tokens output from the standard analyzer: nodes, and, and shards.
The standard analyzer only lowercased all tokens

REQUEST (English)
```
GET _analyze
{
  "text": "Nodes and Shards",
  "analyzer": "english"
}
```

RESPONSE (English)
```
{
  "tokens": [
    {
      "token": "node",
      "start_offset": 0,
      "end_offset": 5,
      "type": "<ALPHANUM>",
      "position": 0
    },
    {
      "token": "shard",
      "start_offset": 10,
      "end_offset": 16,
      "type": "<ALPHANUM>",
      "position": 2
    }
  ]
}
```
NOTE: The english analyzer only outputs two tokens: node and shard.
The english analyzer:
1. Lowercased all tokens
2. Removed stop words, in this case and
3. Stemmed the tokens nodes and shards, reducing them to their root form


# Summary

In this lab, you learned about the differences between text and keyword field data types by performing the following tasks:

    Create and compare match queries on an index
    Use the _analyze API to understand how text analysis works
    Examine how text and keyword data field types affect the ability to sort data in Discover

# Review

- By default, text strings are analyzed by Elasticsearch at query time.
- Unlike text strings, keywords are not analyzed.
- The standard analyzer is the default analyzer which is used if no other analyzer is specified
- The authors.first_name field is a text data type. Text field types cannot be sorted.
- Unlike the authors.first_name field, the authors.first_name.keyword field is a keyword data type. Keyword field types can be sorted.


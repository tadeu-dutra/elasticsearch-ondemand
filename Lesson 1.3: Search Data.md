# 1.3: Search Data

In this lab, you will explore different ways to retrieve documents from the blogs index by performing the following tasks:

    Compare the number of results for various Discover queries
    Create a request with Query DSL that matches all documents, giving them all a _score of 1
    Create a request with Query DSL that retrieves the date when the first blog was published
    Create a request with ES|QL that retrieves the top ten authors from the blogs index

## Run more specific query in Kibana

Make the query more specific and only search for blogs that have the word certification in the title. Do this by entering title:certification in the query bar and running the query.

## Match All Query
Write and run a match_all query on the blogs index. What value is returned for total hits?

RESPONSE:

```
GET blogs/_search
{
  "query": {
    "match_all": {}
  }
}
```

RESPONSE:

```
{
  "took": 6,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 3687,
      "relation": "eq"
    },
    "max_score": 1,
    "hits": [⇔]
  }
}
```

##  Match All Query by Default

Modify and rerun the query so it removes the body and runs the match_all query by default

REQUEST:

```
GET blogs/_search
```

RESPONSE:

```
{
  "took": 22,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 3687,
      "relation": "eq"
    },
    "max_score": 1,
    "hits": [
      {⇔},
      {⇔},
      {⇔},
      {⇔},
      {⇔},
      {⇔},
      {⇔},
      {⇔},
      {⇔},
      {⇔}
    ]
  }
}
```
NOTE: The search API shows the content of the first ten hits in the hits.hits section by default.

## Aggregate

Run the following aggregation in attempt to find the date when the first blog was published.

RESPONSE:

```
GET blogs/_search
{
  "aggs": {
    "first_blog": {
      "min": {
        "field": "publish_date"
      }
    }
  }
}
```

RESPONSE:

```
{
  "took": 18,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 3687,
      "relation": "eq"
    },
    "max_score": 1,
    "hits": [
      {⇔},
      {⇔},
      {⇔},
      {⇔},
      {⇔},
      {⇔},
      {⇔},
      {⇔},
      {⇔},
      {⇔}
    ]
  },
  "aggregations": {
    "first_blog": {
      "value": 1265658554000,
      "value_as_string": "2010-02-08T19:49:14.000Z"
    }
  }
}
```
NOTE: Since the match_all query returns the first ten hits by default, it is more difficult to see when the first blog was published. 

## Aggregation with Size Parameter

Modify and rerun the aggregation to add the size parameter and set it to 0. When was the first blog published?

REQUEST:

```
GET blogs/_search
{
  "size": 0,
  "aggs": {
    "first_blog": {
      "min": {
        "field": "publish_date"
      }
    }
  }
}
```

RESPONSE:

```
{
  "took": 4,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 3687,
      "relation": "eq"
    },
    "max_score": null,
    "hits": []
  },
  "aggregations": {
    "first_blog": {
      "value": 1265658554000,
      "value_as_string": "2010-02-08T19:49:14.000Z"
    }
  }
}
```
NOTE: Setting size to 0 to the request means it will not return any hits, making it easier to read the aggregation result.

## Query or Aggregation

If you wanted to know which 10 authors have written the most blog posts, would you use a query or an aggregation to find out, and why? You would use an aggregation because you want to know something about a bunch of documents.

## ES|QL Request

You can also use ES|QL to write aggregations.

Run an ES|QL request to retrieve the top 10 authors.

REQUEST:

```
POST /_query
{
    "query": """
    FROM blogs
    | STATS count = COUNT(*) BY authors.title
    | SORT count DESC
    | LIMIT 10
    """
}
```

RESPONSE:

```
{
  "columns": [
    {
      "name": "count",
      "type": "long"
    },
    {
      "name": "authors.title",
      "type": "text"
    }
  ],
  "values": [
    [
      181,
      "Clinton Gormley"
    ],
    [
      151,
      "Elastic Culture"
    ],
    [
      127,
      "Shay Banon"
    ],
    [⇔],
    [⇔],
    [⇔],
    [⇔],
    [⇔],
    [⇔],
    [⇔],
    [⇔],
    [⇔],
    [⇔]
  ]
}
```
NOTE: In ES|QL you have different format options to make it even easier to read the query response.

## ES|QL Query with Format

Modify and rerun the query to use the txt format option.

REQUEST:

```
POST /_query?format=txt
{
    "query": """
    FROM blogs
    | STATS count = COUNT(*) BY authors.title
    | SORT count DESC
    | LIMIT 10
    """
}
```

RESPONSE:

```
    count     |  authors.title   
---------------+------------------
181            |Clinton Gormley   
151            |Elastic Culture   
127            |Shay Banon        
112            |Alexander Reelsen 
97             |Adrien Grand      
83             |Michael McCandless
66             |Zachary Tong      
58             |Livia Froelicher  
56             |Yannick Welsch    
54             |Suyog Rao         
```

# Summary

In this lab, you explored different ways to retrieve documents from the blogs index by performing the following tasks:

    Compare the number of results for various Discover queries
    Create a request with Query DSL that matches all documents, giving them all a _score of 1
    Create a request with Query DSL that retrieves the date when the first blog was published
    Create a request with ES|QL that retrieves the top ten authors from the blogs index

# Review

- SQL, Lucene, EQL and KQL query languages are compatible with Elasticsearch but only KQL and Lucene can be used in Kibana’s query bar. Note that ES|QL also uses the query bar if it is explicitly selected.
- You do not need to have an aggregation included within a search request.
- An ES|QL query is composed of a series of commands that are processed sequentially; where the output of one operation becomes the input for the next.


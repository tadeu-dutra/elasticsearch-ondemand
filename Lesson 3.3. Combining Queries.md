# 3.3. Combining Queries

In this lesson, you’ll learn how to create more complex search requests using the bool query to combine one or more Boolean clauses.

In this lab, you will learn to combine multiple clauses to create more complicated queries by performing the following tasks:

    Require specific terms in specific fields and filter results by date
    Exclude terms from specific fields and require specific fields to exist
    Filter results to display only specific fields
    Sort results by relevance based on a specific field

## a) Query

Write a query on the blogs_fixed2 index that satisfies the following requirements:

    The term meetups appears in either the content or title field
    The blog was published after June 1, 2016

REQUEST

```
GET blogs_fixed2/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "multi_match": {
            "query": "meetups",
            "fields": [
              "content",
              "title"
            ]
          }
        }
      ],
      "filter": [
        {
          "range": {
            "publish_date": {
              "gt": "2016-06-01"
            }
          }
        }
      ]
    }
  }
}
```

RESPONSE

```
{
  "took": 5,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 73,
      "relation": "eq"
    },
    "max_score": 5.847891,
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

## b) Query

Write a query on the blogs_fixed2 index that satisfies the following requirements:

    The term visualization must be in the content field
    The term lens must not be in the content field
    The versions field must exist

REQUEST

```
GET blogs_fixed2/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "content": "visualization"
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "content": "lens"
          }
        }
      ],
      "filter": [
        {
          "exists": {
            "field": "versions"
          }
        }
      ]
    }
  }
}
```

RESPONSE

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
      "value": 543,
      "relation": "eq"
    },
    "max_score": 2.6804204,
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

## c) Query

Update the previous query to filter the _source to display only the title, seo, and versions fields.

REQUEST

```
GET blogs_fixed2/_search
{
  "_source": ["title", "seo", "versions"],
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "content": "visualization"
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "content": "lens"
          }
        }
      ],
      "filter": [
        {
          "exists": {
            "field": "versions"
          }
        }
      ]
    }
  }
}
```

RESPONSE

```
{
  "took": 5,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 543,
      "relation": "eq"
    },
    "max_score": 2.6804204,
    "hits": [
      {
        "_index": "blogs_fixed2",
        "_id": "66f55203c5f2efd5d0a7a979",
        "_score": 2.6804204,
        "_source": {
          "title": "Kibana Time Series Visual Builder | Elastic Blog",
          "seo": "Using Kibana's new time series visual builder feature to analyze and explore time series data living in Elasticsearch.",
          "versions": [
            "5.4"
          ]
        }
      },
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
NOTE: The versions don't always match the current version of the stack we are using.

## d) Query

Update the previous query to return the blogs from version 8 first.

REQUEST

```
GET blogs_fixed2/_search
{
  "_source": ["title", "seo", "versions"],
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "content": "visualization"
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "content": "lens"
          }
        }
      ],
      "filter": [
        {
          "exists": {
            "field": "versions"
          }
        }
      ],
      "should": [
        {
          "match": {
            "versions": "8"
          }
        }
      ]
    }
  }
}
```

RESPONSE

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
      "value": 543,
      "relation": "eq"
    },
    "max_score": 5.6033316,
    "hits": [
      {
        "_index": "blogs_fixed2",
        "_id": "66f55474c5f2efe57daca02e",
        "_score": 5.6033316,
        "_source": {
          "title": "How to index data into Elasticsearch from Confluent Cloud | Elastic Blog",
          "seo": "",
          "versions": [
            "8"
          ]
        }
      },
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
NOTE: The number of hits should stay the same but you should see blogs written for the version 8.


# Summary

In this lab, you learned to combine multiple clauses to create more complicated queries by performing the following tasks:

    Require specific terms in specific fields and filter results by date
    Exclude terms from specific fields and require specific fields to exist
    Filter results to display only specific fields
    Sort results by relevance based on a specific field

# Review

- All clauses are optional when using a bool query.
- A filter clause has no effect on a document’s score.
- Use Filter context when you are looking for an exact match, which is a yes or no type query. Ex: "match" : { "locale": "en-us" }

# Review


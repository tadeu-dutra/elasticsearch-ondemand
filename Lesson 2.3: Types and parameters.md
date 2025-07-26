# Lesson 2.3: Types and parameters

In this lesson, you’ll learn how to use mapping parameters to more efficiently store and ingest your documents.

In this lab, you will improve and explore the functionality of an index by performing the following tasks:

    Create a new index that copies multiple fields into a single field, applies an analyzer to other fields, and disables doc values for another field
    Reindex an index into the newly created index
    Compare identical queries on both of the indices
    Compare different queries on the newly created index

Write and run a request that satisfies the following requirements:

    Create a new index named blogs_fixed2 using the mapping and settings of blogs_fixed as the starting point
    Add a new text field to the blogs_fixed2 mapping named search_authors
    Use copy_to to copy the values of the authors.title, authors.company and authors.job_title fields into the search_authors field
    Apply the english analyzer to the content, seo and title fields
    Disable doc values for the new url field (it will not be used for sorting or aggregations)

## a) Run the Request

REQUEST

```
PUT blogs_fixed2
{
  "mappings": {
    "properties": {
      "search_authors": {
        "type": "text"
      },
      "authors": {
        "properties": {
          "company": {
            "type": "keyword",
            "copy_to": "search_authors"
          },
          "first_name": {
            "type": "keyword"
          },
          "job_title": {
            "type": "text",
            "copy_to": "search_authors",
            "fields": {
              "keyword": {
                "type": "keyword",
                "ignore_above": 256
              }
            }
          },
          "last_name": {
            "type": "keyword"
          },
          "title": {
            "type": "text",
            "copy_to": "search_authors"
          }
        }
      },
      "category": {
        "type": "keyword"
      },
      "content": {
        "type": "text",
        "analyzer": "english"
      },
      "locale": {
        "type": "keyword"
      },
      "publish_date": {
        "type": "date",
        "format": "iso8601"
      },
      "seo": {
        "type": "text",
        "analyzer": "english"
      },
      "title": {
        "type": "text",
        "analyzer": "english",
        "fields": {
          "keyword": {
            "type": "keyword",
            "ignore_above": 256
          }
        }
      },
      "url": {
        "type": "keyword",
        "doc_values": false
      },
      "versions": {
        "type": "keyword"
      }
    }
  }
}
```

RESPONSE
```
{
  "acknowledged": true,
  "shards_acknowledged": true,
  "index": "blogs_fixed2"
}
```

## Reindex

Reindex blogs into blogs_fixed2.

REQUEST

```
POST _reindex
{
  "source": {
    "index": "blogs"
  },
  "dest": {
    "index": "blogs_fixed2"
  }
}
```

RESPONSE

```
{
  "took": 9201,
  "timed_out": false,
  "total": 3687,
  "updated": 0,
  "created": 3687,
  "deleted": 0,
  "batches": 4,
  "version_conflicts": 0,
  "noops": 0,
  "retries": {
    "bulk": 0,
    "search": 0
  },
  "throttled_millis": 0,
  "requests_per_second": -1,
  "throttled_until_millis": 0,
  "failures": []
}
```

## c) Match Query

Run a match query searching for the term duplicated in the title field of the blogs index.

REQUEST

```
GET blogs/_search
{
  "query": {
    "match": {
      "title": "duplicated"
    }
  }
}
```

RESPONSE

```
{
  "took": 11,
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
NOTE: You get 0 results because no blog title contains the term duplicated.

Now run the same query on the new blogs_fixed2 index.

REQUEST

```
GET blogs_fixed2/_search
{
  "_source": ["title"], 
  "query": {
    "match": {
      "title": "duplicated"
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
      "value": 3,
      "relation": "eq"
    },
    "max_score": 7.5308595,
    "hits": [
      {
        "_index": "blogs_fixed2",
        "_id": "66f55200c5f2ef4f18a7a2fb",
        "_score": 7.5308595,
        "_source": {
          "title": "Little Logstash Lessons: Handling Duplicates | Elastic Blog"
        }
      },
      {⇔},
      {⇔}
    ]
  }
}
```
NOTE: You get 3 results where the title contains Duplicate or Duplicates. This is because the english analyzer applies stemming to the token, allowing you to match documents that contain variants of search terms. 

Write, run, and compare three queries intended to search for blogs written by a specific person named Tim working as Director. Use the following requirements:

    Search for Tim on the authors.first_name field
    Search for Director on the authors.job_title field
    Use the new field search_authors to query for both terms at the same time

## Three Queries

REQUEST A

```
GET blogs_fixed2/_search
{
  "_source": ["authors"], 
  "query": {
    "match": {
      "authors.first_name": "Tim"
    }
  }
}
```

RESPONSE A

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
      "value": 43,
      "relation": "eq"
    },
    "max_score": 4.963751,
    "hits": [⇔]
  }
}
```
NOTE: This query gives you 43 results of various authors with the named Tim. 

REQUEST B

```
GET blogs_fixed2/_search
{
  "_source": ["authors"], 
  "query": {
    "match": {
      "authors.job_title": "Director"
    }
  }
}
```

RESPONSE B

```
{
  "took": 13,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 369,
      "relation": "eq"
    },
    "max_score": 2.4060593,
    "hits": [⇔]
  }
}
```
NOTE: This query gives you 369 results for all directors. 

REQUEST C

```
GET blogs_fixed2/_search
{
  "_source": ["authors"], 
  "query": {
    "match": {
      "search_authors": "Tim director"
    }
  }
}
```

RESPONSE C

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
      "value": 407,
      "relation": "eq"
    },
    "max_score": 7.40677,
    "hits": [
      {
        "_index": "blogs_fixed2",
        "_id": "66f552b8c5f2efd700a8ef6f",
        "_score": 7.40677,
        "_source": {
          "authors": [
            {
              "last_name": "Schnell",
              "title": "Tim Schnell",
              "company": "Elastic",
              "first_name": "Tim",
              "job_title": "Engineering Director"
            }
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
NOTE: You get a lot of results but they are more relevant, since the first ones are from someone named Tim who is also a director. 

## d) Aggregation

REQUEST

```
GET blogs_fixed2/_search
{
  "size": 0,
  "aggs": {
    "top_job_titles": {
      "terms": {
        "field": "url",
        "size": 10
      }
    }
  }
}
```

RESPONSE

```
{
  "error": {
    "root_cause": [
      {
        "type": "illegal_argument_exception",
        "reason": "Can't load fielddata on [url] because fielddata is unsupported on fields of type [keyword]. Use doc values instead."
      }
    ],
    "type": "search_phase_execution_exception",
    "reason": "all shards failed",
    "phase": "query",
    "grouped": true,
    "failed_shards": [
      {
        "shard": 0,
        "index": "blogs_fixed2",
        "node": "k09ckMkZTlanth9kZGNBTw",
        "reason": {
          "type": "illegal_argument_exception",
          "reason": "Can't load fielddata on [url] because fielddata is unsupported on fields of type [keyword]. Use doc values instead."
        }
      }
    ],
    "caused_by": {
      "type": "illegal_argument_exception",
      "reason": "Can't load fielddata on [url] because fielddata is unsupported on fields of type [keyword]. Use doc values instead.",
      "caused_by": {
        "type": "illegal_argument_exception",
        "reason": "Can't load fielddata on [url] because fielddata is unsupported on fields of type [keyword]. Use doc values instead."
      }
    }
  },
  "status": 400
}
```
NOTE: The result is an error because the url field does not have doc values enabled. As a result, you cannot aggregate on that field.




# Summary

In this lab, you improved and explored the functionality of an index by performing the following tasks:

    Create a new index that copies multiple fields into a single field, applies an analyzer to other fields, and disables doc values for another field
    Reindex an index into the newly created index
    Compare identical queries on both of the indices
    Compare different queries on the newly created index



# Review
- The format parameter is used to change a field’s date format.
- The copy_to parameter doesn't add a new field to the _source that is returned with the hits.
- If you set enabled to false, you can no longer query on that field, as well as no longer aggregate on it.

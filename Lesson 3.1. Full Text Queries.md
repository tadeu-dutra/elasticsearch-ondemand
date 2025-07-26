# 3.1. Full Text Queries

In this lesson, you will learn how to search using full text queries. You will also learn how a score is calculated for each document that is a hit and by default, the ‘hits’ in the query response are sorted by this score.

In this lab, you will learn to write queries using the Elasticsearch Query DSL by performing the following tasks:

    Write match queries that find results using or and and logic
    Write match queries that sort results and return specific information
    Write multi_match queries that find results from multiple fields


## a) Match Query

Write and run a match query on the blogs_fixed2 index for the term open source in the content field.

REQUEST

```
GET blogs_fixed2/_search
{
  "query": {
    "match": {
      "content": "open source"
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
      "value": 2165,
      "relation": "eq"
    },
    "max_score": 3.5980153,
    "hits": [
      {
        "_index": "blogs_fixed2",
        "_id": "66f5517dc5f2ef47efa6bc4b",
        "_score": 3.5980153,
        "_source": {⇔}
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
NOTE: By default, the match query uses or logic, so it matches documents containing either open or source. 

## b)  Query 15 Documents

Modify and rerun the query to return 15 documents.

REQUEST

```
GET blogs_fixed2/_search?size=15
{
  "query": {
    "match": {
      "content": "open source"
    }
  }
}
```

RESPONSE

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
      "value": 2165,
      "relation": "eq"
    },
    "max_score": 3.5980153,
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

## c) Query _source Parameter

Modify your previous query, but have the response only contain each hit's title field.

REQUEST

```
GET blogs_fixed2/_search?size=15
{
  "_source": "title", 
  "query": {
    "match": {
      "content": "open source"
    }
  }
}
```

RESPONSE

```
{
  "took": 25,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 2165,
      "relation": "eq"
    },
    "max_score": 3.5980153,
    "hits": [
      {
        "_index": "blogs_fixed2",
        "_id": "66f5517dc5f2ef47efa6bc4b",
        "_score": 3.5980153,
        "_source": {
          "title": "Elastic Stack security through free and open | Elastic Blog"
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
NOTE: The _source parameter filters the fields that get returned in the hits, but keep in mind that this is done by parsing the JSON document source, resulting in additional overhead for Elasticsearch (although the amount of data transferred over the network decreases). 

## d) Query fields Parameter

The fields parameter is another way to return specific fields from hits, and is more efficient than using _source.
Modify and rerun the previous query so that _source is not returned and title is in the fields parameter instead.

REQUEST

```
GET blogs_fixed2/_search
{
  "size": 15, 
  "_source": false,
  "fields": ["title"],
  "query": {
    "match": {
      "content": "open source"
    }
  }
}
```

RESPONSE

```
{
  "took": 16,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 2165,
      "relation": "eq"
    },
    "max_score": 3.5980153,
    "hits": [
      {
        "_index": "blogs_fixed2",
        "_id": "66f5517dc5f2ef47efa6bc4b",
        "_score": 3.5980153,
        "fields": {
          "title": [
            "Elastic Stack security through free and open | Elastic Blog"
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

## e) Match Query

Create and run a query to search for blogs that contain open and source.

REQUEST

```
GET blogs_fixed2/_search
{
  "query": {
    "match": {
      "content": {
        "query": "open source",
        "operator": "and"
      }
    }
  }
}
```

RESPONSE

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
      "value": 968,
      "relation": "eq"
    },
    "max_score": 3.5980153,
    "hits": [
      {
        "_index": "blogs_fixed2",
        "_id": "66f5517dc5f2ef47efa6bc4b",
        "_score": 3.5980153,
        "_source": {
          "title": "Elastic Stack security through free and open | Elastic Blog",
          "locale": "en-us",
          "content": "14 July 2021 News Delivering security across the stack, through the free and open model By Deepak Mohan • Dan Courcy Share Share on Twitter Share on Facebook Share on LinkedInr Free and open software is foundational to today’s technology world. Whether it is broad infrastructure layer technologies like Docker and Kubernetes, data management platforms like MongoDB and PostgreSQL, or highly specialized applications like Blender and Shotcut, free and open software technologies now power innovations across the entire application stack. In addition to being a free, source-available alternative to commercial closed source offerings..."
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
NOTE: The query matches blogs where the terms open and source do not necessarily appear next to each other.

## f) Query Exact Phrase

Write and run a query to return blogs that contain only the exact phrase open source.

REQUEST

```
GET blogs_fixed2/_search
{
  "query": {
    "match_phrase": {
      "content": "open source"
    }
  }
}
```

RESPONSE

```
{
  "took": 8,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 512,
      "relation": "eq"
    },
    "max_score": 3.5504026,
    "hits": [
      {
        "_index": "blogs_fixed2",
        "_id": "66f55136c5f2efb19ea624d3",
        "_score": 3.5504026,
        "_source": {
          "title": "Elasticsearch is Open Source, Again | Elastic Blog",
          "locale": "en-us",
          "content": "Table of Contents Table of contents Elasticsearch is Open Source, Again Close Elasticsearch is Open Source..."
        }
      },
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
NOTE: By default, the hits are sorted by score.

## g) Match Phrase Query

Modify and rerun the query to satisfy the following requirements:

    Filter the _source to display only the title and the publish_date
    Get the five most recent blogs
    Sort the results by publish_date with the most recent first

REQUEST

```
GET blogs_fixed2/_search
{
  "_source": ["title", "publish_date"], 
  "size": 5,
  "sort": [
    {
      "publish_date": {
        "order": "desc"
      }
    }
  ], 
  "query": {
    "match_phrase": {
      "content": "open source"
    }
  }
}
```

RESPONSE

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
      "value": 512,
      "relation": "eq"
    },
    "max_score": null,
    "hits": [
      {
        "_index": "blogs_fixed2",
        "_id": "66f55405c5f2ef229babe288",
        "_score": null,
        "_source": {
          "title": "A FAIR perspective on generative AI risks and frameworks | Elastic Blog",
          "publish_date": "2024-09-23"
        },
        "sort": [
          1727049600000
        ]
      },
      {⇔},
      {⇔},
      {⇔},
      {⇔}
    ]
  }
}
```

## h) Paging Search Results

Modify and rerun the query to get the next five most recent blogs.

REQUEST

```
GET blogs_fixed2/_search
{
  "_source": ["title", "publish_date"], 
  "from": 5, 
  "size": 5,
  "sort": [
    {
      "publish_date": {
        "order": "desc"
      }
    }
  ], 
  "query": {
    "match_phrase": {
      "content": "open source"
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
      "value": 512,
      "relation": "eq"
    },
    "max_score": null,
    "hits": [
      {
        "_index": "blogs_fixed2",
        "_id": "66f55482c5f2efe35facb9d2",
        "_score": null,
        "_source": {
          "title": "How we built Automatic Import, Attack Discovery, and Elastic AI Assistant using LangChain | Elastic Blog",
          "publish_date": "2024-08-08"
        },
        "sort": [
          1723075200000
        ]
      },
      {⇔},
      {⇔},
      {⇔},
      {⇔}
    ]
  }
}
```

---
So far, we have only performed queries on the content field, but other fields might be relevant for the users.

## i) Query on Multiple Fields

Create and run a query searching for the term performance on the content, title, and seo fields.

REQUEST

```
GET blogs_fixed2/_search
{
  "query": {
    "multi_match": {
      "query": "performance",
      "fields": [
        "title",
        "seo",
        "content"
      ]
    }
  }
}
```

RESPONSE

```
{
  "took": 35,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 1690,
      "relation": "eq"
    },
    "max_score": 5.930011,
    "hits": [
      {
        "_index": "blogs_fixed2",
        "_id": "66f5550ac5f2efbe11ada5ec",
        "_score": 5.930011,
        "_source": {
          "title": "How we perform continuous performance testing on Enterprise Search | Elastic Blog",
          "locale": "en-us",
          "content": """Table of Contents Table of contents How we perform continuous performance testing on Enterprise Search...",
          "seo": ""
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

## i) Searching for Phrases on Multiple Fields

Modify and rerun the previous query to search for the phrase performance tuning on the same three fields.

REQUEST

```
GET blogs_fixed2/_search
{
  "query": {
    "multi_match": {
      "query": "performance tuning",
      "type": "phrase",
      "fields": [
        "title",
        "seo",
        "content"
      ]
    }
  }
}
```

RESPONSE

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
      "value": 20,
      "relation": "eq"
    },
    "max_score": 10.673772,
    "hits": [
      {
        "_index": "blogs_fixed2",
        "_id": "66f553b6c5f2ef0264ab4ae0",
        "_score": 10.673772,
        "_source": {
          "title": "Performance Tuning of the Elastic APM Java Agent | Elastic Blog",
          "locale": "en-us",
          "content": """28 February 2019 Performance Tuning of the Elastic APM Java Agent...",
          "seo": ""
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


# Summary

In this lab, you learned to write queries using the Elasticsearch Query DSL by performing the following tasks:

    Write match queries that find results using or and and logic
    Write match queries that sort results and return specific information
    Write multi_match queries that find results from multiple fields


# Review

- The way the multi_match query is executed depends on the type parameter. For example, setting “type”: “phrase” runs a match_phrase. The type parameter specifies what type of query is executed for multi_match.
- Search results are sorted by score by default, but you can change the sort order with the sort parameter.
- 10 is the default number of hits returned from a query using the Query DSL. You can change this using the size parameter.

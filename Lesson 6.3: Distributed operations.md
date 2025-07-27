# 6.3: Distributed operations

In this lesson, you’ll take a closer look at the internal operation inside Elasticsearch. You will learn how shards communicate with each other for write and search operations.


In this lab, you will learn how Elasticsearch combines the results from multiple shards by performing the following tasks:

    Create a new index and reindex documents into it
    Perform search requests on different shards
    Analyze the anatomy of the search requests

## Create Index

Create a new index that satisfies the following requirements:

    The name of the index is blogs_tmp
    The index has two primary shards
    The index has zero replica shards

REQUEST

```
PUT blogs_tmp
{
  "settings": {
    "number_of_shards": 2,
    "number_of_replicas": 0
  }
}
```

RESPONSE

```
{
  "acknowledged": true,
  "shards_acknowledged": true,
  "index": "blogs_tmp"
}
```

## b) Reindex

Use the Reindex API to reindex the documents from blogs into blogs_tmp.

REQUEST

```
POST _reindex
{
  "source": {
    "index": "blogs"
  },
  "dest": {
    "index": "blogs_tmp"
  }
}
```

RESPONSE

```
{
  "took": 3177,
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

## c) Cat Shards API

Use the _cat/shards API to examine the shards of the new index and see how Elasticsearch distributed the documents.

REQUEST

```
GET _cat/shards/blogs_tmp?v
```

RESPONSE

```
index     shard prirep state   docs  store dataset ip         node
blogs_tmp 0     p      STARTED 1823 12.9mb  12.9mb 172.19.0.2 node3
blogs_tmp 1     p      STARTED 1864   13mb    13mb 172.19.0.4 node2
```

## d) Query (shard 0)

On this new index, perform a query that satisfies the following requirements:

    Uses the preference parameter to query only the documents of the shard 0 (GET blogs_tmp/_search?preference=_shards:0)
    Returns only the top 3 results
    Filters the _source to display only the title field
    Queries the blogs that mention Agent in the content field

REQUEST

```
GET blogs_tmp/_search?preference=_shards:0
{
  "size": 3,
  "_source": ["title"], 
  "query": {
    "match": {
      "content": "Agent"
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
      "value": 209,
      "relation": "eq"
    },
    "max_score": 4.575447,
    "hits": [
      {
        "_index": "blogs_tmp",
        "_id": "66f554f9c5f2ef7954ad89db",
        "_score": 4.575447,
        "_ignored": [
          "content.keyword"
        ],
        "_source": {
          "title": "Regression testing your Java Agent Plugin | Elastic Blog"
        }
      },
      {
        "_index": "blogs_tmp",
        "_id": "66f5544fc5f2efb6f5ac6080",
        "_score": 4.513438,
        "_ignored": [
          "content.keyword"
        ],
        "_source": {
          "title": "Using Elastic Agent Performance Presets in 8.12 | Elastic Blog"
        }
      },
      {
        "_index": "blogs_tmp",
        "_id": "66f55204c5f2efbacfa7aa67",
        "_score": 4.507896,
        "_ignored": [
          "content.keyword"
        ],
        "_source": {
          "title": "Elastic Agent and Fleet make it easier to integrate your systems with Elastic | Elastic Blog"
        }
      }
    ]
  }
}
```
NOTE: Keep track of the number of results, the top 3 blogs' names, and their scores. 

## e) Query (shard 1)

Run the same query for the shard 1.

REQUEST

```
GET blogs_tmp/_search?preference=_shards:1
{
  "size": 3,
  "_source": ["title"], 
  "query": {
    "match": {
      "content": "Agent"
    }
  }
}
```

RESPONSE

```
{
  "took": 31,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 224,
      "relation": "eq"
    },
    "max_score": 4.4801135,
    "hits": [
      {
        "_index": "blogs_tmp",
        "_id": "66f552a3c5f2efaa99a8c30c",
        "_score": 4.4801135,
        "_ignored": [
          "content.keyword"
        ],
        "_source": {
          "title": "Elastic APM iOS agent technical preview released | Elastic Blog"
        }
      },
      {
        "_index": "blogs_tmp",
        "_id": "66f553c3c5f2effbbaab67c0",
        "_score": 4.4663463,
        "_ignored": [
          "content.keyword"
        ],
        "_source": {
          "title": "Elastic APM .NET Agent Preview Released | Elastic Blog"
        }
      },
      {
        "_index": "blogs_tmp",
        "_id": "66f551c0c5f2ef8ebda730a0",
        "_score": 4.437061,
        "_ignored": [
          "content.keyword"
        ],
        "_source": {
          "title": "Elastic APM PHP Agent 1.0 released | Elastic Blog"
        }
      }
    ]
  }
}
```
NOTE: Once again, save the results somewhere so you can easily visualize them. 



## f) Query on the entire index

Run the same query on the entire index by removing the preference parameter. Can you already predict the final result?

REQUEST

```
GET blogs_tmp/_search
{
  "size": 3,
  "_source": ["title"], 
  "query": {
    "match": {
      "content": "Agent"
    }
  }
}
```

RESPONSE

```
{
  "took": 15,
  "timed_out": false,
  "_shards": {
    "total": 2,
    "successful": 2,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 433,
      "relation": "eq"
    },
    "max_score": 4.575447,
    "hits": [
      {
        "_index": "blogs_tmp",
        "_id": "66f554f9c5f2ef7954ad89db",
        "_score": 4.575447,
        "_ignored": [
          "content.keyword"
        ],
        "_source": {
          "title": "Regression testing your Java Agent Plugin | Elastic Blog"
        }
      },
      {
        "_index": "blogs_tmp",
        "_id": "66f5544fc5f2efb6f5ac6080",
        "_score": 4.513438,
        "_ignored": [
          "content.keyword"
        ],
        "_source": {
          "title": "Using Elastic Agent Performance Presets in 8.12 | Elastic Blog"
        }
      },
      {
        "_index": "blogs_tmp",
        "_id": "66f55204c5f2efbacfa7aa67",
        "_score": 4.507896,
        "_ignored": [
          "content.keyword"
        ],
        "_source": {
          "title": "Elastic Agent and Fleet make it easier to integrate your systems with Elastic | Elastic Blog"
        }
      }
    ]
  }
}
```
NOTE: You should get the expected top 3 hits, those with the highest score between both shards. You can also verify that the total number of hits equals the sum on each shard.



# Summary

In this lab, you learned how Elasticsearch combines the results from multiple shards by performing the following tasks:

    Create a new index and reindex documents into it
    Perform search requests on different shards
    Analyze the anatomy of the search requests


# Review

- The primary shard performs the operation first, then forwards the request in parallel to all the replicas.
- Any replica contains the same data as the primary. When searching for documents, Elasticsearch can ask for the primary or its replica depending on the availability.

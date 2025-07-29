# 8.1: Multi cluster operations

In this lesson, you’ll learn about cross-cluster replication and cross-cluster searches.

In this lab, you will learn about cross-cluster replication by performing the following tasks:

    Set up a remote cluster for cross-cluster search (CCS)
    Configure cross-cluster replication (CCR)
    Write a search across indices on multiple clusters

## a) Match All Query

Run a match_all query on the blogs index on cluster2

REQUEST

```
GET blogs/_search
```

RESPONSE

```
{
  "took": 1,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 7,
      "relation": "eq"
    },
    "max_score": 1,
    "hits": [
      {
        "_index": "blogs",
        ...
        "_source": {
          ...
          "publish_date": "2021-04-27T18:00:00.000Z",
          ...
        }
      },
      {
        "_index": "blogs",
        ...
        "_source": {
          ...
          "publish_date": "2021-04-26T15:00:00.000Z",
          ...
        }
      },
      {
        "_index": "blogs",
        ...
        "_source": {
          ...
          "publish_date": "2021-04-28T16:00:00.000Z",
          ...
        }
      },
      {
        "_index": "blogs",
        ...
        "_source": {
          ...
          "publish_date": "2021-04-28T15:00:00.000Z",
          ...
        }
      },
      {
        "_index": "blogs",
        ...
        "_source": {
          ...
          "publish_date": "2021-04-29T15:00:00.000Z",
          ...
        }
      },
      {
        "_index": "blogs",
        ...
        "_source": {
          ...
          "publish_date": "2021-04-29T18:00:00.000Z",
          ...
          ]
        }
      },
      {
        "_index": "blogs",
        ...
        "_source": {
          ...
          "publish_date": "2021-04-27T15:00:00.000Z",
          ...
          ]
        }
      }
    ]
  }
}
```

## b) 


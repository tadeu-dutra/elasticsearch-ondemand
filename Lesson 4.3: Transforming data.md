# 4.3: Transforming data

In this lesson, you’ll learn how to perform a transform in Kibana using event-centric data. We will examine both pivot transforms, similar to pivot tables which you may already be used to, and the latest transforms, which gather the newest documents in your index.

In this lab, you will learn about transforms by performing the following tasks:

    Create and start a pivot transform to aggregate and group data from an index
    View, open, and query the transform data in Discover

## a) Aggregation Query

Write an aggregation to get the number of views for each distinct URL. What is the most popular blog?

REQUEST

```
GET web_traffic/_search
{
  "size": 0,
  "aggs": {
    "popular_urls": {
      "terms": {
        "field": "url.keyword"
      }
    }
  }
}
```

RESPONSE

```
{
  "took": 645,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 10000,
      "relation": "gte"
    },
    "max_score": null,
    "hits": []
  },
  "aggregations": {
    "popular_urls": {
      "doc_count_error_upper_bound": 0,
      "sum_other_doc_count": 1314462,
      "buckets": [
        {
          "key": "https://www.elastic.co/blog/no-sql-yes-search",
          "doc_count": 985
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
}
```
NOTE: The blog https://www.elastic.co/blog/no-sql-yes-search that has 985 views. Notice that you get only the number of views for the top 10 URLs.

Next, you will answer the same question using the Transforms UI in Kibana to create a transform which will:

    Count the number of visitors to a blog page using the url field
    Compute the average load time of all the visits to a blog page using the runtime_sec field

TIP

You can also complete this task by running the following command in Console: 

```
PUT _transform/traffic_stats
{
  "source": {
    "index": "web_traffic",
    "query": {
      "match_all": {}
    }
  },
  "pivot": {
    "group_by": {
      "url_original": {
        "terms": {
          "field": "url.keyword"
        }
      }
    },
    "aggregations": {
      "load_time": {
        "avg": {
          "field": "runtime_sec"
        }
      },
      "number_of_views": {
        "value_count": {
          "field": "@timestamp"
        }
      }
    }
  },
  "dest": {
    "index": "traffic_stats"
  }
}
```

```
POST _transform/traffic_stats/_start
```



# Summary

In this lab, you learned about transforms by performing the following tasks:

    Create and start a pivot transform to aggregate and group data from an index
    View, open, and query the transform data in Discover

# Review

- Transforms and destination indices can be configured to work optimally with your dataset.
- When should you use transforms?
  - To pivot your data from event-centric to entity-centric data
  - To find the most recent documents of a particular grouping
- What transform type can you use to answer the following question: “Which of my servers have been idle during the last 10 mins?”
  - Use Latest to keep track of the most recent activity for each server. Find the server that hasn’t had a new document in the last 10 minutes.
  - NOTE: To address the question of idle servers, you should use Latest to keep track of the most recent activity for each server. This way, you can find the server that hasn’t had a new document in the last 10 minutes.
  

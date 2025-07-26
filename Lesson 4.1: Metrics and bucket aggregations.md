# 4.1: Metrics and bucket aggregations

In this lesson, you’ll learn about the  different types of aggregations, the basic structure of the request and associated response. You will then analyze data using both metric and bucket aggregations in order to answer many types of questions about your data.

In this lab, you will become familiar with writing metrics and bucket aggregations by performing the following tasks:

    Query for statistics, percentiles, and distinct values on a field
    Query to count document by sets and date ranges

## a) Min Aggregation

Write and run a query on the web_traffic index to get the runtime of the fastest request. Be sure to set size to 0.

REQUEST

```
GET web_traffic/_search
{
  "size": 0,
  "aggs": {
    "fastest_request_time": {
      "min": {
        "field": "runtime_sec"
      }
    }
  }
}

```

RESPONSE

```
{
  "took": 21,
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
    "fastest_request_time": {
      "value": 0.2199999988079071
    }
  }
}
```
NOTE: The fastest query took 0.22 s to run.

---
You can also use the stats aggregation when you want to calculate all the main metrics (min, max, avg, and sum).

## b) Stats Aggregation

Update the previous aggregation query to use stats instead of min. What is the average runtime?

REQUEST

```
GET web_traffic/_search
{
  "size": 0,
  "aggs": {
    "request_time_stats": {
      "stats": {
        "field": "runtime_sec"
      }
    }
  }
}
```

RESPONSE

```
{
  "took": 141,
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
    "request_time_stats": {
      "count": 1324128,
      "min": 0.2199999988079071,
      "max": 95.87799835205078,
      "avg": 3.3013751171283854,
      "sum": 4371443.231092975
    }
  }
}
```
NOTE: The average runtime is approximately 3.3 s

## c) Percentiles Aggregation

Calculate the median runtime and verify if 90% of the requests take less than 6 seconds.
INFO: The percentile_ranks aggregation further allows you to provide a value and get back the percentile it represents.

REQUESTS

```
GET web_traffic/_search
{
  "size": 0,
  "aggs": {
    "runtime_median_and_90": {
      "percentiles": {
        "field": "runtime_sec",
        "percents": [
          50,
          90
        ]
      }
    }
  }
}
```

RESPONSE

```
{
  "took": 677,
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
    "runtime_median_and_90": {
      "values": {
        "50.0": 2.844954458986629,
        "90.0": 5.599563660456872
      }
    }
  }
}
```
NOTE: The median runtime is approximately 2.84 s, and 90% of the requests take less than approximately 5.6 seconds.

## d) Percentile Ranks Aggregation

Your SLA for the queries' runtime is 5 seconds. What percentage of the requests is within this time?

REQUEST

```
GET web_traffic/_search
{
  "size": 0,
  "aggs": {
    "runtime_goal": {
      "percentile_ranks": {
        "field": "runtime_sec",
        "values": [
          5
        ]
      }
    }
  }
}
```

RESPONSE

```
{
  "took": 158,
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
    "runtime_goal": {
      "values": {
        "5.0": 85.99170507032534
      }
    }
  }
}
```
NOTE: Approximately 86% of the requests take 5 seconds or less.

## e) Cardinality Aggregation

Use a cardinality aggregation to query the url.keyword field. How many distinct URLs were visited?

REQUEST

```
GET web_traffic/_search
{
  "size": 0,
  "aggs": {
    "my_url_value_count": {
      "cardinality": {
        "field": "url.keyword"
      }
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
      "value": 10000,
      "relation": "gte"
    },
    "max_score": null,
    "hits": []
  },
  "aggregations": {
    "my_url_value_count": {
      "value": 3656
    }
  }
}
```
NOTE: You should get 3656 distinct URLs.

## f) Bucket Aggregation

Use a bucket aggregation to query the method.keyword field. How many requests are there for each HTTP method?

REQUEST

```
GET web_traffic/_search
{
  "size": 0,
  "aggs": {
    "method_buckets": {
      "terms": {
        "field": "method.keyword"
      }
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
      "value": 10000,
      "relation": "gte"
    },
    "max_score": null,
    "hits": []
  },
  "aggregations": {
    "method_buckets": {
      "doc_count_error_upper_bound": 0,
      "sum_other_doc_count": 0,
      "buckets": [
        {
          "key": "GET",
          "doc_count": 926215
        },
        {
          "key": "POST",
          "doc_count": 265298
        },
        {
          "key": "PUT",
          "doc_count": 105933
        },
        {
          "key": "DELETE",
          "doc_count": 26682
        }
      ]
    }
  }
}
```
NOTE: By default, the terms aggregation is sorted by doc_count.

## g) Terms Aggregation Order

Change your previous search so that terms are sorted alphabetically.

REQUEST

```
GET web_traffic/_search
{
  "size": 0,
  "aggs": {
    "method_buckets": {
      "terms": {
        "field": "method.keyword",
        "order": {
          "_key": "asc"
        }
      }
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
      "value": 10000,
      "relation": "gte"
    },
    "max_score": null,
    "hits": []
  },
  "aggregations": {
    "method_buckets": {
      "doc_count_error_upper_bound": 0,
      "sum_other_doc_count": 0,
      "buckets": [
        {
          "key": "DELETE",
          "doc_count": 26682
        },
        {
          "key": "GET",
          "doc_count": 926215
        },
        {
          "key": "POST",
          "doc_count": 265298
        },
        {
          "key": "PUT",
          "doc_count": 105933
        }
      ]
    }
  }
}
```


## h) Date Histogram Aggregation

Use a date_histogram aggregation to determine how many requests there are for each week.

REQUEST

```
GET web_traffic/_search
{
  "size": 0,
  "aggs": {
    "logs_by_week": {
      "date_histogram": {
        "field": "@timestamp",
        "calendar_interval": "week"
      }
    }
  }
}
```

RESPONSE

```
{
  "took": 394,
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
    "logs_by_week": {
      "buckets": [
        {
          "key_as_string": "2010-02-08T00:00:00.000Z",
          "key": 1265587200000,
          "doc_count": 1
        },
        {
          "key_as_string": "2010-02-15T00:00:00.000Z",
          "key": 1266192000000,
          "doc_count": 4
        },
        {⇔},
        ...
        {⇔}
      ]
    }
  }
}
```


## i) Updated Aggregation

Change the previous aggregation to return the number of requests per second. What happened?

REQUEST

```
GET web_traffic/_search
{
  "size": 0,
  "aggs": {
    "logs_by_week": {
      "date_histogram": {
        "field": "@timestamp",
        "calendar_interval": "second"
      }
    }
  }
}
```

RESPONSE

```
{
  "error": {
    "root_cause": [],
    "type": "search_phase_execution_exception",
    "reason": "",
    "phase": "rank-feature",
    "grouped": true,
    "failed_shards": [],
    "caused_by": {
      "type": "too_many_buckets_exception",
      "reason": "Trying to create too many buckets. Must be less than or equal to: [65536] but this number of buckets was exceeded. This limit can be set by changing the [search.max_buckets] cluster level setting.",
      "max_buckets": 65536
    }
  },
  "status": 400
}
```
NOTE: The requested aggregation would generate too many buckets. This might put the cluster in danger. Thus, Elasticsearch will not execute it. The maximum number of buckets allowed in a single response is limited by a dynamic cluster setting named search.max_buckets. As you can see in the response, it defaults to 65536. Requests that try to return more than the limit will fail with an exception. 


# Summary

In this lab, you became familiar with writing metrics and bucket aggregations by performing the following tasks:

    Query for statistics, percentiles, and distinct values on a field
    Query to count document by sets and date ranges


# Review

- Is date_histogram a bucket, metric, or pipeline aggregation? Bucket. The date_histogram groups documents by their date in fixed date range intervals.
- Some buckets are sorted alphabetically. You can sort buckets by a metric value in a sub-aggregation.
- Terms aggregation creates buckets for each unique value of the field. It can be used to put logging events into buckets by log level (“error”, “warn”, “info”, etc.)

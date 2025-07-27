# 4.2: Combining aggregations

In this lesson, you’ll learn how to combine aggregations to answer more complex questions. For example, you can create multiple aggregations in the same search request or define sub-aggregations to create a hierarchical structure of aggregations.

In this lab, you will learn how to combine queries by performing the following tasks:

    Combine an aggregation with a query to reduce the scope
    Combine multiple aggregations
    Sort aggregations usings sub-buckets


1. Update the original query to include only weekly requests that returned a 404 response:

## a) Original Query
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

REQUEST

```
GET web_traffic/_search
{
  "size": 0,
  "query": {
    "term": {
      "response_code.keyword": {
        "value": "404"
      }
    }
  },
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
  "took": 20,
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
          "key_as_string": "2010-02-22T00:00:00.000Z",
          "key": 1266796800000,
          "doc_count": 1
        },
        {
          "key_as_string": "2010-03-01T00:00:00.000Z",
          "key": 1267401600000,
          "doc_count": 0
        },
        {⇔},
        ...
        {⇔}
      ]
    }
  }
}
```

2. In a single request, calculate both the average and the median of the runtime_sec field.

REQUEST

```
GET web_traffic/_search
{
  "size": 0,
  "aggs": {
    "average_request_time": {
      "avg": {
        "field": "runtime_sec"
      }
    },
    "median": {
      "percentiles": {
        "field": "runtime_sec",
        "percents": [
          50
        ]
      }
    }
  }
}
```

RESPONSE

```
    {
      "took": 2,
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
        "median": {
          "values": {
            "50.0": 2.844954458986629
          }
        },
        "average_request_time": {
          "value": 3.3013751171283854
        }
      }
    }
```

3. Calculate the median runtime for each response code.

REQUEST

```
GET web_traffic/_search
{
  "size": 0,
  "aggs": {
    "status_code_buckets": {
      "terms": {
        "field": "response_code.keyword"
      },
      "aggs": {
        "runtime": {
          "percentiles": {
            "field": "runtime_sec",
            "percents": [
              50
            ]
          }
        }
      }
    }
  }
}
```

RESPONSE

```
{
  "took": 195,
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
    "status_code_buckets": {
      "doc_count_error_upper_bound": 0,
      "sum_other_doc_count": 0,
      "buckets": [
        {
          "key": "200",
          "doc_count": 1125469,
          "runtime": {
            "values": {
              "50.0": 2.7177628526374282
            }
          }
        },
        {⇔},
        {⇔},
        {⇔}
      ]
    }
  }
}
```

4. Sort the result of the previous query starting with the highest runtime median. What response codes are the slowest?

REQUEST

```
GET web_traffic/_search
{
  "size": 0,
  "aggs": {
    "status_code_buckets": {
      "terms": {
        "field": "response_code.keyword",
        "order": {
          "runtime.50": "desc"
        }
      },
      "aggs": {
        "runtime": {
          "percentiles": {
            "field": "runtime_sec",
            "percents": [
              50
            ]
          }
        }
      }
    }
  }
}
```

RESPONSE

```
{
  "took": 236,
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
    "status_code_buckets": {
      "doc_count_error_upper_bound": 0,
      "sum_other_doc_count": 0,
      "buckets": [
        {
          "key": "500",
          "doc_count": 13294,
          "runtime": {
            "values": {
              "50.0": 7.3192221781243365
            }
          }
        },
        {⇔},
        {⇔},
        {⇔}
      ]
    }
  }
}
```
NOTE: The 500 response codes are the slowest.

5. For each month, break down the number of requests by response code.

REQUEST

```
GET web_traffic/_search
{
  "size": 0,
  "aggs": {
    "logs_by_week": {
      "date_histogram": {
          "field": "@timestamp",
          "calendar_interval": "month"
      },
      "aggs": {
        "response": {
          "terms": {
            "field": "response_code.keyword"
          }
        }
      }
    }
  }
}
```

RESPONSE

```
{
  "took": 309,
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
          "key_as_string": "2010-02-01T00:00:00.000Z",
          "key": 1264982400000,
          "doc_count": 12,
          "response": {
            "doc_count_error_upper_bound": 0,
            "sum_other_doc_count": 0,
            "buckets": [
              {
                "key": "200",
                "doc_count": 8
              },
              {
                "key": "301",
                "doc_count": 3
              },
              {
                "key": "404",
                "doc_count": 1
              }
            ]
          }
        },
        {⇔},
        ...
        {⇔}
      ]
    }
  }
}
```

6. Which month had the highest average for the runtime_sec field?

REQUEST

```
GET web_traffic/_search
{
  "size": 0, 
  "aggs": {
    "runtime_avg_per_month": {
      "date_histogram": {
        "field": "@timestamp",
        "calendar_interval": "month"
      },
      "aggs": {
        "avg_runtime": {
          "avg": {
            "field": "runtime_sec"
          }
        }
      }
    },
    "max_avg_runtime": {
      "max_bucket": {
        "buckets_path": "runtime_avg_per_month>avg_runtime" 
      }
    }
  }
}
```

RESPONSE

```
{
  "took": 122,
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
    "runtime_avg_per_month": {
      "buckets": [⇔]
    },
    "max_avg_runtime": {
      "value": 3.905590911706289,
      "keys": [
        "2010-03-01T00:00:00.000Z"
      ]
    }
  }
}
```
NOTE: In March 2010, the average of the field runtime_sec was approximately 3.9 seconds. 


# Summary

In this lab, you learned how to combine queries by performing the following tasks:

    Combine an aggregation with a query to reduce the scope
    Combine multiple aggregations
    Sort aggregations usings sub-buckets



# Review

- You create a date_histogram to group documents by month, and then calculate the moving average of the “price” field over a three-month window. What type of aggregation have you created? A pipeline aggregation, where the output from the histogram is used for the input to the moving average.
- You can combine a date_histogram aggregation (buckets by year) with a terms aggregation (buckets by category.) to build a visualization like this:
  ![q2](https://github.com/user-attachments/assets/19755161-adb3-4cd2-ac58-e9c22835d5d9)
- When metrics are embedded under a bucket as a sub-aggregation, they calculate their value based on the documents residing inside the bucket. (i.e. Metrics can be embedded under a bucket as a sub-aggregation.)
  

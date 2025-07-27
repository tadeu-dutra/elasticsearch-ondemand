# 5.1. Changing Data

In this lesson, you’ll learn how to manipulate data within your Elasticsearch cluster by reindexing, updating, or deleting collections of documents.

In this lab, you will become familiar with ingest pipelines by performing the following tasks:

    Reindex, count, and delete documents
    Create and test an ingest pipeline
    Create an index and define its default pipeline

1. Reindex the documents from the blogs index on the remote cluster2 into the existing blogs_fixed2 index on cluster1 (Kibana1). Use the following details for cluster2:

REQUEST

```
POST _reindex
{
  "source": {
    "remote": {
      "host": "https://node5:9200",
      "username": "training",
      "password": "nonprodpwd"
    },
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
  "took": 294,
  "timed_out": false,
  "total": 7,
  "updated": 0,
  "created": 7,
  "deleted": 0,
  "batches": 1,
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

2. Get a count of documents in the blogs_fixed2 index. What is the result?

REQUEST

```
GET blogs_fixed2/_count
```

RESPONSE

```
{
  "count": 3694,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  }
}
```

3. Delete all the documents in blogs_fixed2 where versions equals Pre 1. How many documents are deleted?

REQUEST

```
POST blogs_fixed2/_delete_by_query
{
  "query": {
    "match": {
      "versions": "Pre 1"
    }
  }
}
```

RESPONSE

```
{
  "took": 162,
  "timed_out": false,
  "total": 194,
  "deleted": 194,
  "batches": 1,
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
NOTE: 194 documents should be deleted. 

Next, you will create an ingest pipeline that satisfies the following requirements:

    The name of the pipeline is web_traffic_pipeline
    The pipeline removes the field timestamp
    The pipeline renames the field method to http.request.method
    The pipeline renames the field response_code to http.response.status_code
    The pipeline uses the user_agent processor on the field user_agent
    The above processors should ignore documents that do not have the specified fields

Go to Stack Management > Ingest Pipelines and click Create pipeline > New pipeline.

<img width="1232" height="824" alt="image" src="https://github.com/user-attachments/assets/90493b17-7028-48a1-9482-474e9bccc5c3" />

```
[
  {
    "remove": {
      "field": "timestamp",
      "ignore_missing": true
    }
  },
  {
    "rename": {
      "field": "method",
      "target_field": "http.request.method",
      "ignore_missing": true
    }
  },
  {
    "rename": {
      "field": "response_code",
      "target_field": "http.response.status_code",
      "ignore_missing": true
    }
  },
  {
    "user_agent": {
      "field": "user_agent",
      "ignore_missing": true
    }
  }
]
```

INFO: Elastic's web team tracks visits to the blogs. Here is an example log from one of those visits: 

```
{
  "_index": "web_traffic",
  "_id": "4unr_JIBmBaRB3mGRhkY",
  "_score": 1,
  "_source": {
    "response_code": "301",
    "@timestamp": "2024-01-18T13:13:59.000Z",
    "method": "PUT",
    "ip": "3.168.38.114",
    "runtime_sec": 1.899,
    "bytes_sent": "40901",
    "url": "https://www.elastic.co/blog/elasticsearch-7-0-0-released",
    "user_agent": "Mozilla/5.0 (Linux; Android 10; SM-G973F) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.159 Mobile Safari/537.36",
    "timestamp": "2024-01-1813:13:59"
  }
}
```

Test Pipeline

```
{
  "@timestamp": "2024-01-18T13:13:59.000Z",
  "ip": "3.168.38.114",
  "runtime_sec": 1.899,
  "http": {
    "request": {
      "method": "PUT"
    },
    "response": {
      "status_code": "301"
    }
  },
  "bytes_sent": "40901",
  "url": "https://www.elastic.co/blog/elasticsearch-7-0-0-released",
  "user_agent": {
    "name": "Chrome Mobile",
    "original": "Mozilla/5.0 (Linux; Android 10; SM-G973F) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.159 Mobile Safari/537.36",
    "os": {
      "name": "Android",
      "version": "10",
      "full": "Android 10"
    },
    "device": {
      "name": "Samsung SM-G973F"
    },
    "version": "92.0.4515.159"
  }
}
```

## If you want to bypass the UI and define the pipeline using an HTTP request, you create and test the pipeline by running the below requests in Console.

## a) PIPELINE REQUEST

```
PUT _ingest/pipeline/web_traffic_pipeline
{
  "processors": [
    {
      "remove": {
        "field": "timestamp",
        "ignore_missing": true
      }
    },
    {
      "rename": {
        "field": "method",
        "target_field": "http.request.method",
        "ignore_missing": true
      }
    },
    {
      "rename": {
        "field": "response_code",
        "target_field": "http.response.status_code",
        "ignore_missing": true
      }
    },
    {
      "user_agent": {
        "field": "user_agent",
        "ignore_missing": true
      }
    }
  ]
}
```

b) PIPELINE RESPONSE

```
{
  "acknowledged": true
}
```

c) TEST REQUEST

```
GET _ingest/pipeline/web_traffic_pipeline/_simulate
{
  "docs": [
    {
      "_index": "web_traffic",
      "_id": "4unr_JIBmBaRB3mGRhkY",
      "_score": 1,
      "_source": {
        "response_code": "301",
        "@timestamp": "2024-01-18T13:13:59.000Z",
        "method": "PUT",
        "ip": "3.168.38.114",
        "runtime_sec": 1.899,
        "bytes_sent": "40901",
        "url": "https://www.elastic.co/blog/elasticsearch-7-0-0-released",
        "user_agent": "Mozilla/5.0 (Linux; Android 10; SM-G973F) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.159 Mobile Safari/537.36",
        "timestamp": "2024-01-1813:13:59"
      }
    }
  ]
}
```

d) TEST RESPONSE

```
{
  "docs": [
    {
      "doc": {
        "_index": "web_traffic",
        "_version": "-3",
        "_id": "4unr_JIBmBaRB3mGRhkY",
        "_source": {
          "@timestamp": "2024-01-18T13:13:59.000Z",
          "ip": "3.168.38.114",
          "runtime_sec": 1.899,
          "http": {
            "request": {
              "method": "PUT"
            },
            "response": {
              "status_code": "301"
            }
          },
          "bytes_sent": "40901",
          "url": "https://www.elastic.co/blog/elasticsearch-7-0-0-released",
          "user_agent": {
            "name": "Chrome Mobile",
            "original": "Mozilla/5.0 (Linux; Android 10; SM-G973F) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.159 Mobile Safari/537.36",
            "os": {
              "name": "Android",
              "version": "10",
              "full": "Android 10"
            },
            "device": {
              "name": "Samsung SM-G973F"
            },
            "version": "92.0.4515.159"
          }
        },
        "_ingest": {
          "timestamp": "2025-01-10T09:02:32.441234945Z"
        }
      }
    }
  ]
}
```

e) Define Index

Once your pipeline is working, define a new index named web_traffic_fixed and configure web_traffic_pipeline as its default pipeline. Use the below mapping for the new index (copy-and-paste it into your PUT request):

REQUEST

```
PUT web_traffic_fixed
{
  "settings": {
    "default_pipeline": "web_traffic_pipeline"
  },
  "mappings": {
    "properties": {
      "@timestamp": {
        "type": "date"
      },
      "http": {
        "properties": {
          "request": {
            "properties": {
              "method": {
                "type": "keyword"
              }
            }
          },
          "response": {
            "properties": {
              "status_code": {
                "type": "keyword"
              }
            }
          }
        }
      },
      "ip": {
        "type": "ip"
      },
      "runtime_sec": {
        "type": "long"
      },
      "bytes_sent": {
        "type": "long"
      },
      "url": {
        "type": "keyword"
      },
      "user_agent": {
        "properties": {
          "device": {
            "properties": {
              "name": {
                "type": "keyword"
              }
            }
          },
          "name": {
            "type": "keyword"
          },
          "original": {
            "type": "keyword",
            "fields": {
              "text": {
                "type": "text"
              }
            }
          },
          "version": {
            "type": "keyword"
          },
          "os": {
            "properties": {
              "name": {
                "type": "keyword"
              },
              "full": {
                "type": "keyword"
              },
              "version": {
                "type": "keyword"
              }
            }
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
  "acknowledged": true,
  "shards_acknowledged": true,
  "index": "web_traffic_fixed"
}
```

## f) REINDEX Pipeline Test

Test your pipeline by running the following _reindex operation:
NOTE: By adding a query clause we limit the number of documents to reindex.

REQUEST

```
POST _reindex
{
  "source": {
    "index": "web_traffic",
    "query": {
      "match": {
        "@timestamp": "2015-05-28T19:44:05.000Z"
      }
    }
  },
  "dest": {
    "index": "web_traffic_fixed"
  }
}
```

RESPONSE

```
{
  "took": 66,
  "timed_out": false,
  "total": 1,
  "updated": 0,
  "created": 1,
  "deleted": 0,
  "batches": 1,
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
NOTE: Only a single document matches this timestamp which helps us to test our pipeline much faster.

g) Verify everything is working

In order to check your web_traffic_fixed index and verify everything is working, run a search request on the index and verify that your document has the correct structure.

REQUEST

```
GET web_traffic_fixed/_search
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
      "value": 1,
      "relation": "eq"
    },
    "max_score": 1,
    "hits": [
      {
        "_index": "web_traffic_fixed",
        "_id": "6enr_JIBmBaRB3mGRhkY",
        "_score": 1,
        "_source": {
          "@timestamp": "2015-05-28T19:44:05.000Z",
          "ip": "72.190.6.33",
          "runtime_sec": 2.827,
          "http": {
            "request": {
              "method": "POST"
            },
            "response": {
              "status_code": "200"
            }
          },
          "bytes_sent": "46464",
          "url": "https://www.elastic.co/blog/2014-05-07-this-week-in-elasticsearch",
          "user_agent": {
            "original": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/14.0.3 Safari/605.1.15",
            "os": {
              "name": "Mac OS X",
              "version": "10.15.7",
              "full": "Mac OS X 10.15.7"
            },
            "name": "Safari",
            "device": {
              "name": "Mac"
            },
            "version": "14.0.3"
          }
        }
      }
    ]
  }
}
```

## h) Delete

If the sample documents look correct, delete it by running the following command:

REQUEST

```
POST web_traffic_fixed/_delete_by_query
{
  "query": {
    "match_all": {}
  }
}
```

RESPONSE

```
{
  "took": 21,
  "timed_out": false,
  "total": 1,
  "deleted": 1,
  "batches": 1,
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

## i) Reindex

Reindex all the logs to this new index. Use the request parameter ?wait_for_completion=false to run the operation asynchronously.

REQUEST

```
POST _reindex?wait_for_completion=false
{
  "source": {
    "index": "web_traffic"
  },
  "dest": {
    "index": "web_traffic_fixed"
  }
}
```

RESPONSE

```
{
  "task": "your_task_ID_here:#####"
}
```
NOTE: It will take several minutes for all 1,324,128 documents to be indexed. Let it run and move on to the next step.

Create a data view in Kibana for your web_traffic_fixed index, using @timestamp as the time field.

<img width="529" height="387" alt="image" src="https://github.com/user-attachments/assets/1d45a942-fae9-43ab-9158-ec2fe0177455" />

<img width="533" height="394" alt="image" src="https://github.com/user-attachments/assets/daca226f-2085-4f59-b8b5-6575811a9224" />





# Summary

In this lab, you became familiar with ingest pipelines by performing the following tasks:

    Reindex, count, and delete documents
    Create and test an ingest pipeline
    Create an index and define its default pipeline



# Review

- In a Reindex request, you can add a query clause to only reindex a subset of the documents.
- The processors of an ingest pipeline are executed in sequence.
- This is how you set the default pipeline of the blogs_fixed index
  ```
  PUT blogs_fixed
  {
    "settings": {
      "default_pipeline": "blogs_pipeline"
    }
  }
  ```
  NOTE: This request configures the default pipeline of the blogs_fixed index so that any document that is indexed into blogs_fixed will pass through blogs_pipeline first. Remember that this is applied only if no other pipeline parameter is defined.

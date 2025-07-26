# 3.2. Term-level Queries

In this lesson, you’ll learn how to define term-level queries that return an exact match of the original string as it occurs in the documents. You will also implement queries to search for numbers, dates, and IPs by creating ranges. Finally, at the end of this lesson, you will perform a sync search to handle long-running searches.

In this lab, you will learn to write more advanced search requests by performing the following tasks:

    Write queries based on date
    Get a count of documents, and sort results based on _score
    Write queries for an exact match
    Run an asynchronous search


## a) Query Since a Date

Write a query on the blogs_fixed2 index to get the blogs written since 2018. Filter the _source to display only the title and publish_date.

REQUEST

```
GET blogs_fixed2/_search
{
  "_source": ["publish_date", "title"],
  "query": {
    "range": {
      "publish_date": {
        "gte": "2018-01-01"
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
      "value": 2383,
      "relation": "eq"
    },
    "max_score": 1,
    "hits": [
      {
        "_index": "blogs_fixed2",
        "_id": "66f55182c5f2efe517a6c42a",
        "_score": 1,
        "_source": {
          "title": "Azure Cloud Monitoring with the Elastic Stack | Elastic Blog",
          "publish_date": "2018-08-27T16:00:00.000Z"
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

## b) Query on a Date

Update the previous query to get the blogs written in 2018.

REQUEST

```
GET blogs_fixed2/_search
{
  "_source": ["publish_date", "title"],
  "query": {
    "range": {
      "publish_date": {
        "gte": "2018-01-01",
        "lt": "2019-01-01"
      }
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
      "value": 308,
      "relation": "eq"
    },
    "max_score": 1,
    "hits": [
      {
        "_index": "blogs_fixed2",
        "_id": "66f55182c5f2efe517a6c42a",
        "_score": 1,
        "_source": {
          "title": "Azure Cloud Monitoring with the Elastic Stack | Elastic Blog",
          "publish_date": "2018-08-27T16:00:00.000Z"
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

## c) Count

Write and run a query to count the number of documents that match Director of Engineering on the authors.job_title fields.

REQUEST

```
GET blogs_fixed2/_count
{
  "query": {
    "match": {
      "authors.job_title": "Director of Engineering"
    }
  }
}
```

RESPONSE

```
{
  "count": 512,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  }
}
```

## d) Search

Write and run a query that searches for the same documents. Filter the _source to display only the authors.job_title field and sort by ascending _score.

REQUEST

```
GET blogs_fixed2/_search
{
  "_source": ["authors.job_title"],
  "sort": [
    {
      "_score": {
        "order": "asc"
      }
    }
  ],
  "query": {
    "match": {
      "authors.job_title": "Director of Engineering"
    }
  }
}
```

RESPONSE

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
      "value": 512,
      "relation": "eq"
    },
    "max_score": null,
    "hits": [
      {
        "_index": "blogs_fixed2",
        "_id": "66f55453c5f2ef23b1ac6822",
        "_score": 0.54287577,
        "_source": {
          "authors": [
            {
              "job_title": "Product Manager, Security Core - Security Content"
            },
            {
              "job_title": "Director, Product Management for Elastic Security"
            },
            {
              "job_title": "Security Protections & Insights, Senior Product Manager"
            },
            {
              "job_title": "Senior Product Manager, Cloud Security, Runtime & Vulnerability"
            },
            {
              "job_title": ""
            },
            {
              "job_title": "Product Marketing Manager, Security Intelligence "
            }
          ]
        },
        "sort": [⇔]
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
NOTE: The documents that match the query each only contain one of the terms from the query. These blogs have not been written by a Director of Engineering. 

## e) Search for Exact Match

Write and run a query that uses an exact match to retrieve only the blogs written by someone whose job title is Director of Engineering.

REQUEST

```
GET blogs_fixed2/_search
{
  "query": {
    "term": {
      "authors.job_title.keyword": "Director of Engineering"
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
      "value": 83,
      "relation": "eq"
    },
    "max_score": 4.146516,
    "hits": [
      {
        "_index": "blogs_fixed2",
        "_id": "66f55208c5f2ef550fa7b167",
        "_score": 4.146516,
        "_source": {
          "title": "Just Enough Kafka for the Elastic Stack, Part 1 | Elastic Blog",
          "locale": "de-de,fr-fr,ja-jp,ko-kr,en-us",
          "content": """12 May 2016 Just Enough Kafka for the Elastic Stack, Part 1 By Suyog Rao • Tal Levy...",
          "seo": "This post talks about design considerations for integrating Kafka with the Elastic Stack.",
          "url": "https://www.elastic.co/blog/just-enough-kafka-for-the-elastic-stack-part1",
          "versions": [],
          "publish_date": "2016-05-12T13:09:15.000Z",
          "authors": [
            {
              "last_name": "Rao",
              "title": "Suyog Rao",
              "company": "Elastic",
              "first_name": "Suyog",
              "job_title": "Director of Engineering"
            },
            {⇔}
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

## f) Updated Search

Update the previous query to retrieve the last (end) 5 results. Display only the authors field and make sure that every blog is written by at least one Director of Engineering.

REQUEST

```
GET blogs_fixed2/_search
{
  "_source": [
    "authors"
  ],
  "from": 78,
  "query": {
    "term": {
      "authors.job_title.keyword": "Director of Engineering"
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
      "value": 83,
      "relation": "eq"
    },
    "max_score": 4.146516,
    "hits": [
      {
        "_index": "blogs_fixed2",
        "_id": "66f55302c5f2ef96a0a9930f",
        "_score": 4.146516,
        "_source": {
          "authors": [
            {
              "last_name": "Rao",
              "title": "Suyog Rao",
              "company": "Elastic",
              "first_name": "Suyog",
              "job_title": "Director of Engineering"
            }
          ]
        }
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
In the next steps, let's explore how to run an asynchronous search.

## g) Function Score Query

Run the following query which uses function_score to customize how Elasticsearch computes the _score for each document

REQUEST

```
GET blogs_fixed2/_search
{
  "query": {
    "function_score": {
      "query": {
        "match": {
          "content": "to the blog and your query: you are both enjoying being on Elasticsearch "
        }
      },
      "script_score": {
        "script": """
        int m = 1;
        double u = 1.0;
        for (int x = 0; x < m; ++x)
          for (int y = 0; y < 10000; ++y)
            u=Math.log(y);
        return u
        """
      }
    }
  }
}
```

RESPONSE

```
{
  "took": 1011,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 3669,
      "relation": "eq"
    },
    "max_score": 84.503914,
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
NOTE: Increasing the value of m increases the took time. Since m = 1 took about 1000ms, then m=30 should take about 30000ms (30 seconds). 

## h) Asynchronous Search

Update m = 30 and run an asynchronous search request on this query so that you can let the query run in the background and retrieve its results later.

REQUEST

```
POST blogs_fixed2/_async_search
{
  "query": {
    "function_score": {
      "query": {
        "match": {
          "content": "to the blog and your query: you are both enjoying being on Elasticsearch "
        }
      },
      "script_score": {
        "script": """
        int m = 30;
        double u = 1.0;
        for (int x = 0; x < m; ++x)
          for (int y = 0; y < 10000; ++y)
            u=Math.log(y);
        return u
        """
      }
    }
  }
}
```

RESPONSE

```
{
  "id": "your_search_ID_here",
  "is_partial": true,
  "is_running": true,
  "start_time_in_millis": 1732223663176,
  "expiration_time_in_millis": 1732655663176,
  "response": {
    "took": 1017,
    "timed_out": false,
    "terminated_early": false,
    "num_reduce_phases": 0,
    "_shards": {
      "total": 1,
      "successful": 0,
      "skipped": 0,
      "failed": 0
    },
    "hits": {
      "total": {
        "value": 0,
        "relation": "gte"
      },
      "max_score": null,
      "hits": []
    }
  }
}
```

## i) _async_search

Copy the id value from the response and retrieve the search results.
The command will be similar to the following with your particular id instead. 

REQUEST

```
GET _async_search/your_search_ID_here
```

RESPONSE

```
{
  "id": "your_search_ID_here",
  "is_partial": false,
  "is_running": false,
  "start_time_in_millis": 1732223663176,
  "expiration_time_in_millis": 1732655663176,
  "completion_time_in_millis": 1732223688394,
  "response": {
    "took": 25218,
    "timed_out": false,
    "_shards": {
      "total": 1,
      "successful": 1,
      "skipped": 0,
      "failed": 0
    },
    "hits": {
      "total": {
        "value": 3669,
        "relation": "eq"
      },
      "max_score": 84.503914,
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
}
```
NOTE: If you sent your request before the query finished running, you will notice that the hits.hits in your response are still empty. Wait about 30 seconds and send the request again. Eventually, the is_running value will be false, and the hits.hits will contain some documents.
INFO: If you attempt retrieve the search results again, you can still see the results. This is because the results are stored on your cluster until the expiration_time_in_millis, or until you manually delete it yourself.

## j) Delete

To clear up the space which is being taken up on your disk, delete the results of this search.

REQUEST

```
DELETE _async_search/your_search_ID_here
```

RESPONSE

```
{
  "acknowledged": true
}
```

# Summary

In this lab, you learned to write more advanced search requests by performing the following tasks:

    Write queries based on date
    Get a count of documents, and sort results based on _score
    Write queries for an exact match
    Run an asynchronous search

# Review

- The Query DSL enables you to do any kind of query, including Full Text Queries, Term-Level Queries and Combining Querys.
- In an asynchronous search, is_partial is always set to true while the query is being executed.
- In a range query, you can set up open-ended ranges such as “prices less than $50” or dates greater than or equal to January 1st, 2023.” 

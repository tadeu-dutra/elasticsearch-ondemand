# Lesson 2.2: Mapping

In this lesson, you’ll learn about mappings, a way for Elasticsearch to decide how to define fields when indexing and storing data. You will also learn how to define your own custom mappings as well as mapping optimization techniques.

In this lab, you will work through the process of defining a custom mapping by performing the following tasks:

    Index a sample document and create a new index and iew mappings of the index
    Create an index with configured mappings and index a sample document into the index
    Copy and modify mappings from one index to another
    Reindex documents from one index to another
    Compare queries on different indices

## a) Index Sample Document

REQUEST:

```
POST sample_blog/_doc
{
  "@timestamp": "2021-03-10T16:00:00.000Z",
  "abstract": "The Joy of Painting",
  "author": "Bob Ross",
  "body": "Painting should do one thing. It should put happiness in your heart. We'll take a little bit of Van Dyke Brown. Isn't that fantastic? You can just push a little tree out of your brush like that. Mix your color marbly don't mix it dead.",
  "body_word_count": 55,
  "category": "Painting",
  "title": "Making Happy Little Trees",
  "url": "/blog/happy-little-trees",
  "published": true
}
```

RESPONSE:
```
{
  "_index": "sample_blog",
  "_id": "5rr9IZMBZeSLGhuoJXsW",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 2,
    "failed": 0
  },
  "_seq_no": 0,
  "_primary_term": 1
}
```

## b) View Mappings

Send a request to view the default mappings that were created.

REQUEST:

```
GET sample_blog/_mapping
```

RESPONSE:
```
{
  "sample_blog": {
    "mappings": {
      "properties": {
        "@timestamp": {
          "type": "date"
        },
        "abstract": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "author": {⇔},
        "body": {⇔},
        "body_word_count": {⇔},
        "published": {⇔},
        "url": {⇔}
      }
    }
  }
}
```
NOTE: Elasticsearch did its best to guess the data types, but notice a lot of the fields are of the text and keyword types. 

## c) Create a new index based on a previously created mapping

Create a new index called test_blogs based on the sample_blog mapping. Configure test_blogs to satisfy the following requirements:

    @timestamp is a date
    body_word_count is an integer
    abstract, body, and title are text only
    author, category, and url are keyword only
    published is boolean

REQUEST:

```
PUT test_blogs
{
  "mappings": {
    "properties": {
      "@timestamp": {
        "type": "date"
      },
      "abstract": {
        "type": "text"
      },
      "author": {
        "type": "keyword"
      },
      "body": {
        "type": "text"
      },
      "body_word_count": {
        "type": "integer"
      },
      "category": {
        "type": "keyword"
      },
      "title": {
        "type": "text"
      },
      "url": {
        "type": "keyword"
      },
      "published": {
        "type": "boolean"
      }
    }
  }
}
```

RESPONSE:

```
{
  "acknowledged": true,
  "shards_acknowledged": true,
  "index": "test_blogs"
}
```

## d) Index the sample document from step 1 into your new test_blogs index.

REQUEST:

```
POST test_blogs/_doc
{
  "@timestamp": "2021-03-10T16:00:00.000Z",
  "abstract": "The Joy of Painting",
  "author": "Bob Ross",
  "body": "Painting should do one thing. It should put happiness in your heart. We'll take a little bit of Van Dyke Brown. Isn't that fantastic? You can just push a little tree out of your brush like that. Mix your color marbly don't mix it dead.",
  "body_word_count": 55,
  "category": "Painting",
  "title": "Making Happy Little Trees",
  "url": "/blog/happy-little-trees",
  "published": "true"
}
```

RESPONSE:

```
{
  "_index": "test_blogs",
  "_id": "57oNIpMBZeSLGhuoTHt0",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 2,
    "failed": 0
  },
  "_seq_no": 0,
  "_primary_term": 1
}
```
NOTE: The document should be indexed without any issues with the mapping or data types.

## e) Create New Index

Now let's make some changes to the blogs index. First, create a new blogs_fixed index.

REQUEST:

```
PUT blogs_fixed
```

RESPONSE:

```
{
  "acknowledged": true,
  "shards_acknowledged": true,
  "index": "blogs_fixed"
}
```

## f) View Mappings

View the current mapping of blogs. What type are most of the fields mapped as?

REQUEST:

```
GET blogs/_mapping
```

RESPONSE:

```
{
  "blogs": {
    "mappings": {
      "properties": {
        "authors": {
          "properties": {
            "company": {
              "type": "text",
              "fields": {
                "keyword": {
                  "type": "keyword",
                  "ignore_above": 256
                }
              }
            },
            "first_name": {⇔},
            "job_title": {⇔},
            "last_name": {⇔},
            "title": {⇔}
          }
        },
        "category": {⇔},
        "content": {⇔},
        "locale": {⇔},
        "publish_date": {⇔},
        "seo": {⇔},
        "title": {⇔},
        "url": {⇔},
        "versions": {⇔}
      }
    }
  }
}
```
NOTE: Many fields are mapped as both text and keyword. This is not very efficient, since most of these fields will not need both. 

## g) Copy Mappings

Write a request to copy the existing mappings from the blogs index to the blogs_fixed index, but do not run the command.

REQUEST:

```
PUT blogs_fixed/_mapping
{
  "properties": {
    "authors": {
      "properties": {
        "company": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "first_name": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "job_title": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "last_name": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "title": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        }
      }
    },
    "category": {
      "type": "text",
      "fields": {
        "keyword": {
          "type": "keyword",
          "ignore_above": 256
        }
      }
    },
    "content": {
      "type": "text",
      "fields": {
        "keyword": {
          "type": "keyword",
          "ignore_above": 256
        }
      }
    },
    "locale": {
      "type": "text",
      "fields": {
        "keyword": {
          "type": "keyword",
          "ignore_above": 256
        }
      }
    },
    "publish_date": {
      "type": "date",
      "format": "iso8601"
    },
    "seo": {
      "type": "text",
      "fields": {
        "keyword": {
          "type": "keyword",
          "ignore_above": 256
        }
      }
    },
    "title": {
      "type": "text",
      "fields": {
        "keyword": {
          "type": "keyword",
          "ignore_above": 256
        }
      }
    },
    "url": {
      "type": "text",
      "fields": {
        "keyword": {
          "type": "keyword",
          "ignore_above": 256
        }
      }
    },
    "versions": {
      "type": "text",
      "fields": {
        "keyword": {
          "type": "keyword",
          "ignore_above": 256
        }
      }
    }
  }
}
```

Modify the mapping for blogs_fixed so that it satisfies the following requirements:

    first_name, last_name, and company under the authors object are keyword only
    authors.title is text only
    content and seo are text only
    locale, url, versions, and category are keyword only


Modified Mapping:

REQUEST

```
PUT blogs_fixed/_mapping
{
  "properties": {
    "authors": {
      "properties": {
        "company": {
          "type": "keyword"
        },
        "first_name": {
          "type": "keyword"
        },
        "job_title": {
          "type": "text",
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
          "type": "text"
        }
      }
    },
    "category": {
      "type": "keyword"
    },
    "content": {
      "type": "text"
    },
    "locale": {
      "type": "keyword"
    },
    "publish_date": {
      "type": "date",
      "format": "iso8601"
    },
    "seo": {
      "type": "text"
    },
    "title": {
      "type": "text",
      "fields": {
        "keyword": {
          "type": "keyword",
          "ignore_above": 256
        }
      }
    },
    "url": {
      "type": "keyword"
    },
    "versions": {
      "type": "keyword"
    }
  }
}
```

Run the request to add the new mapping as defined below to blogs_fixed index. 

RESPONSE:

```
{
  "acknowledged": true
}
```
NOTE: The blogs_fixed index does not have any data in it yet. You will index data into it next.

## h) Reindex Documents

REQUEST:

```
POST _reindex
{
  "source": {
    "index": "blogs"
  },
  "dest": {
    "index": "blogs_fixed"
  }
}
```

RESPONSE:

```
{
  "took": 7555,
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
NOTE: The reindex request will take a few moments but should run fairly quickly. If it times out, do not panic, and do not run the reindex command again. It just means it took more than 1 minute, and Console stopped waiting for the response. The request will continue to run in the background though. 

i) Document Request

Run a request to see how many documents are in blogs_fixed.

REQUEST:

```
GET blogs_fixed/_count
```

RESPONSE:

```
    {
      "count": 3687,
      "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
      }
    }
```

j) Compare Queries

Run two queries for kim in the authors.first_name field, one for the blogs index and the other for the blogs_fixed index. Confirm the changes were successful by comparing both requests.

blogs REQUEST

```
GET blogs/_search
{
  "query": {
    "match": {
      "authors.first_name": "kim"
    }
  }
}
```

blogs RESPONSE

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
      "value": 2,
      "relation": "eq"
    },
    "max_score": 8.234724,
    "hits": [⇔]
  }
}
```
NOTE: The first query should have 2 hits. This is because queries on text fields are case-insensitive.

blogs_fixed REQUEST

```
GET blogs_fixed/_search
{
  "query": {
    "match": {
      "authors.first_name": "kim"
    }
  }
}
```

blogs_fixed RESPONSE

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
      "value": 0,
      "relation": "eq"
    },
    "max_score": null,
    "hits": []
  }
}
```
NOTE: The second query should have 0 hits. This is because queries on keyword fields are case-sensitive.

k) Case-Sensitive Query

Run a query for Kim (with a capital K) as the authors.first_name in blogs_fixed.

REQUEST

```
GET blogs_fixed/_search
{
  "query": {
    "match": {
      "authors.first_name": "Kim"
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
      "value": 2,
      "relation": "eq"
    },
    "max_score": 8.16025,
    "hits": [⇔]
  }
}
```

# Summary

In this lab, you will work through the process of defining a custom mapping by performing the following tasks:

    Index a sample document and create a new index and iew mappings of the index
    Create an index with configured mappings and index a sample document into the index
    Copy and modify mappings from one index to another
    Reindex documents from one index to another
    Compare queries on different indices

# Review

- Each index will have its own mappings, which lets developers tune each index for a specific use case.
- A mapping cannot be changed. (i.e., You cannot change a field’s data type from integer to long).
- Creating a custom mapping will produce savings in search and index speed, as well as memory and storage requirements. It will not, however, make any changes to the _source.
  - Creating a custom mapping may improve search and index speeds
  - Creating a custom mapping may improve memory and storage requirements
  - Creating a custom mapping doesn't change the _source containing the original JSON object

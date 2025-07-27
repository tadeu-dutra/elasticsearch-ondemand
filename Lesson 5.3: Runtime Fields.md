# 5.3: Runtime Fields

In this lesson, you’ll learn how to create and use runtime fields using Elasticsearch’s scripting language: Painless.

In this lab, you will learn how to write Painless scripts by performing the following tasks:

    Write and run a script queries using functions
    Write and run a search that defines runtime mappings
    Run a query using runtime fields to temporarily index a disabled field
    Write and run a search using runtime fields to change a mapping for the search

## Script Query

Run the following script query, which is really just a match_all query:

REQUEST

```
GET blogs_fixed2/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "script": {
            "script": """
              return true;
            """
          }
        }
      ]
    }
  }
}
```

RESPONSE

```
{
  "took": 38,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 3500,
      "relation": "eq"
    },
    "max_score": 0,
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

## b) Query

Now write a script query that satisfies the following requirements:

    Returns all blogs where the number of versions of the blog is greater than or equal to 3
    Uses the size() function of the versions field to determine the number of versions

REQUEST

```
GET blogs_fixed2/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "script": {
            "script": """
              return doc['versions'].size() >= 3;
            """
          }
        }
      ]
    }
  }
}
```

RESPONSE

```
{
  "took": 84,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 28,
      "relation": "eq"
    },
    "max_score": 0,
    "hits": [⇔]
  }
}
```

## c) Query

Write a script query that satisfies the following requirements:

    Returns all blogs where the authors.last_name field starts with the letter K
    Uses the startsWith() function

TIP: The authors field is an array, so you must iterate through all of the elements.

REQUEST

```
GET blogs_fixed2/_search
{
  "_source": "authors", 
  "query": {
    "bool": {
      "filter": [
        {
          "script": {
            "script": """
              def authors = doc['authors.last_name'];
              for (author in authors) {
                if (author.startsWith('K')) {
                  return true;
                }
              }
              return false;
            """
          }
        }
      ]
    }
  }
}
```

RESPONSE

```
{
  "took": 53,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 351,
      "relation": "eq"
    },
    "max_score": 0,
    "hits": [⇔]
  }
}
```

## d) Query

Now write a script query that satisfies the following requirements:

    Returns all blogs where the title of the blog is greater than or equal to 200 characters
    Uses the length() function of the value of the title.keyword field to determine the number of characters

REQUEST

```
GET blogs_fixed2/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "script": {
            "script": """
                      return doc['title.keyword'].value.length() >= 200;
                    """
          }
        }
      ]
    }
  }
}
```

RESPONSE

```
{
  "took": 104,
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
    "max_score": 0,
    "hits": [⇔]
  }
}
```
NOTE: The query should match 1 hit. 

## e) Search

Suppose you want to determine which day of the week had the most blog postings. Currently, we do not know which day of the week a blog was posted. We know the publish_date field in an ISO date format. If this is not an aggregation we will run often, we can use a runtime field to do the calculation.

Run a search on blogs_fixed2 that satisfies the following requirements:

    Defines a runtime_mappings named day_of_week of the keyword type
    Uses the following Painless code to calculate the day of the week from the publish_date field:

      "doc['publish_date'].value.getDayOfWeekEnum().toString()"

    Contains a terms aggregation on the day_of_week field

REQUEST

```
GET blogs_fixed2/_search
{
  "size": 0,
  "runtime_mappings": {
    "day_of_week": {
      "type": "keyword",
      "script": {
        "source": "emit(doc['publish_date'].value.getDayOfWeekEnum().toString())"
      }
    }
  },
  "aggs": {
    "top_days": {
      "terms": {
        "field": "day_of_week"
      }
    }
  }
}
```

RESPONSE

```
{
  "took": 83,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 3500,
      "relation": "eq"
    },
    "max_score": null,
    "hits": []
  },
  "aggregations": {
    "top_days": {
      "doc_count_error_upper_bound": 0,
      "sum_other_doc_count": 0,
      "buckets": [
        {
          "key": "TUESDAY",
          "doc_count": 888
        },
        {
          "key": "WEDNESDAY",
          "doc_count": 883
        },
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
NOTE: You should see that Tuesday is the most popular day to publish a blog, followed closely by Wednesday. 

## f) Query

INFO: Another handy use of runtime fields is to temporarily access a field that was disabled during indexing.

Try running the following query which returns the blogs where the url field is longer than 100 characters.

REQUEST

```
GET blogs_fixed2/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "script": {
            "script": """
                      return doc['url'].value.length() >= 100;
                    """
          }
        }
      ]
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
        "type": "script_exception",
        "reason": "runtime error",
        "script_stack": [
          "org.elasticsearch.server@8.15.3/org.elasticsearch.index.mapper.MappedFieldType.failIfNoDocValues(MappedFieldType.java:482)",
          "org.elasticsearch.server@8.15.3/org.elasticsearch.index.mapper.KeywordFieldMapper$KeywordFieldType.fielddataBuilder(KeywordFieldMapper.java:643)",
          "org.elasticsearch.server@8.15.3/org.elasticsearch.index.fielddata.IndexFieldDataService.getForField(IndexFieldDataService.java:94)",
          "org.elasticsearch.server@8.15.3/org.elasticsearch.index.IndexService.loadFielddata(IndexService.java:1372)",
          "org.elasticsearch.server@8.15.3/org.elasticsearch.index.query.SearchExecutionContext.lambda$setLookupProviders$2(SearchExecutionContext.java:515)",
          "org.elasticsearch.server@8.15.3/org.elasticsearch.search.lookup.SearchLookup.getForField(SearchLookup.java:139)",
          "org.elasticsearch.server@8.15.3/org.elasticsearch.search.lookup.LeafDocLookup.lambda$getFactoryForDoc$1(LeafDocLookup.java:169)",
          "java.base/java.security.AccessController.doPrivileged(AccessController.java:319)",
          "org.elasticsearch.server@8.15.3/org.elasticsearch.search.lookup.LeafDocLookup.getFactoryForDoc(LeafDocLookup.java:150)",
          "org.elasticsearch.server@8.15.3/org.elasticsearch.search.lookup.LeafDocLookup.get(LeafDocLookup.java:185)",
          "org.elasticsearch.server@8.15.3/org.elasticsearch.search.lookup.LeafDocLookup.get(LeafDocLookup.java:32)",
          """return doc['url'].value.length() >= 100;
                    """,
          "           ^---- HERE"
        ],
        "script": " ...",
        "lang": "painless",
        "position": {
          "offset": 34,
          "start": 23,
          "end": 84
        }
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
        "node": "0EZxI-9HQua7XUJisw5Qgw",
        "reason": {
          "type": "script_exception",
          "reason": "runtime error",
          "script_stack": [
            "org.elasticsearch.server@8.15.3/org.elasticsearch.index.mapper.MappedFieldType.failIfNoDocValues(MappedFieldType.java:482)",
            "org.elasticsearch.server@8.15.3/org.elasticsearch.index.mapper.KeywordFieldMapper$KeywordFieldType.fielddataBuilder(KeywordFieldMapper.java:643)",
            "org.elasticsearch.server@8.15.3/org.elasticsearch.index.fielddata.IndexFieldDataService.getForField(IndexFieldDataService.java:94)",
            "org.elasticsearch.server@8.15.3/org.elasticsearch.index.IndexService.loadFielddata(IndexService.java:1372)",
            "org.elasticsearch.server@8.15.3/org.elasticsearch.index.query.SearchExecutionContext.lambda$setLookupProviders$2(SearchExecutionContext.java:515)",
            "org.elasticsearch.server@8.15.3/org.elasticsearch.search.lookup.SearchLookup.getForField(SearchLookup.java:139)",
            "org.elasticsearch.server@8.15.3/org.elasticsearch.search.lookup.LeafDocLookup.lambda$getFactoryForDoc$1(LeafDocLookup.java:169)",
            "java.base/java.security.AccessController.doPrivileged(AccessController.java:319)",
            "org.elasticsearch.server@8.15.3/org.elasticsearch.search.lookup.LeafDocLookup.getFactoryForDoc(LeafDocLookup.java:150)",
            "org.elasticsearch.server@8.15.3/org.elasticsearch.search.lookup.LeafDocLookup.get(LeafDocLookup.java:185)",
            "org.elasticsearch.server@8.15.3/org.elasticsearch.search.lookup.LeafDocLookup.get(LeafDocLookup.java:32)",
            """return doc['url'].value.length() >= 100;
                    """,
            "           ^---- HERE"
          ],
          "script": " ...",
          "lang": "painless",
          "position": {
            "offset": 34,
            "start": 23,
            "end": 84
          },
          "caused_by": {
            "type": "illegal_argument_exception",
            "reason": "Can't load fielddata on [url] because fielddata is unsupported on fields of type [keyword]. Use doc values instead."
          }
        }
      }
    ]
  },
  "status": 400
}
```
NOTE: Using runtime fields, you can temporarily index an existing field by simply referring to that field in the runtime_mappings section.

## g) Search

Run the following search, which causes the url field to be indexed as a keyword with doc_values enabled just for the execution of this search request:

REQUEST

```
GET blogs_fixed2/_search
{
  "runtime_mappings": {
    "url": {
      "type": "keyword"
    }
  },
  "query": {
    "bool": {
      "filter": [
        {
          "script": {
            "script": """
                      return doc['url'].value.length() >= 100;
                    """
          }
        }
      ]
    }
  }
}
```
NOTE: You can use runtime fields to change the mapping of a field just for a specific search request. For example, the authors.title field is currently text, but you can still search it as a keyword field.

## i) Search

Write a search on blogs that queries the authors.title field as a keyword field for the value "Jongmin Kim".

REQUEST

```
GET blogs_fixed2/_search
{
  "runtime_mappings": {
    "authors.title": {
      "type": "keyword"
    }
  },
  "query": {
    "match": {
      "authors.title": "Jongmin Kim"
    }
  }
}
```

RESPONSE

```
{
  "took": 204,
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
    "max_score": 1,
    "hits": [⇔]
  }
}
```

## j) Query

Try running the same query without the runtime mapping to see the difference.

REQUEST

```
GET blogs_fixed2/_search
{
  "query": {
    "match": {
      "authors.title": "Jongmin Kim"
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
      "value": 17,
      "relation": "eq"
    },
    "max_score": 14.058631,
    "hits": [⇔]
  }
}
```

# Summary

In this lab, you learned how to write Painless scripts by performing the following tasks:

    Write and run a script queries using functions
    Write and run a search that defines runtime mappings
    Run a query using runtime fields to temporarily index a disabled field
    Write and run a search using runtime fields to change a mapping for the search


# Review

- Instead of hard-coding values, use named params to make scripts flexible, and also reduce compilation time when the script runs.
- You can add fields to existing documents without reindexing your data.
- You can directly define a runtime mapping at query time. You don't need to define runtime fields in advance.

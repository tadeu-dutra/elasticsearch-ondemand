# 5.2: Enriching Data

In this lesson, you’ll learn about different use cases for enriching data. One particular use case is how to denormalize data into a single index in order to improve the response time of a query. In this lesson, you will also learn how to denormalize data using the enrich processor in an ingest node pipeline.


In this lab, you will learn how to enrich an index by performing the following tasks:

    Create an index that maps IDs to names
    Create and execute an enrich policy
    Create an ingest pipeline that uses the enrich policy
    Add an object to the index mapping
    Update the documents using the ingest pipeline



## a) Terms Aggregation

Run the terms aggregation on the category field of the blogs_fixed2 index.

REQUEST

```
GET blogs_fixed2/_search
{
  "size": 0,
  "aggs": {
    "NAME": {
      "terms": {
        "field": "category",
        "size": 10
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
      "value": 3500,
      "relation": "eq"
    },
    "max_score": null,
    "hits": []
  },
  "aggregations": {
    "NAME": {
      "doc_count_error_upper_bound": 0,
      "sum_other_doc_count": 0,
      "buckets": [
        {
          "key": "bltfaae4466058cc7d6",
          "doc_count": 660
        },
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

## b) _bulk command

Using the _bulk command, create a new index named categories that maps the IDs to their actual category names below:

```
ID Categories:

{"uid": "blt26ff0a1ade01f60d","title":"customers"}
{"uid": "bltfaae4466058cc7d6","title": "releases"}
{"uid": "bltc253e0851420b088","title": "culture"}
{"uid": "blt0c9f31df4f2a7a2b","title": "company-news"}
{"uid": "blt1d90b8e0edce3ea9","title": "how-to"}
{"uid": "blt3f90b5g1edce6vd4","title": "product"}
{"uid": "blt1dcv54a0edce456a","title": "business"}
```

REQUEST

```
POST categories/_bulk
{"create":{}}
{"uid": "blt26ff0a1ade01f60d","title":"customers"}
{"create":{}}
{"uid": "bltfaae4466058cc7d6","title": "releases"}
{"create":{}}
{"uid": "bltc253e0851420b088","title": "culture"}
{"create":{}}
{"uid": "blt0c9f31df4f2a7a2b","title": "company-news"}
{"create":{}}
{"uid": "blt1d90b8e0edce3ea9","title": "how-to"}
{"create":{}}
{"uid": "blt3f90b5g1edce6vd4","title": "product"}
{"create":{}}
{"uid": "blt1dcv54a0edce456a","title": "business"}
```

RESPONSE

```
{
  "errors": false,
  "took": 401,
  "items": [
    {
      "create": {
        "_index": "categories",
        "_id": "duopiJMB5hu1ZsyHmgOH",
        "_version": 1,
        "result": "created",
        "_shards": {
          "total": 2,
          "successful": 1,
          "failed": 0
        },
        "_seq_no": 0,
        "_primary_term": 1,
        "status": 201
      }
    },
    {⇔},
    {⇔},
    {⇔},
    {⇔},
    {⇔},
    {⇔}
  ]
}
```

## c) Create Enrich Policy

Create an enrich policy that satisfies the following requirements:

    The name of the policy is categories_policy
    The match field is the uid field of the categories index
    The enrich field is the title field

REQUEST

```
PUT _enrich/policy/categories_policy
{
  "match": {
    "indices": "categories",
    "match_field": "uid",
    "enrich_fields": ["title"]
  }
}
```

RESPONSE

```
{
  "acknowledged": true
}
```

## d) Execute Policy

Execute the policy to create an enrich index.

REQUEST

```
POST _enrich/policy/categories_policy/_execute
```

RESPONSE

```
{
  "status": {
    "phase": "COMPLETE"
  }
}
```

## e) Create a new ingest pipeline

Pipeline Requirements

    Is named categories_pipeline
    Uses an enrich processor with the categories_policy policy to map the existing category field to a new field named category_title
    Removes the original category field
    Ignore documents that don't have a category field

Use the Ingest Node Pipeline UI to define the pipeline in Kibana.

<img width="496" height="376" alt="image" src="https://github.com/user-attachments/assets/ee9d4330-cef9-4877-8ce5-43b3057ecf9e" />
<img width="494" height="378" alt="image" src="https://github.com/user-attachments/assets/bdcff604-202f-475a-adfb-3b1447560d2e" />
<img width="496" height="375" alt="image" src="https://github.com/user-attachments/assets/94e1c6e2-d759-451b-95e4-5e8d2c6273dd" />

If you want to skip that step you can copy-and-paste the following PUT command into Console.

REQUEST

```
PUT _ingest/pipeline/categories_pipeline
{
  "processors": [
    {
      "enrich": {
        "field": "category",
        "policy_name": "categories_policy",
        "target_field": "category_title",
        "ignore_missing": true
      }
    },
    {
      "remove": {
        "field": "category",
        "ignore_missing": true
      }
    }
  ]
}
```

RESPONSE

```
{
  "acknowledged": true
}
```

## f) Add Object

Add object category_title with fields title and uid (both of type keyword) to the blogs_fixed2 index mapping.

REQUEST

```
PUT blogs_fixed2/_mapping
{
  "properties": {
    "category_title": {
      "properties": {
        "title": {
          "type": "keyword"
        },
        "uid": {
          "type": "keyword"
        }
      }
    }
  }
}
```

RESPONSE

```
{
  "acknowledged": true
}
```

## g) Update by Query API

Using the Update by Query API, run all the documents in blogs_fixed2 through your categories_pipeline.

REQUEST

```
POST blogs_fixed2/_update_by_query?pipeline=categories_pipeline&wait_for_completion=false
```

RESPONSE

```
{
  "task": "your_task_ID_here:######"
}
```

## h) Terms Aggregation

Run a terms aggregation on the category_title.title field and verify you enriched the index.

REQUEST

```
GET blogs_fixed2/_search
{
  "size": 0,
  "aggs": {
    "blogs_by_category": {
      "terms": {
        "field": "category_title.title",
        "size": 10
      }
    }
  }
}
```

RESPONSE

```
{
  "took": 7,
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
    "blogs_by_category": {
      "doc_count_error_upper_bound": 0,
      "sum_other_doc_count": 0,
      "buckets": [
        {
          "key": "releases",
          "doc_count": 660
        },
        {
          "key": "how-to",
          "doc_count": 404
        },
        {
          "key": "company-news",
          "doc_count": 358
        },
        {
          "key": "product",
          "doc_count": 253
        },
        {
          "key": "culture",
          "doc_count": 247
        },
        {
          "key": "business",
          "doc_count": 245
        },
        {
          "key": "customers",
          "doc_count": 192
        }
      ]
    }
  }
}
```

# Summary

In this lab, you learned how to enrich an index by performing the following tasks:

    Create an index that maps IDs to names
    Create and execute an enrich policy
    Create an ingest pipeline that uses the enrich policy
    Add an object to the index mapping
    Update the documents using the ingest pipeline


# Review

- Denormalizing your data enables you to quickly and easily search across multiple fields in a single query without joining datasets.
- How do you create an enrich index? Create an enrich policy then execute it. The enriched index is created from a policy. The enriched index can then be used in an ingest pipeline in the enrich processor to add detailed data to incoming documents.
- Once created, you can’t update or change an enrich policy. Create and execute a new enrich policy.

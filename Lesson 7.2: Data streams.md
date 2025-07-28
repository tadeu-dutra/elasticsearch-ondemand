# 7.2: Data streams

In this lesson, you’ll learn how data streams provide an additional layer of abstraction to simplify and separate time-series data coming from many data sources.

In this lab, you will learn about data streams by performing the following tasks:

    Clone an index template and add a data stream
    Update a component template to add a data stream field
    Roll over the data stream and index a document into the data stream
    View the mappings for the backing indices
    Add an integration to automatically create a data stream


## a) Within Kibana2, go to Stack Management > Index Management.

Next, you will create an index template that meets the following requirements:

    Clones the existing my-metrics-template
    The name of the new template is metrics-custom-template
    The index pattern is metrics-custom-*
    Creates a data stream
    Keeps all other settings of my-metrics-template

<img width="914" height="654" alt="image" src="https://github.com/user-attachments/assets/c7f653b8-801d-4d04-bdb4-157db0eedd40" />

TIP: You can also complete this task by running the following command in Console:

REQUEST

```
PUT _index_template/metrics-custom-template
  {
    "priority": 500,
    "index_patterns": [
      "metrics-custom-*"
    ],
    "data_stream": {},
    "composed_of": [
      "time-series-mappings",
      "time-series-settings"
    ]
  }
```

RESPONSE

```
{
  "acknowledged": true
}
```

## b) In Console, run the following command to create the data stream:

REQUEST

```
POST metrics-custom-labenv/_doc
{
  "@timestamp": "2021-07-04",
  "status": "UP",
  "message": "Service is running."
}
```

RESPONSE

```
{
  "_index": ".ds-metrics-custom-labenv-2024.11.08-000001",
  "_id": "pSKft5MByocUwy6Q4BdC",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 3,
    "successful": 1,
    "failed": 0
  },
  "_seq_no": 0,
  "_primary_term": 1
}
```

Next, you will edit the time-series-mappings component template by adding a field named data_stream.type, which will be a constant_keyword type, and save the changes.


## c) From the Index Management page, open the Component Templates tab.

<img width="1454" height="691" alt="image" src="https://github.com/user-attachments/assets/1faa6267-cad6-4e90-884c-63af8c44d579" />

TIP: You can also solve this task by running the following command in Console:

REQUEST

```
PUT _component_template/time-series-mappings
{
  "template": {
    "mappings": {
      "properties": {
        "@timestamp": {
          "type": "date"
        },
        "message": {
          "type": "text"
        },
        "status": {
          "type": "keyword"
        },
        "data_stream.type": {
          "type": "constant_keyword"
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

## d) Run the following command in Console to view the mappings for all the backing indices:

REQUEST:

```
GET metrics-custom-labenv/_mapping
```

RESPONSE:

```
{
  ".ds-metrics-custom-labenv-2024.12.10-000001": {
    "mappings": {
      "_data_stream_timestamp": {
        "enabled": true
      },
      "properties": {
        "@timestamp": {
          "type": "date"
        },
        "message": {
          "type": "text"
        },
        "status": {
          "type": "keyword"
        }
      }
    }
  }
}
```
NOTE: Notice that the mapping of the initial backup index has not been updated.

## e) Roll Over

Manually roll over the data stream.

REQUEST

```
POST metrics-custom-labenv/_rollover/
```

RESPONSE

```
{
  "acknowledged": true,
  "shards_acknowledged": true,
  "old_index": ".ds-metrics-custom-labenv-2024.12.10-000001",
  "new_index": ".ds-metrics-custom-labenv-2024.12.10-000002",
  "rolled_over": true,
  "dry_run": false,
  "lazy": false,
  "conditions": {}
}
```

## f) Index a document into the data stream

Run the following command to index a document into the data stream:

REQUEST

```
POST metrics-custom-labenv/_doc
{
  "@timestamp": "2021-07-05",
  "status": "UP",
  "message": "Service is running.",
  "data_stream.type": "metrics"
}
```

RESPONSE

```
{
  "_index": ".ds-metrics-custom-labenv-2024.12.10-000002",
  "_id": "hrgRspMBIOLfbEt20Dwh",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 3,
    "successful": 3,
    "failed": 0
  },
  "_seq_no": 0,
  "_primary_term": 1
}
```
NOTE: This document will be indexed in a different index than the first document.

## g) View Mappings

View the mappings for all the backing indices again.

REQUEST

```
GET metrics-custom-labenv/_mapping
```

RESPONSE

```
{
  ".ds-metrics-custom-labenv-2024.12.10-000002": {
    "mappings": {
      "_data_stream_timestamp": {
        "enabled": true
      },
      "properties": {
        "@timestamp": {
          "type": "date"
        },
        "data_stream": {
          "properties": {
            "type": {
              "type": "constant_keyword",
              "value": "metrics"
            }
          }
        },
        "message": {
          "type": "text"
        },
        "status": {
          "type": "keyword"
        }
      }
    }
  },
  ".ds-metrics-custom-labenv-2024.12.10-000001": {
    "mappings": {
      "_data_stream_timestamp": {
        "enabled": true
      },
      "properties": {
        "@timestamp": {
          "type": "date"
        },
        "message": {
          "type": "text"
        },
        "status": {
          "type": "keyword"
        }
      }
    }
  }
}
```
INFO: Future backing indices will have a blank value, which will also be set by the first document indexed to that backing index.
This is not the behavior we want, as all backing indices for this data stream should have the same value already set.
Next, you will update the time-series-mappings component template again, this time to add metrics as the default value for the data_stream.type field.

## h) From Index Management, open the Component Templates tab.

Update the time-series-mappings component template to add metrics as the default value for the data_stream.type field.

<img width="517" height="384" alt="image" src="https://github.com/user-attachments/assets/60643c36-759f-4f08-aa90-a715b0c93a2e" />

TIP: You can also solve this task by running the following command in Console:

REQUEST

```
PUT _component_template/time-series-mappings
{
  "template": {
    "mappings": {
      "properties": {
        "@timestamp": {
          "type": "date"
        },
        "message": {
          "type": "text"
        },
        "status": {
          "type": "keyword"
        },
        "data_stream.type": {
          "type": "constant_keyword",
          "value": "metrics"
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

## i) Roll Over

In Console, manually roll over the data stream.

REQUEST

```
POST metrics-custom-labenv/_rollover/
```

RESPONSE

```
{
  "acknowledged": true,
  "shards_acknowledged": true,
  "old_index": ".ds-metrics-custom-labenv-2024.12.10-000002",
  "new_index": ".ds-metrics-custom-labenv-2024.12.10-000003",
  "rolled_over": true,
  "dry_run": false,
  "lazy": false,
  "conditions": {}
}
```

## j) Run the following command to index a document into the data stream:

REQUEST

```
POST metrics-custom-labenv/_doc
{
  "@timestamp": "2021-07-06",
  "status": "UP",
  "message": "Service is running."
}
```

RESPONSE

```
{
  "_index": ".ds-metrics-custom-labenv-2024.12.10-000003",
  "_id": "h7g5spMBIOLfbEt29TwQ",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 3,
    "successful": 3,
    "failed": 0
  },
  "_seq_no": 0,
  "_primary_term": 1
}
```

## k) Search Request

Perform the following search request on the data stream:

REQUEST

```
GET metrics-custom-labenv/_search
```

RESPONSE

```
{
  "took": 20,
  "timed_out": false,
  "_shards": {
    "total": 3,
    "successful": 3,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 3,
      "relation": "eq"
    },
    "max_score": 1,
    "hits": [
      {⇔},
      {⇔},
      {⇔}
    ]
  }
}
```

## l) Search

Search for the documents where data_stream.type is equal to metrics.

REQUEST

```
GET metrics-custom-labenv/_search
{
  "query": {
    "match": {
      "data_stream.type" : "metrics"
    }
  }
}
```

RESPONSE

```
{
  "took": 19,
  "timed_out": false,
  "_shards": {
    "total": 3,
    "successful": 3,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 2,
      "relation": "eq"
    },
    "max_score": 1,
    "hits": [
      {⇔},
      {⇔}
    ]
  }
}
```

Next you will explore how the Elastic Agent integrations automatically create data streams.

<img width="1915" height="814" alt="image" src="https://github.com/user-attachments/assets/8829be30-a8fd-402c-95dd-2bf683e22ab1" />

In Console, run the following command to inspect the data stream that has been automatically created by the integration:

REQUEST

```
GET _data_stream/metrics-system.memory-labenv
```

RESPONSE

```
{
  "data_streams": [
    {
      "name": "metrics-system.memory-labenv",
      "timestamp_field": {
        "name": "@timestamp"
      },
      "indices": [
        {
          "index_name": ".ds-metrics-system.memory-labenv-2024.12.11-000001",
          "index_uuid": "iC9jjcE_S7-z6TigzrOvgw",
          "prefer_ilm": true,
          "ilm_policy": "metrics",
          "managed_by": "Index Lifecycle Management"
        }
      ],
      "generation": 1,
      "_meta": {
        "managed_by": "fleet",
        "managed": true,
        "package": {
          "name": "system"
        }
      },
      "status": "YELLOW",
      "template": "metrics-system.memory",
      "ilm_policy": "metrics",
      "next_generation_managed_by": "Index Lifecycle Management",
      "prefer_ilm": true,
      "hidden": false,
      "system": false,
      "allow_custom_routing": false,
      "replicated": false,
      "rollover_on_write": false,
      "time_series": {
        "temporal_ranges": [
          {
            "start": "2024-12-11T20:38:25.000Z",
            "end": "2024-12-11T23:08:25.000Z"
          }
        ]
      }
    }
  ]
}
```

# Summary

In this lab, you learned about data streams by performing the following tasks:

    Clone an index template and add a data stream
    Update a component template to add a data stream field
    Roll over the data stream and index a document into the data stream
    View the mappings for the backing indices
    Add an integration to automatically create a data stream




# Review

- When _bulk writing to a data stream, you should use the create action. Data streams are designed to only create, never update.
- Index template to use data streams:
    - Include the data_stream object in the create index template API
    - Set “create data stream” to true in the Index Management UI
NOTE: Either of the first two will work since the data_stream object is really a placeholder object and does not require any additional configuration to create data streams.
- Changes should be performed to the Index Template of a data stream’s configuration.
NOTE: Always make changes to your data stream via the index template.  This ensures that new backing indices will inherit the changes as well.

# 7.1: Data management concepts

In this lesson, you’ll learn about tools to help manage your time-series data.

In this lab, you will learn about index aliases by performing the following tasks:

    Create a component template
    Create an index template which uses the component template
    Create an index configured to be the write index
    Perform a rollover

## a) Create a Component Template

Using Kibana, you will create a component template that satisfies the following requirements:

    The name is time-series-mappings
    There are three mapped fields defined:
        @timestamp of type Date
        status of type Keyword
        message of type Text

From Index Management, open the Component Templates tab.
<img width="487" height="360" alt="image" src="https://github.com/user-attachments/assets/eddf4366-a25b-4699-94d8-3b7edd3b9a3b" />

On the Mappings page, add the following mappings:
<img width="496" height="358" alt="image" src="https://github.com/user-attachments/assets/cedaccf5-2982-4be7-9fa2-bd19e4ddcd6d" />

On the Review page, click Create component template.
<img width="476" height="363" alt="image" src="https://github.com/user-attachments/assets/142d0e1f-f08c-4561-8d96-c39303bf3741" />

Alternatively, you can run the following command in Console:

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
        "status": {
          "type": "keyword"
        },
        "message": {
          "type": "text"
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

## b) Create a New Index Template

Next, you will create a new index template that satisfies the following requirements:

    The name is my-metrics-template
    The index pattern matches any index that starts with my-metrics-
    The priority is set to 500
    The index template uses the time-series-mappings component template

Open the Index Templates tab.
<img width="473" height="352" alt="image" src="https://github.com/user-attachments/assets/1841adcd-14db-440d-8327-cfa260453ff3" />

On the Logistics page configure the following settings:

    Name your template my-metrics-template
    Set the index pattern to my-metrics-*
    Toggle the Data stream button to create indices instead of data streams
    Set priority to 500
    Leave everything else as default
    Click Next
<img width="853" height="645" alt="image" src="https://github.com/user-attachments/assets/3cb85105-4685-4758-8074-2ba15d678b97" />

On the Component Templates page, add the time-series-mappings component template you created by clicking the + symbol next to it.
<img width="387" height="299" alt="image" src="https://github.com/user-attachments/assets/a4505ffa-546e-4295-951b-be88bd8a28c0" />

Click Next on each page until the Review Template page, then click Create template.
<img width="614" height="458" alt="image" src="https://github.com/user-attachments/assets/6012fdac-f1c4-4072-88fa-59cf2462f124" />

Alternatively, you can run the following command in Console:

REQUEST

```
PUT _index_template/my-metrics-template
{
  "priority": 500,
  "index_patterns": [
    "my-metrics-*"
  ],
  "composed_of": [
    "time-series-mappings"
  ]
}
```

RESPONSE

```
{
  "acknowledged": true
}
```

In Console, run the following Simulate Index API command to confirm your template is appropriately defined:

REQUEST

```
POST _index_template/_simulate_index/my-metrics-test
```

RESPONSE

```
{
  "template": {
    "settings": {
      "index": {
        "routing": {
          "allocation": {
            "include": {
              "_tier_preference": "data_content"
            }
          }
        }
      }
    },
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
        }
      }
    },
    "aliases": {}
  },
  "overlapping": []
}
```

## c) Create a second component template named time-series-settings with 2 replicas.

If using the UI, name the template and add the number_of_replicas value to the index settings page of the component template wizard: 

```
{
  "number_of_replicas": 2
}
```
<img width="500" height="369" alt="image" src="https://github.com/user-attachments/assets/4f3c49cc-5013-433b-ba14-509c8809636a" />

Here is the Console command to create the second component template:

REQUEST

```
PUT _component_template/time-series-settings
{
  "template": {
    "settings": {
      "index": {
        "number_of_replicas": "2"
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

## d) Update Index Template

Update the my-metrics-template to also use your new time-series-settings component.

If using the UI, click to Edit the template, and add the time-series-settings component on the Compontent templates page.
<img width="311" height="485" alt="image" src="https://github.com/user-attachments/assets/42f9ef6b-c07d-4dae-965b-6433d05f634a" />

If using Console:

REQUEST

```
PUT _index_template/my-metrics-template
{
  "priority": 500,
  "index_patterns": [
    "my-metrics-*"
  ],
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

## e) Simulate Index API

Test that your new template settings have been applied by rerunning the Simulate Index API command.

REQUEST

```
POST _index_template/_simulate_index/my-metrics-test
```

RESPONSE

```
{
  "template": {
    "settings": {
      "index": {
        "number_of_replicas": "2",
        "routing": {
          "allocation": {
            "include": {
              "_tier_preference": "data_content"
            }
          }
        }
      }
    },
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
        }
      }
    },
    "aliases": {}
  },
  "overlapping": []
}
```


## f) Create Index to be the write index for my-metrics alias

Create a new index named my-metrics-000001 configured to be the write index for the my-metrics alias. 

REQUEST

```
PUT my-metrics-000001
{
  "aliases": {
    "my-metrics": {
      "is_write_index": true
    }
  }
}
```

RESPONSE

```
{
  "acknowledged": true,
  "shards_acknowledged": true,
  "index": "my-metrics-000001"
}
```

## g) View Index Details

View the details of your new index and verify it has all the correct settings from the index template.

REQUEST

```
GET my-metrics-000001
```

RESPONSE

```
{
  "my-metrics-000001": {
    "aliases": {
      "my-metrics": {
        "is_write_index": true
      }
    },
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
        }
      }
    },
    "settings": {
      "index": {
        "routing": {
          "allocation": {
            "include": {
              "_tier_preference": "data_content"
            }
          }
        },
        "number_of_shards": "1",
        "provided_name": "my-metrics-000001",
        "creation_date": "1733518627130",
        "number_of_replicas": "2",
        "uuid": "BWudUoHpSmGLyznIxs-w6w",
        "version": {
          "created": "8512000"
        }
      }
    }
  }
}
```

## h) Rollover API

Call the rollover API with the my-metrics alias and set a maximum age of 2 seconds.

REQUEST

```
POST my-metrics/_rollover
{
  "conditions": {
    "max_age": "2s"
  }
}
```

RESPONSE

```
{
  "acknowledged": true,
  "shards_acknowledged": true,
  "old_index": "my-metrics-000001",
  "new_index": "my-metrics-000002",
  "rolled_over": true,
  "dry_run": false,
  "lazy": false,
  "conditions": {
    "[max_age: 2s]": true
  }
}
```

## i) View Index Details

View the details of the indices and verify the changes.
Confirm that now there are two indices in the alias, and that the new my-metrics-000002 index is the write index.

REQUEST 1

```
GET my-metrics-000001
```

RESPONSE 1

```
{
  "my-metrics-000001": {
    "aliases": {
      "my-metrics": {
        "is_write_index": false
      }
    },
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
        }
      }
    },
    "settings": {
      "index": {
        "routing": {
          "allocation": {
            "include": {
              "_tier_preference": "data_content"
            }
          }
        },
        "number_of_shards": "1",
        "provided_name": "my-metrics-000001",
        "creation_date": "1733773038847",
        "number_of_replicas": "2",
        "uuid": "6uMlG7mIQPiyUSNLqNLWzw",
        "version": {
          "created": "8512000"
        }
      }
    }
  }
}
```

REQUEST 2

```
GET my-metrics-000002
```

REQUEST 2

```
{
  "my-metrics-000002": {
    "aliases": {
      "my-metrics": {
        "is_write_index": true
      }
    },
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
        }
      }
    },
    "settings": {
      "index": {
        "routing": {
          "allocation": {
            "include": {
              "_tier_preference": "data_content"
            }
          }
        },
        "number_of_shards": "1",
        "provided_name": "my-metrics-000002",
        "creation_date": "1733773075928",
        "number_of_replicas": "2",
        "uuid": "iPCgHoY8TMCnCuGPy47SWg",
        "version": {
          "created": "8512000"
        }
      }
    }
  }
}
```

# Summary

In this lab, you learned about index aliases by performing the following tasks:

    Create a component template
    Create an index template which uses the component template
    Create an index configured to be the write index
    Perform a rollover

# Review

- It’s a good idea, but not required, to use aliases for all of your production indices.
- If more than one template matches an index pattern, only one template will be applied. Use a “priority” to resolve conflicts.

# 7.3: Index lifecycle management

In this lesson, you’ll learn all about using the Index Lifecycle Management tools, including how to create a policy, and defining various actions to take as your index moves through the various policy phases.

In this lab, you will learn about index lifecycle policies by performing the following tasks:

    Create an index lifecycle policy
    Apply an index lifecycle policy to a data stream
    Manage the indices for time-series data


## a) Within Kibana2, go to Stack Management > Index Lifecycle Policies.

Next, you will create a new policy that satisfies the following requirements:

    The name of the policy is metrics-custom-policy
    The index is in the hot phase for 2 minutes
    The index immediately moves to the warm phase and makes the index read-only when it rolls over
    The index moves to the cold phase and the number of replicas is set to 0 after 2 minutes in the warm phase
    The index is deleted 5 minutes after rolling over

## b) For the Hot phase, set the Rollover Maximum age to 2 minutes

Hot Phase Settings

    Expand the Advanced settings
    Toggle off the Use recommended defaults switch
    Delete the value for maximum size
    Set the Maximum age to 2 minutes
    Leave the remaining defaults

<img width="587" height="450" alt="image" src="https://github.com/user-attachments/assets/a2d9f442-6295-4753-95cd-61891f3d5075" />

## c) Activate the Warm phase and make the index read-only. Leave the Move data into phase when set to 0, and leave the rest of the values as defaults.

<img width="547" height="401" alt="image" src="https://github.com/user-attachments/assets/e67e6eb1-3270-4c08-8db7-66f5fea2b8a6" />

## d) Activate the Cold phase and configure the following settings:

    Set the Move data into phase when to 2 minutes
    Expand the Advanced settings
    Set the Number of replicas to 0
    Change the Keep data in this phase forever to Delete data after this phase
    Leave the remaining defaults

<img width="639" height="480" alt="image" src="https://github.com/user-attachments/assets/25416d89-c55c-4ac5-9565-30bbe93f74a2" />

## e) In the Delete phase, change Move data into phase when to 5 minutes. You can also click Show request to verify the policy settings. Click to Save Policy.

<img width="701" height="532" alt="image" src="https://github.com/user-attachments/assets/f52fb464-3f88-4c35-9a22-0b37b1ef6550" />


## f) You can also click Show request to verify the policy settings and then click to Save the request.

TIP: The policy Request should look like the following: 

```
PUT _ilm/policy/metrics-custom-policy
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "rollover": {
            "max_age": "2m"
          },
          "set_priority": {
            "priority": 100
          }
        },
        "min_age": "0ms"
      },
      "warm": {
        "min_age": "0d",
        "actions": {
          "set_priority": {
            "priority": 50
          },
          "readonly": {}
        }
      },
      "cold": {
        "min_age": "2m",
        "actions": {
          "set_priority": {
            "priority": 0
          },
          "allocate": {
            "number_of_replicas": 0
          }
        }
      },
      "delete": {
        "min_age": "5m",
        "actions": {
          "delete": {}
        }
      }
    }
  }
}
```

## g) In Console, run the following command, which sets the poll interval for lifecycle policies to 30 seconds:

REQUEST

```
PUT _cluster/settings
{
  "persistent": {
    "indices.lifecycle.poll_interval": "30s"
  }
}
```

RESPONSE

```
{
  "acknowledged": true,
  "persistent": {
    "indices": {
      "lifecycle": {
        "poll_interval": "30s"
      }
    }
  },
  "transient": {}
}
```
NOTE: A poll interval of 30 seconds is far too low for production. The default of 10 minutes is generally acceptable.

## h) Use the Console to create a new Index Template with the following requirements:

    The name is memory-ds-template
    The priority is set to 500
    It applies the new ILM policy
    The number of replicas is set to 0
    It matches the data stream metrics-system.memory-labenv

## i) Create Index Template

REQUEST

```
PUT _index_template/memory-ds-template
{
  "priority": 500,
  "template": {
    "settings": {
      "number_of_replicas": 0,
      "index.lifecycle.name": "metrics-custom-policy"
    }
  },
  "data_stream": {},
  "index_patterns": [
    "metrics-system.memory-labenv"
  ]
}
```

RESPONSE

```
{
  "acknowledged": true
}
```

## j) Delete Data Stream

Delete the metrics-system.memory-labenv data stream from the previous lab.

REQUEST

```
DELETE _data_stream/metrics-system.memory-labenv
```

RESPONSE

```
{
  "acknowledged": true
}
```
NOTE: As the integration is continuously sending data to this data stream, it should be automatically recreated.

## k) Display Settings

Display the settings of your data stream to check if you correctly set up ILM.

REQUEST

```
GET metrics-system.memory-labenv/_settings
```

RESPONSE

```
{
  ".ds-metrics-system.memory-labenv-2024.12.12-000001": {
    "settings": {
      "index": {
        "lifecycle": {
          "name": "metrics-custom-policy"
        },
        "routing": {
          "allocation": {
            "include": {
              "_tier_preference": "data_hot"
            }
          }
        },
        "hidden": "true",
        "number_of_shards": "1",
        "provided_name": ".ds-metrics-system.memory-labenv-2024.12.12-000001",
        "creation_date": "1733966495440",
        "priority": "100",
        "number_of_replicas": "0",
        "uuid": "dqGIpOgMR0KzJYYOYuJbQQ",
        "version": {
          "created": "8512000"
        }
      }
    }
  }
}
```
NOTE: You should see that the metrics-custom-policy policy manages the backing indices. The first index should be in the hot phase. 

## l) Run the following command to see how the indices are progressing:

REQUEST

```
GET metrics-system.memory-labenv/_ilm/explain
```

RESPONSE

```
{
  "indices": {
    ".ds-metrics-system.memory-labenv-2024.12.12-000001": {
      "index": ".ds-metrics-system.memory-labenv-2024.12.12-000001",
      "managed": true,
      "policy": "metrics-custom-policy",
      "index_creation_date_millis": 1733966495440,
      "time_since_index_creation": "4.78m",
      "lifecycle_date_millis": 1733966617079,
      "age": "2.76m",
      "phase": "cold",
      "phase_time_millis": 1733966737141,
      "action": "complete",
      "action_time_millis": 1733966737341,
      "step": "complete",
      "step_time_millis": 1733966737341,
      "phase_execution": {
        "policy": "metrics-custom-policy",
        "phase_definition": {
          "min_age": "2m",
          "actions": {
            "allocate": {
              "number_of_replicas": 0,
              "include": {},
              "exclude": {},
              "require": {}
            },
            "set_priority": {
              "priority": 0
            }
          }
        },
        "version": 1,
        "modified_date_in_millis": 1733965756628
      }
    },
    ".ds-metrics-system.memory-labenv-2024.12.12-000002": {
      "index": ".ds-metrics-system.memory-labenv-2024.12.12-000002",
      "managed": true,
      "policy": "metrics-custom-policy",
      "index_creation_date_millis": 1733966617194,
      "time_since_index_creation": "2.75m",
      "lifecycle_date_millis": 1733966767157,
      "age": "15.55s",
      "phase": "warm",
      "phase_time_millis": 1733966767557,
      "action": "readonly",
      "action_time_millis": 1733966767557,
      "step": "check-ts-end-time-passed",
      "step_time_millis": 1733966767757,
      "phase_execution": {
        "policy": "metrics-custom-policy",
        "phase_definition": {
          "min_age": "0d",
          "actions": {
            "readonly": {},
            "set_priority": {
              "priority": 50
            }
          }
        },
        "version": 1,
        "modified_date_in_millis": 1733965756628
      }
    },
    ".ds-metrics-system.memory-labenv-2024.12.12-000003": {
      "index": ".ds-metrics-system.memory-labenv-2024.12.12-000003",
      "managed": true,
      "policy": "metrics-custom-policy",
      "index_creation_date_millis": 1733966767195,
      "time_since_index_creation": "15.51s",
      "lifecycle_date_millis": 1733966767195,
      "age": "15.51s",
      "phase": "hot",
      "phase_time_millis": 1733966767357,
      "action": "rollover",
      "action_time_millis": 1733966767557,
      "step": "check-rollover-ready",
      "step_time_millis": 1733966767557,
      "phase_execution": {
        "policy": "metrics-custom-policy",
        "phase_definition": {
          "min_age": "0ms",
          "actions": {
            "set_priority": {
              "priority": 100
            },
            "rollover": {
              "max_age": "2m",
              "min_docs": 1,
              "max_primary_shard_docs": 200000000
            }
          }
        },
        "version": 1,
        "modified_date_in_millis": 1733965756628
      }
    }
  }
}
```
NOTE: Over the next 5 minutes, you should see indices being created, moving through the phases, and being deleted. 

# Summary

In this lab, you learned about index lifecycle policies by performing the following tasks:

    Create an index lifecycle policy
    Apply an index lifecycle policy to a data stream
    Manage the indices for time-series data

# Review

- When a hot index rolls over, write requests are automatically sent to the newly created backing index.
- You have 5 phases in ILM: Hot/Warm/Cold/Frozen/Delete.
- If you want an index in the warm phase for 5 days, then have it move to the cold phase, what would you set min_age to in the cold phase? 5 days. To execute this pattern, you’d set rollover in hot to 1 day, and then set the min_age in the cold phase to 5 days.


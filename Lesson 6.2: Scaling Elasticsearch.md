# 6.2: Scaling Elasticsearch

In this lesson, you’ll learn how shards help facilitate scaling your Elasticsearch cluster effectively. You will also learn how to scale your cluster for write and read operations.


In this lab, you will learn more about scaling by performing the following tasks:

    Allocate shards to optimize write throughput during an initial load
    Configure replicas to optimize reads and availability of the index
    Scale up your cluster by adding one node

TIP: Suppose you want to index many documents into a newly-created index. You can improve write speed by disabling replicas.
WARNING: Disabling replicas is typically risky, but is fine for this scenario or if you have the data backed up somewhere else.

## a) Create Index

Create a new index that satisfies the following requirements:

    The name of the index is temp1
    The index has four primary shards
    The index has zero replica shards
    Refreshing is disabled on the index

REQUEST

```
PUT temp1
{
  "settings": {
    "number_of_shards": 4,
    "number_of_replicas": 0,
    "refresh_interval": -1
  }
}
```

RESPONSE

```
{
  "acknowledged": true,
  "shards_acknowledged": true,
  "index": "temp1"
}
```

## b) Review the shard allocation by using _cat

Before loading data into temp1, run the following _cat command to review its shard allocation:

REQUEST

```
GET _cat/shards/temp1?v&h=index,shard,prirep,state,node&s=index,shard,prirep
```

RESPONSE

```
index shard prirep state   node
temp1 0     p      STARTED node3
temp1 1     p      STARTED node2
temp1 2     p      STARTED node1
temp1 3     p      STARTED node3
```
NOTE: Notice that two primary shards are on the same node. 

## c) Scale up your cluster

TERMINAL

Add one additional node to your cluster by running the following command in your terminal:

```
docker start node4
```

Wait a few seconds for your node to join the cluster, then run a command in Console to check whether the new node joined the cluster.

REQUEST

```
GET _cat/nodes?v
```

RESPONSE

```
ip          heap.percent ram.percent cpu load_1m load_5m load_15m node.role   master name
172.19.0.2            28          86  15    0.41    0.67     1.18 cdfhilmrstw *      node3
172.19.0.4            66          86  15    0.41    0.67     1.18 cdfhilmrstw -      node2
172.19.0.3            26          88  15    0.41    0.67     1.18 cdfhilmrstw -      node1
172.19.0.10           42          82  15    0.41    0.67     1.18 cdfhilmrstw -      node4
```

## d) Shard Allocation

Review the shard allocation of the temp1 index.

REQUEST

```
GET _cat/shards/temp1?v&h=index,shard,prirep,state,node&s=index,shard,prirep
```

RESPONSE

```
index shard prirep state   node
temp1 0     p      STARTED node2
temp1 1     p      STARTED node1
temp1 2     p      STARTED node3
temp1 3     p      STARTED node4
```
NOTE: One of the primary shards has been reallocated. Your cluster is now ready!

## e) Reindex

Using the Reindex API, reindex the documents from web_traffic_fixed into temp1 where http.response.status_code equals 404.

REQUEST

```
POST _reindex
{
  "source": {
    "index": "web_traffic_fixed",
    "query": {
      "match": {
        "http.response.status_code": "404"
      }
    }
  },
  "dest": {
    "index": "temp1"
  }
}
```

RESPONSE

```
{
  "took": 4999,
  "timed_out": false,
  "total": 52840,
  "updated": 0,
  "created": 52840,
  "deleted": 0,
  "batches": 53,
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
NOTE: It should take about 10 seconds for the reindex to execute. In this case, 52,840 documents have an http.response.status_code equal to 404.

## f) Count

Count how many documents ended up in temp1. What is the result and why?

REQUEST

```
GET temp1/_count
```

RESPONSE

```
{
  "count": 0,
  "_shards": {
    "total": 4,
    "successful": 4,
    "skipped": 0,
    "failed": 0
  }
}
```
NOTE: The count is 0 because you disabled refresh by setting index.refresh_interval to -1, since you needed to load a large amount of data at once. You need to perform a refresh before the documents are searchable.

## g) Refresh

To make your documents searchable, you can manually trigger a refresh for the temp1 index:

REQUEST

```
POST temp1/_refresh
```

RESPONSE

```
{
  "_shards": {
    "total": 4,
    "successful": 4,
    "failed": 0
  }
}
```

## h) Count

Now, recheck the count.

REQUEST

```
GET temp1/_count
```

RESPONSE

```
{
  "count": 52840,
  "_shards": {
    "total": 4,
    "successful": 4,
    "skipped": 0,
    "failed": 0
  }
}
```
NOTE: You should have 52,840 documents in temp1 now. 

## i) Enable Refresh

Now that you finished your initial load, enable refresh on temp1 by setting the refresh interval to the default value of 1 second.

REQUEST

```
PUT temp1/_settings
{
  "index.refresh_interval": "1s"
}
```

RESPONSE

```
{
  "acknowledged": true
}
```
NOTE: You can also set it to null to restore the default value.


## j) Add Replicas to the Index

Now that we are done with the initial data loading into temp1, it is good to add some replicas to the index. Having a replica on each node increases read throughput.

Run the following command, which auto-expands the number of replicas based on the number of data nodes in the cluster:

REQUEST

```
PUT temp1/_settings
{
  "index.auto_expand_replicas": "0-all"
}
```

RESPONSE

```
{
  "acknowledged": true
}
```
NOTE: The auto-expanded number of replicas does not take any other allocation rules into account, such as shard allocation awareness, or total shards per node, which can lead to the cluster health from becoming yellow if the applicable rules prevent all the replicas from being allocated.

## k) Review Shard Allocation by running _cat command

Run the following _cat command to review temp1 shard allocation. How many replica shards do you have after enabling auto-expanded replicas?

REQUEST

```
GET _cat/shards/temp1?v&h=index,shard,prirep,state,node&s=index,shard,prirep
```

RESPONSE

```
index shard prirep state   node
temp1 0     p      STARTED node2
temp1 0     r      STARTED node4
temp1 0     r      STARTED node3
temp1 0     r      STARTED node1
temp1 1     p      STARTED node1
temp1 1     r      STARTED node4
temp1 1     r      STARTED node3
temp1 1     r      STARTED node2
temp1 2     p      STARTED node3
temp1 2     r      STARTED node4
temp1 2     r      STARTED node1
temp1 2     r      STARTED node2
temp1 3     p      STARTED node4
temp1 3     r      STARTED node3
temp1 3     r      STARTED node1
temp1 3     r      STARTED node2
```
NOTE: You should see 16 shards now, which means three replicas were created from each of the four initial primary shards. 

## l) Remove the last added node from the cluster

Now remove node4 from the cluster by running this command in the terminal:

TERMINAL

```
docker stop node4
```

## m) Review Shard Allocation by running _cat command again

Run the following _cat command in Console to review temp1 shard allocation.

REQUEST

```
GET _cat/shards/temp1?v&h=index,shard,prirep,state,node&s=index,shard,prirep
```

RESPONSE

```
index shard prirep state   node
temp1 0     p      STARTED node2
temp1 0     r      STARTED node3
temp1 0     r      STARTED node1
temp1 1     p      STARTED node1
temp1 1     r      STARTED node3
temp1 1     r      STARTED node2
temp1 2     p      STARTED node3
temp1 2     r      STARTED node2
temp1 2     r      STARTED node1
temp1 3     p      STARTED node3
temp1 3     r      STARTED node2
temp1 3     r      STARTED node1
```
NOTE: Even if one node is down, a replica has been promoted to primary. You should have all the primaries available, and the number of replicas has been automatically reduced.

## n) Delete Index

You are done examining shards and replicas, so delete the temp1 index.

REQUEST

```
DELETE temp1
```

RESPONSE

```
{
  "acknowledged": true
}
```


# Summary

In this lab, you learned more about scaling by performing the following tasks:

    Allocate shards to optimize write throughput during an initial load
    Configure replicas to optimize reads and availability of the index
    Scale up your cluster by adding one node 

# Review

- If you’re planning to eventually scale up to distribute work, additional primaries are useful.
- Read throughput scales with the number of replicas. Additional primaries may actually slow reads, though it does improve writes.
- Use auto-generated document IDs so Elasticsearch does not have to check whether an ID already exists.

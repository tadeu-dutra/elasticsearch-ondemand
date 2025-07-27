# 6.1. Understanding Shards

In this lesson, you’ll learn how Elasticsearch manages the documents in an index using shards.  Shards allow you to replicate your data for high availability and distribute your documents evenly across your cluster.

In this lab, you will become familiar with shard distribution by performing the following tasks:

    Use the Cat Nodes API, CAT Indices API, and Cat Shards API to return information about the cluster
    Create a new index with a specific number of shards and replicas to analyze shard allocation

## a) Cat Nodes API

Use the Cat Nodes API to return information about the nodes in the cluster. How many nodes are in your cluster?

REQUEST

```
GET _cat/nodes?v
```

RESPONSE

```
Your cluster should have three nodes.
ip         heap.percent ram.percent cpu load_1m load_5m load_15m node.role   master name
172.19.0.2           39          94   3    0.30    0.20     0.19 cdfhilmrstw *      node3
172.19.0.5           52          97   3    0.30    0.20     0.19 cdfhilmrstw -      node2
172.19.0.4           52          95   3    0.30    0.20     0.19 cdfhilmrstw -      node1
```

## b) Cat Indices API

Use the Cat Indices API to return information about the indices in the cluster.

REQUEST

```
GET _cat/indices?v
```

RESPONSE

```
health status index                                                              uuid                   pri rep docs.count docs.deleted store.size pri.store.size dataset.size
green  open   .internal.alerts-observability.logs.alerts-default-000001          gRVgdRZ_QBCdm6A1xJTHTQ   1   1          0            0       498b           249b         249b
green  open   categories                                                         zxJzU2PrQYGjphBaLf_S_w   1   1          7            0     13.1kb          6.5kb        6.5kb
green  open   web_traffic                                                        ehwg216sQhGkiQd6b4yvGw   1   1    1324128            0    620.1mb          310mb        310mb
green  open   .internal.alerts-observability.slo.alerts-default-000001           WiTqprm9R2yBa9U12pu8kQ   1   1          0            0       498b           249b         249b
green  open   .internal.alerts-stack.alerts-default-000001                       80mnal75Q6mMYAafQu_Sjw   1   1          0            0       498b           249b         249b
green  open   .internal.alerts-observability.apm.alerts-default-000001           kaqyWlLGSFivbukDezOCMQ   1   1          0            0       498b           249b         249b
green  open   .internal.alerts-ml.anomaly-detection-health.alerts-default-000001 -C4VRAzRQUSSgBceqn3UjQ   1   1          0            0       498b           249b         249b
green  open   traffic_stats                                                      Kytc7jy8RC6lmnmLLypyig   1   1       3687            0      1.3mb        675.9kb      675.9kb
green  open   .kibana-observability-ai-assistant-kb-000001                       bOkxTJ0bR5WqvcuoJKmx9w   1   1          0            0       498b           249b         249b
green  open   web_traffic_fixed                                                  XdlBdO6nT52Oo4hRKL8CYg   1   1    1324128            0    344.4mb          171mb        171mb
green  open   .internal.alerts-observability.threshold.alerts-default-000001     4Ik_AEgERsuQ1HLXW0wdwQ   1   1          0            0       498b           249b         249b
green  open   .kibana-observability-ai-assistant-conversations-000001            IHPFV-bbRPS8xZF6vViCuw   1   1          0            0       498b           249b         249b
green  open   .internal.alerts-security.alerts-default-000001                    i2Jg08XBQLWYU46-as-Pvg   1   1          0            0       498b           249b         249b
green  open   .internal.alerts-observability.uptime.alerts-default-000001        HJR0_Kx9Q2iE62R5wZezeg   1   1          0            0       498b           249b         249b
green  open   .internal.alerts-default.alerts-default-000001                     0y0Md8ocQ0SL8OSDhnklrg   1   1          0            0       498b           249b         249b
green  open   .internal.alerts-ml.anomaly-detection.alerts-default-000001        MtGVLn4LStq6U-9bIQkmkw   1   1          0            0       498b           249b         249b
green  open   .internal.alerts-observability.metrics.alerts-default-000001       ryNjkieuQJKT7csvFp5jNQ   1   1          0            0       498b           249b         249b
green  open   blogs_fixed                                                        YtmmiNapRSaxCyS7TyRO0Q   1   1       3687            0     53.3mb         26.6mb       26.6mb
green  open   blogs_fixed2                                                       xH9wRHj3TcWtTKJsZTBRvQ   1   1       3500          500     48.2mb         25.8mb       25.8mb
green  open   blogs                                                              h2fO15MUQr6DIOnXZna-sA   1   1       3687            0     54.6mb         27.3mb       27.3mb
green  open   kibana_sample_data_ecommerce                                       HrDaZryoTxmtUTx7JaySig   1   1       4675            0      8.5mb          4.2mb        4.2mb
green  open   .internal.alerts-transform.health.alerts-default-000001            OtL6FfhBQ-OC0MY60zcYdQ   1   1          0            0       498b           249b         249b
```
NOTE: Notice the following are shown:

    The number of primary shards, replica shards, documents and deleted documents
    The size on disk for primary shards and total (primary shards + replica shards)
    Every index's name and UUID 

## c) Cat Shards API

Use the Cat Shards API to return information about the shards in the cluster. How many shards does the index web_traffic have, and in which node are they allocated?

REQUEST

```
GET _cat/shards?v
```

RESPONSE

```
index                                                              shard prirep state      docs   store dataset ip         node
...
web_traffic                                                        0     r      STARTED 1324128   310mb   310mb 172.19.0.5 node2
web_traffic                                                        0     p      STARTED 1324128   310mb   310mb 172.19.0.4 node1
...
```
NOTE: The web_traffic index has two shards located in nodes 1 and 2.
The Cat Shards API shows the number of documents, the size on disk, and which nodes contain which shards. 

## d) Cat Shards API (View the shard details)

View the shard details for specifically just the web_traffic index by adding the index name after /shards in the _cat command.

REQUEST

```
GET _cat/shards/web_traffic?v
```

RESPONSE

```
index       shard prirep state      docs store dataset ip         node
web_traffic 0     r      STARTED 1324128 310mb   310mb 172.19.0.5 node2
web_traffic 0     p      STARTED 1324128 310mb   310mb 172.19.0.4 node1
```

## e) New Index

To better understand shard allocation, create a new index named test1 with two primary shards and three replicas. How many shards will be needed for test1 on the cluster?

REQUEST

```
PUT test1
{
  "settings": {
    "number_of_shards": 2,
    "number_of_replicas": 3
  }
}
```

RESPONSE

```
{
  "acknowledged": true,
  "shards_acknowledged": true,
  "index": "test1"
}
```
NOTE: The cluster will need eight shards: two primary and six replica shards. 

## f) Shard Allocation

View the shard allocation of your test1 index. How many primary and replica shards were started on the cluster? Why are there two unassigned shards?

REQUEST

```
GET _cat/shards/test1?v
```

RESPONSE

```
index shard prirep state      docs store dataset ip         node
test1 0     p      STARTED       0  227b    227b 172.19.0.2 node3
test1 0     r      STARTED       0  227b    227b 172.19.0.5 node2
test1 0     r      STARTED       0  227b    227b 172.19.0.4 node1
test1 0     r      UNASSIGNED                               
test1 1     r      STARTED       0  227b    227b 172.19.0.2 node3
test1 1     p      STARTED       0  227b    227b 172.19.0.5 node2
test1 1     r      STARTED       0  227b    227b 172.19.0.4 node1
test1 1     r      UNASSIGNED                               
```
NOTE: You should see:

    2 STARTED primary shards
    4 STARTED replica shards
    2 UNASSIGNED replica shards

You have a 3-node cluster, which provides nodes for one primary and two replicas. Any additional replicas will have to be unassigned until more nodes are added to the cluster. 

## g) Sorting shards details

Run the following command, which returns the same shard details but sorts them by shard first then by primary versus replica:

REQUEST

```
GET _cat/shards/test1?v&s=shard,prirep
```

RESPONSE

```
index shard prirep state      docs store dataset ip         node
test1 0     p      STARTED       0  227b    227b 172.19.0.2 node3
test1 0     r      STARTED       0  227b    227b 172.19.0.5 node2
test1 0     r      STARTED       0  227b    227b 172.19.0.4 node1
test1 0     r      UNASSIGNED                               
test1 1     p      STARTED       0  227b    227b 172.19.0.5 node2
test1 1     r      STARTED       0  227b    227b 172.19.0.2 node3
test1 1     r      STARTED       0  227b    227b 172.19.0.4 node1
test1 1     r      UNASSIGNED                               
```

## h) Update Index

Update the test1 index to have two replicas instead of 3.

REQUEST

```
PUT test1/_settings
{
    "number_of_replicas": 2
}
```

RESPONSE

```
{
  "acknowledged": true
}
```

## i) Check shard allocation, sorting by node then by shard

REQUEST

```
GET _cat/shards/test1?v&s=node,shard
```

RESPONSE

```
index shard prirep state   docs store dataset ip         node
test1 0     r      STARTED    0  249b    249b 172.19.0.4 node1
test1 1     r      STARTED    0  249b    249b 172.19.0.4 node1
test1 0     r      STARTED    0  249b    249b 172.19.0.5 node2
test1 1     p      STARTED    0  249b    249b 172.19.0.5 node2
test1 0     p      STARTED    0  249b    249b 172.19.0.2 node3
test1 1     r      STARTED    0  249b    249b 172.19.0.2 node3
```

## j) Delete Index

You are done experimenting with shards, so go ahead and delete the test1 index.

REQUEST

```
DELETE test1
```

RESPONSE

```
{
  "acknowledged": true
}
```




# Summary

In this lab, you became familiar with shard distribution by performing the following tasks:

    Use the Cat Nodes API, CAT Indices API, and Cat Shards API to return information about the cluster
    Create a new index with a specific number of shards and replicas to analyze shard allocation


# Review

- If number_of_shards for an index is 4, and number_of_replicas is 2, how many total shards will exist for this index? You get 12 shards. 4 primaries + 8 (4 x 2) replicas.
- Shards are optimally sized around 30-80GB.
- Elasticsearch automatically migrates shards to rebalance the cluster as the cluster grows. You don’t need to manually allocate the shards in your cluster.

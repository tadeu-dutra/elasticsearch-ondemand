# 6.3: Distributed operations

In this lesson, you’ll take a closer look at the internal operation inside Elasticsearch. You will learn how shards communicate with each other for write and search operations.

# Review

- The primary shard performs the operation first, then forwards the request in parallel to all the replicas.
- Any replica contains the same data as the primary. When searching for documents, Elasticsearch can ask for the primary or its replica depending on the availability.
- 
